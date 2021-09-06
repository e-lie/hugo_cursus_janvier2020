---
title: TP0 - Installer Jenkins et les outils nécessaires dans k8s
draft: false
---


## Installer un cluster `k3s` en utilisant Vagrant

Nous allons recréer un cluster k3s à l'aide de Vagrant pour avoir tous la même configuration (et parce que c'est un bon exercice).

Nous installerons ensuite Jenkins et quelques autres outils dans ce cluster.

- Créer un nouveau dossier `tp_jenkins_infra` avec à l'intérieur un dossier `vagrant_k3s` contenant le `Vagrantfile` suivant:

```ruby
Vagrant.configure("2") do |config|
    config.ssh.insert_key = false
    # si le montage du dossier partagé ne fonctionne pas décommentez la ligne suivante
    # config.vm.synced_folder ".", "/vagrant", disabled: true
    config.vm.provider :virtualbox do |v|
      v.memory = 8192
      v.cpus = 4
    end
  
    config.vm.define :v3s do |v3s|
      v3s.vm.box = "ubuntu/focal64"
      v3s.vm.hostname = "v3s"
      v3s.vm.network :private_network, ip: "10.12.0.10"
      v3s.vm.provision :shell, privileged: false, inline: <<-SHELL
        # commandes d'installation à ajouter ici
      SHELL
    end
  end
```

Nous allons maintenant ajouter la commande pour installer k3s.

Nous allons un peu modifier la commande de base (`curl -sfL https://get.k3s.io | sh`) car nous aimerions paramétrer l'installation de k3s pour:

- ne pas installer l'ingress controller traefik (car nous allons mettre nginx à la place)
- donner un hostname à notre noeud (important si on a plusieurs noeuds et pour reconnaitre son cluster):

`curl -sfL https://get.k3s.io | sh -s - --disable=traefik --node-name=v3s`

- Ajoutez également la commande suivante pour récupérer plus facilement la configuration de connexion kubernetes (si votre dossier partagé fonctionne, sinon il faudra faire un copier coller):

`sudo cp /etc/rancher/k3s/k3s.yaml /vagrant/k3s.yaml`

- Vérifiez que les autres machines vagrant sont arrêtées avec `vagrant global-status --prune`.

- Lancez notre nouveau cluster avec `vagrant up`. Debuggez le cas échéant ;)

- Pour se connecter au nouveau cluster nous avons donc besoin de la configuration kubectl de k3s qui est dans `/etc/rancher/k3s/k3s.yaml` dans la machine vagrant ou dans le dossier `vagrant_k3s` de votre projet (si le dossier partagé fonctionne).


## Révision Kubernetes et Helm : Héberger des applications HTTPS en production

Pour rendre une application HTTP accessible nous avons besoin comme vous l'avez vu de pouvoir créer des ressources de type ingress c'est à dire des reverse proxy dynamiques qui pointent vers les services que vous voulez rendre public.

Il existe plusieurs reverse proxy installables dans kubernetes qu'on appelle des ingress controller. Le plus standard est basé sur nginx et s'appelle le `ingress-nginx`.

### Installer le `nginx ingress controller`.

