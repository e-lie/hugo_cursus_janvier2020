---
title: TP2 - Déployer notre application dans plusieurs contextes avec `kustomize`
draft: true
---


## Reprendre le déploiement du module kubernetes (TP3)

Pour continuer, nous allons reprendre la correction du TP1 avec en plus le déploiement kubernetes du TP3 kubernetes.

- Pour cela, dans le projet `tp_jenkins_application`, commitez vos modifications puis lancez `git checkout jenkins_tp2_base`

- Ouvrez ensuite le projet `tp2_infra_et_app` dans VSCode.

## Déployer à partir d'un dépôt privé avec un login par mot de passe

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
- Installer un répository docker simple en https avec un login (par mot de passe ou autre)

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
secrets:
  htpasswd: <votre htpassword voir suite>
```

- Complétez le nom de domaine avec votre nom.

- Nous allons générer un secret pour le mot de passe htpassword avec `docker run --rm --entrypoint htpasswd registry:2.6.2 -Bbn <votreuser> <votrepassword>`. Collez ce hash dans le values.yaml précédent

 (il faudrait idéalement créer le secret à la main ou ajouter un gestionnaire de secret ici mais il n'y a pas de solution simple).


- Modifiez le `helmfile` de `tp_jenkins_infra` pour ajouter la release du `docker-registry` (dans le namespace `docker-registry`) en vous inspirant des autres et de la doc artifacthub.

- Appliquez le helmfile comme dans le tp0.

- Ajoutez le nom de domaine `registry.<votrenom>.vagrantk3s.dopl.uk` à votre /etc/hosts 

<!-- TODO add le wildcard domain à digitalocean -->

- Connectez vous avec `docker login registry.<votrenom>.vagrantk3s.dopl.uk -u <votreuser> -p <votrepassword>`

- Poussez une image par exemple `python:3.9` en la tagguant avec l'adresse du dépot:
    - `docker tag registry.<votrenom>.vagrantk3s.dopl.uk/python:3.9 python:3.9`



## Faire varier une installation kubernetes

Nous avons besoin de pouvoir déployer notre application **monsterstack** dans Kubernetes de façon légèrement différente selon les environnements `prod` et `dev`.

Deux principales solution s'offrent à nous pour éviter de répéter le même code pour chaque environnement:

- Écrire notre propre chart Helm. Helm utilise des templates un peu comme ansible sur des fichiers resources k8s.
- Utiliser le mode `kustomize` de `kubectl` pour 

Helm est :

- plus puissant est flexible
- plus complexe
- nécessite un dépôt de chart helm comme `chartmuseum` en plus du repository docker

Kustomize est :

- disponible dans kubectl
- plus rigide
- plus adapté lorsque les modifications ne sont pas trop importantes comme pour nous (peu de usecases différents)
## Créer les fichiers de base `kustomize`

Kustomize fonctionne en partant de fichiers ressource kubernetes de base et en écrasant certaines parties du fichiers avec de nouvelles valeurs appelées overlays.

- Ouvrez les fichiers dans `k8s/base`:

Créer des dossiers `base` et `overlays` dans le dossier `k8s`.



## Environnement par défaut : `dev`


## Environnement de production : `prod`



## Tester notre déploiement dans deux namespaces




## Désinstaller et nettoyer