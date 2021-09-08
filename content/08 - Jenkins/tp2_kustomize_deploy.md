---
title: TP2 - Déployer notre application dans plusieurs contextes avec `kustomize`
draft: false
---


## Reprendre le déploiement du TP3 kubernetes pour l'adapté un déploiement multienvironnement

Pour continuer, nous allons reprendre la correction du TP1 avec en plus le déploiement kubernetes du TP3 kubernetes.

- Pour cela, dans le projet `tp_jenkins_application`, commitez vos modifications puis lancez `git checkout jenkins_tp2_base`

- Ouvrez ensuite le projet `tp2_infra_et_app` dans VSCode.

## Déployer à partir d'un dépôt privé

Jusqu'ici nous avons poussé nos images docker sur le Docker Hub qui est gratuit et déjà configuré pour que docker récupère les image dessus par défaut. Généralement il est impossible d'utiliser ce repository public pour des logiciels d'une entreprise car cela révèle le fonctionnement et les failles des logiciels que vous y stocker.

De plus le hub Docker est souvent lent car surchargé.

Plus généralement nous aimerions avoir un répository privé et interne à notre cluster:
- pour la vitesse
- pour la sécurité

Pour avoir un répository privé il existe de nombreuses solutions. On peut mentionner:
- un compte commercial docker hub
- un compte commercial sur quay.io
- Utiliser gitlab
- Installer un repository avec `Harbor` (puissant mais lourd à installer et configurer)
- Installer un répository docker simple en https (idéalement avec un login par mot de passe ou autre)

Nous allons opter pour la dernière solution pour sa simplicité et sa versatilité.

- Chercher sur artifacthub le chart `docker-registry` de `twun`.

- Créez un fichier `docker-registry/values.yaml` pour configurer notre installation contenant:

```yaml
ingress:
  enabled: true
  path: /
  hosts:
    - registry.<votrenom>.vagrantk3s.dopl.uk
  tls:
    - hosts:
      - registry.<votrenom>.vagrantk3s.dopl.uk
      secretName: docker-registry-tls-cert
  annotations:
    kubernetes.io/ingress.class: "nginx"
    kubernetes.io/tls-acme: "true"
    cert-manager.io/cluster-issuer: acme-dns-issuer-prod
    nginx.ingress.kubernetes.io/proxy-body-size: "0" # important pour mettre une max body size illimitée pour nginx et pouvoir pousser des grosses images de plusieurs Gio
persistence:
  enabled: true
  size: 10Gi
service:
  port: 5000
  type: ClusterIP
replicaCount: 1
```
<!-- ```
secrets:
  htpasswd: <votre htpassword voir suite>
``` -->

- Complétez le nom de domaine avec votre nom.

<!-- - Nous allons générer un secret pour le mot de passe htpassword avec `docker run --rm --entrypoint htpasswd registry:2.6.2 -Bbn <votreuser> <votrepassword>`. Collez ce hash dans le values.yaml précédent

 (il faudrait idéalement créer le secret à la main ou ajouter un gestionnaire de secret ici mais il n'y a pas de solution simple). -->


- Modifiez le `helmfile` de `tp_jenkins_infra` pour ajouter la release du `docker-registry` (dans le namespace `docker-registry`) en vous inspirant des autres et de la doc artifacthub.

- Appliquez le helmfile comme dans le tp0.

- Ajoutez le nom de domaine `registry.<votrenom>.vagrantk3s.dopl.uk` aux deux fichiers `/etc/hosts` de votre machine hote et de votre machine vagrant k3s (pour que le cluster connaisse aussi le nom). 

- Connectez vous avec `docker login registry.<votrenom>.vagrantk3s.dopl.uk -u <votreuser> -p <votrepassword>`

- Poussez une image par exemple `python:3.9` en la tagguant avec l'adresse du dépot:
    - `docker tag registry.<votrenom>.vagrantk3s.dopl.uk/python:3.9 python:3.9`

## Faire varier une installation kubernetes

Nous avons besoin de pouvoir déployer notre application **monsterstack** dans Kubernetes de façon légèrement différente selon les environnements `prod` et `dev`.

### Créer les fichiers de base `kustomize`

Kustomize fonctionne en partant de fichiers ressource kubernetes de base et en écrasant certaines parties du fichiers avec de nouvelles valeurs appelées overlays.

- Ouvrez les fichiers dans `k8s/base`

- Les fichiers de base ne contienne que les information de base non spécifiques à un environnement.

- Quelques sont les paramètres qui doivent varier en fonction de l'environnement ?
  - la version de l'image utilisée !
  - les replicats
  - le port de monstericon (5000 en dev et 9090 en prod)
  - resource quota éventuellement
  - les noms des différentes resources

L'idée générale est de supprimer tous ces paramètres variables des fichiers de base pour les reporter dans un autre ensemble de fichier pour chaque environnement.

### Environnement de production : `prod`

Il contiendra une seule version de l'application avec des paramètres de production.

- Commitez vos modifications puis lancez `git checkout jenkins_tp2_correction`.

- Remplacez automatiquement toutes les instances de `<votrenom>` par votre nom avec la fonction de search and replace de VSCode.

- Observez les fichiers dans le dossier `overlays/prod`. Il contiennent les paramètres spécifiques de la production à ajouter par dessus les fichiers de base

- Depuis le dossier `k8s` lancez la commande `kubectl kustomize overlays/prod > result.yaml` puis observez ce fichier `resultprod.yaml`.

- Créez un namespace de prod pour le déploiement avec `kubectl create namespace prod`

- Supprimez le fichier précédent et appliquez la configuration de prod avec `kubectl apply -k overlays/prod -n prod`

### Environnement par défaut : `dev`

Il contiendra potentiellement plusieurs version de l'application dans le même namespace.

- Donc il faut idéalement que **chaque release ait un nom différent pour ses objets.
- Il faut également que la version de l'image puisse changer dynamiquement au moment du déploiement.

Cependant pour garder l'installation simple notre overlay de dev ne permettra d'installer qu'une seule version beta pour le moment.


Sinon le principe est un peu le même que pour la production seules les valeurs sont différentes : moins de replicat, le port de dev, une image beta et un nom de domaine beta.

- Depuis le dossier `k8s` vous pouvez lancer la commande `kubectl kustomize overlays/dev > result.yaml` pour observez le résultat dans le fichier `result.yaml`

- Puis déployer dans le namespace default: `kubectl apply -k overlays/dev -n default`

## Désinstaller et nettoyer

Pour désinstaller une release on peut simplement remplace `apply` par `delete` dans les commandes précédentes.

## Correction

Dans le dépot de corrections: 

- Commitez vos modifications puis lancez `git checkout jenkins_tp2_correction`