- Cherchez le `ingress-nginx` sur le répertoire des charts helm [artifacthub.io](https://artifacthub.io)

- Ajoutez le dépôt de chart comme indiqué sur la page du chart `ingress-nginx`.

Avant de lancer l'installation, nous avons également besoin pour la suite de configurer le nginx du controlleur pour permettre le SSL passthrough (la résolution du certificat HTTPS par le backend plutôt que le reverse proxy).

- Créez un dossier `k8s` et l'intérieur un dossier `ingress-nginx`. Créez ensuite un fichier `values.yaml` à l'intérieur avec comme paramètres pour le chart:

```yaml
controller:
  extraArgs:
    enable-ssl-passthrough: '' # --enable-ssl-passthrough aux arguments du conteneur controller
```

- Vérifiez que vous êtes bien dans la bonne configuration k8s avec `kubectl get nodes` qui devrait afficher `v3s`.

- Lancez l'installation dans le namespace `kube-system` et avec les valeurs du fichier `values.yaml` avec la commande suivante: 

`helm install ingress-nginx ingress-nginx/ingress-nginx -n kube-system --values=ingress-nginx/values.yaml --version=4.0.1`

- Vérifiez dans la section `Apps>releases` de `Lens` que le chart est bien installé avec les valeurs du fichier.

### Installer l'utilitaire `cert-manager` pour générer automatiquement des certificats letsencrypt

Pour des raison de sécurité il est également nécessaire pour exposer des applications web à l'extérieur de les configurer avec un certificat HTTPS valide.

Un certificat doit être validé par une autorité de confiance qui va le générer pour nous. La solution automatique pour cela s'appelle Letsencrypt.

Il s'agit :
- d'une API web que l'on peut appeler gratuitement
- pour remplir un "Challenge" confirmant notre légitimité
- récupérer ensuite un certificat attestant de cette légitimité envers le site/nom de domaine
- configurer du HTTPS valide à partir de ce certificat

Sur kubernetes ce processus laborieux peut être remplit automatiquement pour chaque `Ingress`/site web grâce a un composant nommé `Cert Manager` qui comme son nom l'indique gère des certficats.

- Cherchez `cert-manager` sur [artifacthub.io].

Plutôt que de l'installer à la main avec la ligne de commande Helm on voudrait procéder avec de l'infrastructure as code (et rattraper notre installation manuelle de l'étape précédent car c'est mal).

Pour cela nous allons utiliser un utilitaire (parmis de nombreux autres possibles) qui a comme qualité d'être simple appelé `helmfile`.

- Pour l'installer lancez `sudo wget https://github.com/roboll/helmfile/releases/download/v0.140.0/helmfile_linux_amd64 -c -O /usr/bin/helmfile && sudo chmod +x /usr/bin/helmfile`

- Créez ensuite dans le dossier `k8s` un fichier `helmfile.yaml` avec à l'intérieur:

```yaml
repositories:
- name: ingress-nginx
  url: https://kubernetes.github.io/ingress-nginx
- name: jetstack
  url: https://charts.jetstack.io
- name: jenkins
  url: https://charts.jenkins.io
- name: twuni
  url: https://helm.twun.io

releases:

- name: ingress-nginx
  namespace: kube-system
  chart: ingress-nginx/ingress-nginx
  version: 4.0.1
  values:
  - ingress-nginx/values.yaml
```

- Dupliquez le bloc de 6 lignes final sur la release de ingress nginx et modifiez le pour installer cert-manager
    - nom, cert-manager
    - namespace, cert-manager
    - chart, voir sur artifacthub
    - version, voir sur artifacthub
    - values, voir en dessous

- Pour les valeurs d'installation; créez et utilisez  le fichier `cert-manager/values.yaml` avec à l'intérieur simplement:

```yaml
installCRDs: true # Installer automatiquement les Custom Resource Definitions pour les certificats et challenges (déconseillé en prod mais anyway)
```

- Pour installer l'ensemble il suffit de lancer dans le dossier `k8s` la commande: `helmfile apply`

- Vérifiez dans Lens que l'installation s'est bien passée

### Configurer `cert-manager`

Maintenant il faut configurer le manager pour résoudre un challenge Letsencrypt et emettre un certificat.

Comme expliqué [ici](https://cert-manager.io/docs/configuration/acme/) et [là](https://cert-manager.io/docs/configuration/acme/dns01/digitalocean/) il nous faut pour cela

- Créer une resource custom de type `ClusterIssuer` pour effectuer un DNS challenge avec DigitalOcean (compte cloud du formateur). On aurait pu utiliser AWS, Scaleway ou simplement un HTTP challenge si notre cluster avait été pupliquement accessible.

- Créer un secret pour se connecter à l'API de DNS

Comme suit:

- Dans le dossier `cert-manager` précédent créez le fichier CRDs `acme-dns-issuer-prod.yaml` avec à l'intérieur:

```yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: acme-dns-issuer-prod
spec:
  acme:
    # You must replace this email address with your own.
    # Let's Encrypt will use this to contact you about expiring
    # certificates, and issues related to your account.
    email: eliegavoty@free.fr
    server: https://acme-v02.api.letsencrypt.org/directory
    privateKeySecretRef:
      # Secret resource that will be used to store the account's private key.
      name: letsencrypt-prod-account-key
    # Add a single challenge solver, HTTP01 using nginx
    solvers:
    - dns01:
        digitalocean:
          tokenSecretRef:
            name: digitalocean-token
            key: access-token
```

- Dans ce même dossier créez un fichier secret `digitalocean-dns-api-secret.yaml` contenant:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: digitalocean-token
  namespace: cert-manager
data:
  # insert your DO access token here encoded in base64
  access-token: "Y2hhbmdlX21lX3dpdGhfdG9rZW4K"
```

- Vérifiez que vous etes bien dans le dossier cert-manager et appliquez ces deux resources dans le namespace `cert-manager` avec la commande `kubectl -n cert-manager -f .`

Le secret précédent est un placeholder (faux secret) qu'il faut maintenant modifier avec la bonne valeur fournie par le formateur. En effet il faut absolument éviter de pousser des tokens et autre secret dans un dépot git.

- Dans Lens cherchez le secret `digitalocean-token` et éditez le pour mettre la valeur fournie par le formateur à la place du `change me`.

## Installer Jenkins

Nous allons de même utiliser un chart pour installer Jenkins

- Cherchez le Chart Jenkins sur artifacthub.

- Comme précédemment ajoutez au  `helmfile` une release pour Jenkins dans le namespace `jenkins`

- Pour les valeurs, utilisez un fichier `jenkins/values.yaml` contenant:

```yaml
controller:
  ingress:
    enabled: true
    paths: []
    apiVersion: "networking.k8s.io/v1"
    hostName: jenkins.<votrenom>.v3s.dopl.uk
    tls:
      - hosts:
        - jenkins.<votrenom>.v3s.dopl.uk
        secretName: jenkins-tls-cert
    annotations:
      kubernetes.io/ingress.class: "nginx"
      kubernetes.io/tls-acme: "true"
      cert-manager.io/cluster-issuer: acme-dns-issuer-prod
  jenkinsAdminEmail: <votreemail>
  installPlugins:
    - configuration-as-code:1.51
    - job-dsl:1.77
    - kubernetes:1.30.1
    - blueocean:1.24.7
    - ansible:1.1
    - ansicolor:1.0.0
    - ssh-slaves:1.33.0
```

- Complétez bien le fichier précédent avec votre nom et votre email (ou un faux email).

- Installez jenkins avec `helmfile apply`.

### Test l'installation

Comme l'indique la section `ingress` de notre `values.yaml`, nous avons demandé au chart Jenkins de s'occuper du ingress pour nous et de le configurer avec un nom de domaine spécifique.

Comme nous avons configuré le cert-manager, la création d'un ingress en mode tls doit automatiquement déclencher le challende DNS et la récupération/installation d'un certficat.

- Vérifiez dans Lens que
    - le `Ingress` a bien été créé dans le ns jenkins
    - le `Statefulset` Jenkins (un peu comme un déploiement) a bien démarré (pas d'erreur)
    - Une ressource spéciale de type `Certificate` existe bien dans la section `Custom Resource Definitions` et qu'elle est `Ready: True`


- Ajoutez le nom de domaine Jenkins à votre fichier `etc/hosts`.

- Visitez Jenkins dans votre navigateur.

- Connectez vous avec le mot de passe admin présent dans le secret `jenkins` à récupérer dans Lens ou avec `kubectl get secret -n jenkins jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode`

### A propos de l'installation de Jenkins dans Kubernetes


L'installation de Jenkins avec ce Chart est intéressante car:

- Elle permet d'installation automatiquement un ensemble de plugin dans une version particulière
- Elle utilise un plugin Jenkins de Configuration as Code qui permet de préciser précisément la configuration de Jenkins à l'installation ou a l'update

Ces deux qualités permettent de gagner plein de temps et d'avoir une installation de Jenkins stable / référence qui peut être remontée from scratch si Jenkins plante gravement (ce qui arrive assez régulièrement à cause de la divresité des plugins)

- Cette installation dans Kubernetes configure automatiquement la connexion de Jenkins au cluster dans lequel il est installé. Cela permet ensuite d'exécuter les pipelines Jenkins dans des agents kubernetes temporaire.

- Allez vérifier ce dernier point dans `Administrer Jenkins > Gérer les noeuds > Clouds` pour voir la configuration du cloud Kubernetes. On pourrait aussi piloter de multiple Cluster avec un seul Jenkins.