---
title: "12 - TP 5 - Déployer Wordpress avec Helm et ArgoCD"
draft: false
weight: 2071
---

Helm est un "gestionnaire de paquet" ou vu autrement un "outil de templating avancé" pour k8s qui permet d'installer des applications sans faire des copier-coller pénibles de YAML :

- Pas de duplication de code
- Possibilité de créer du code générique et flexible avec pleins de paramètres pour le déploiement.
- Des déploiements avancés avec plusieurs étapes

Inconvénient: Helm ajoute souvent de la complexité non nécessaire car les Charts sur internet sont très paramétrables pour de multiples cas d'usage (plein de code qui n'est utile que dans des situations spécifiques).

Helm ne dispense pas de maîtriser l'administration de son cluster.

### Installer Helm

- Pour installer Helm sur Ubuntu, utilisez : `sudo snap install helm --classic`

- Suivez le Quickstart : <https://helm.sh/docs/intro/quickstart/>

#### Autocomplete

`helm completion bash | sudo tee /etc/bash_completion.d/helm` et relancez votre terminal.

### Utiliser un chart Helm pour installer Wordpress

- Cherchez Wordpress sur [https://artifacthub.io/](https://artifacthub.io/).

- Prenez la version de **Bitnami** et ajoutez le dépôt avec la première commande à droite (ajouter le dépôt et déployer une release).

- Installer une **"release"** `wordpress-tp` de cette application (ce chart) avec `helm install wordpress-tp bitnami/wordpress`

- Des instructions sont affichées dans le terminal pour trouver l'IP et afficher le login et password de notre installation. La commande pour récupérer l'IP ne fonctionne que dans les cluster proposant une intégration avec un loadbalancer et fournissant donc des IP externe. Dans minikube (qui ne fournit pas de loadbalancer) il faut à la place lancer `minikube service wordpress-tp` pour y accéder avec le NodePort.

- Notre Wordpress est prêt. Connectez-vous-y avec les identifiants affichés (il faut passer les commandes indiquées pour récupérer le mot de passe stocké dans un secret k8s).

Vous pouvez constater que l'utilisateur est par default `user` ce qui n'est pas très pertinent. Un chart prend de nombreux paramètres de configuration qui sont toujours listés dans le fichier `values.yaml` à la racine du Chart.

On peut écraser certains de ces paramètres dans un nouveau fichier par exemple `myvalues.yaml` et installer la release avec l'option `--values=myvalues.yaml`.

<!-- Nous allons faire cela avec Argocd à la place de la CLI helm. -->

- Désinstallez Wordpress avec `helm uninstall wordpress-tp`

### Utiliser la fonction `template` de Helm pour étudier les ressources d'un Chart

- Visitez le code des charts de votre choix en clonant le répertoire Git des Charts officielles Bitnami et en l'explorant avec VSCode :

```bash
git clone https://github.com/bitnami/charts/
code charts
```

- Regardez en particulier les fichiers `templates` et le fichier de paramètres `values.yaml`.

- Comment modifier l'username et le password wordpress à l'installation ? il faut donner comme paramètres le yaml suivant:

```yaml
wordpressUsername: <votrenom>
wordpressPassword: <easytoguesspasswd>
```

- Nous allons paramétrer plus encore l'installation. Créez un dossier TP5 avec à l'intérieur un fichier `values.yaml` contenant:

```yaml
wordpressUsername: <stagiaire> # replace
wordpressPassword: myunsecurepassword
wordpressBlogName: Kubernetes example blog

replicaCount: 1

service:
  type: ClusterIP

ingress:
  enabled: true
  hostname: wordpress.<stagiaire>.formation.dopl.uk # replace with your hostname pointing on the cluster ingress loadbalancer IP
  tls: true
  certManager: true
  annotations:
    cert-manager.io/cluster-issuer: letsencrypt-prod
    kubernetes.io/ingress.class: nginx
```

- En utilisant ces paramètres, plutôt que d'installer le chart, nous allons faire le rendu (templating) des fichiers ressource générés par le chart: `helm template wordpress-tp bitnami/wordpress --values=values.yaml > wordpress-tp-manifests.yaml`.

On peut maintenant lire dans ce fichier les objets kubernetes déployés par le chart et ainsi apprendre de nouvelles techniques et syntaxes. En le parcourant on peut constater que la plupart des objets abordés pendant cette formation y sont présent plus certains autres.

### Installer le ingress NGINX et ArgoCD

voir TP Gitlab Argocd

<!-- Cette partie requiert d'avoir installé k3s sur votre machine de formation (ou sur un autre VPS ubuntu 20.04 min 8Go de RAM) comme indiqué dans le TP1 section 3e méthode d'installation.

- Pour bien effectuer l'installation sur notre cluster k3s (et pas dans minikube ou sur un cluster loué) lancez:

- `minikube stop`

`kubectl config use-context default` ou `kubectl config use-context k3s` puis `kubectl get nodes` pour vérifier.

Argocd est une solution de "Continuous Delivery" dédiée au **GitOps** avec Kubernetes. Elle fourni une interface assez géniale pour détecter et monitorer les ressources d'un cluster. En particulier pour visualiser les charts Helm déployés.

ArgoCD s'installe grâce à une série de manifestes Kubernetes. Pour récupérer ces manifestes d'installation nous allons utiliser git et le dépôt de correction : `cd ~/Desktop && git clone -b argocd_installation https://github.com/Uptime-Formation/corrections_tp.git argocd_installation`.

Cette installation comporte plusieurs étapes qui doivent être exécutées dans l'ordre et en vérifiant s'il n'y a pas d'erreurs à chaque étape.

- Pour être sur d'installer argocd sur notre cluster k3s lancez: `kubectl config use-context default` puis `kubectl get nodes` pour vérifier.
- Les resources Kubernetes d'installation sont dans le dossier cloné précédemment et la partie kubernetes: `cd ~/Desktop/argocd_installation/kubernetes`
- Commençons par créer quelques namespaces (`argocd` et `cert-manager`) pour installer nos différentes applications: `kubectl apply -f argocd-kluster/namespaces.yaml`
- Puis installation de l'ingress controller nginx dans le namespace `kube-system`: `kubectl apply -n kube-system -f argocd-kluster/ingress-nginx`
- Dans Lens vérifiez que le pod `ingress-nginx-controller-xxx` est bien lancé (vert)

Ensuite installons l'application cert-manager qui permet de générer **automatiquement** des certificats TLS pour nos applications web HTTPS (notamment avec letsencrypt et ACME). Argocd à une interface web qui nécessite un accès https.

- Lancez : `kubectl apply -f argocd-kluster/cert-manager/cert-manager-manifests.yaml`.
- Créons également les **"Issuers"** c'est à dire les composants qui vont permettre d'émettre des certificats avec la commande: `kubectl apply -n cert-manager -f argocd-kluster/cert-manager/issuers`.

Vos serveurs VNC qui sont aussi désormais des clusters k3s ont déjà deux sous-domaines configurés: `<votrelogin>.formation.dopl.uk` et `*.<votrelogin>.formation.dopl.uk`. Le sous domaine `argocd.<login>.formation.dopl.uk` pointe donc déjà sur le serveur (Wildcard DNS). Celà va permettre à `cert-manager` de créer automatiquement un `ACME HTTP Challenge` pour enregistrer un certificat TLS.

- Dans le fichier `argocd-kluster/argocd/argocd-ingress.yaml`, changez `<yourname>` par votre nom (le login guacamole) pour configurer l'ingress sur le nom de domaine de votre cluster personnel.

- Ensuite installez **ArgoCD** avec la commande: `kubectl apply -f argocd-kluster/argocd/manifests -n argocd`
- Enfin `kubectl apply -f argocd-kluster/argocd/argocd-ingress.yaml`

- Affichez les ingress du namespace argocd: il devrait y avoir 2 ingress pendant un moment (car le http challenge implique un ingress temporaire) puis un seul celui de l'interface web de argocd. Vous pouvez également vérifier que le certificat est ready avec : `kubectl get certificates -n argocd`. -->

### ArgoCD pour installer et visualiser en live les ressources de notre chart

Argocd permet de d'installer des applications qui peuvent être soit des dossiers de manifestes kubernetes simple, soit des dossiers contenant une `kustomization.yaml` soit des charts Helm. Une application Argocd peut être créée dans l'interface web ou être déclarée elle-même grâce à un fichier manifeste de type:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
```

Ce n'est pas une ressource de base mais bien une `CustomResourceDefinition` car ArgoCD est un opérateur d'applications. Nous allons créer un tel manifeste.

- Ouvrez le fichier `wordpress-chart-argocd-app.yaml` et collez à l'intérieur:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: wordpress
  namespace: argocd
spec:
  destination:
    namespace: default
    server: https://kubernetes.default.svc
  project: default
  source:
    repoURL: https://charts.bitnami.com/bitnami
    chart: wordpress
    targetRevision: 11.0.5
    helm:
      values: |
        wordpressUsername: elie
        wordpressPassword: myunsecurepassword
        wordpressBlogName: Kubernetes example blog

        replicaCount: 1

        service:
          type: ClusterIP

        ingress:
          enabled: true
          hostname: wordpress.elie.formation.dopl.uk
          pathType: Prefix
          tls: true
          certManager: true
          annotations:
            cert-manager.io/cluster-issuer: letsencrypt-prod
            kubernetes.io/ingress.class: nginx
```

- Appliquez ce fichier avec `kubectl apply -f`.

- Visitez la page `https://argocd.<votrelogin>.formation.dopl.uk`.

Une application wordpress est apparue

- Visitez la, en particulier les `desired manifests` de quelques resources.
- Synchronisez l'application pour installer le chart avec `Sync`.

En une minute ou deux, l'application est installée et l'ingress avec son certificat devrait être généré.

Vous pouvez visiter le blog à l'adresse: `https://wordpress.<votrelogin>.formation.dopl.uk`

### Solution

Le dépôt Git contenant la correction de ce TP et des précédents est accessible avec cette commande : `git clone -b all_corrections https://github.com/Uptime-Formation/corrections_tp.git`
