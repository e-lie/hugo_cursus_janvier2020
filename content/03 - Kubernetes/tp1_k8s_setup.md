---
title: TP1 - Installation et configuration de Kubernetes
draft: false
---

Au cours de ce TP nous allons passer rapidement en revue deux manières de mettre en place kubernetes :

- Un cluster de développement avec `microk8s`
- Un cluster managed loué chez le provider Digital Ocean
<!-- - cluster installé et géré manuellement grâce à Ansible (sur des VPS digital ocean) -->

## Découverte de Kubernetes avec microk8s

### Installer microk8s

**Microk8s** est une version de développement de Kubernetes développée par Canonical (Ubuntu) qui peut être utilisée en local ou en mode cluster de plusieurs noeuds. Elle n'utilise pas de machine virtuelle contrairement à la solution de dev plus classique **Minikube**.

- Pour installer microk8s la méthode recommandée est d'utiliser snap : `sudo snap install microk8s --edge --classic`
- Il faut ensuite ajouter notre utilisateur (elk-master) au groupe sudo avec : `sudo usermod -a -G microk8s $USER`
- Déconnectez vous et reconnectez vous de la session pour que les modifications de groupe soient prises en compte.

- Vérifiez que `microk8s` fonctionne avec `microk8s.status --wait-ready`

Microk8s inclue tous les outils pour démarrer avec kubernetes:

- Tous les composants du control plane Kubernetes et kubelet.
- une version interne du client kubectl
- D'autre composants non nécessaires mais importants peuvent être installés sous forme d'addons:
  - ingress pour le loadbalancing
  - coredns pour le dns
  - la dashboard kubernetes

La liste complète est [ici](https://microk8s.io/docs/addons#list)

Pour utiliser le client kubectl interne de microk8s on lance `microk8s.kubectl`:

- La façon classique de tester la connectivité à un cluster est de lister les noeuds(serveurs) avec : `microk8s.kubectl get nodes`

Cependant nous allons installer et configurer une version externe de kubectl pour pouvoir également :

- apprendre comment configurer ce client
- nous connecter à un cluster dans le cloud

### Installer le client Kubernetes `Kubectl`

- Télécharger la clé dev google : `curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -`
- Ajouter le dépot officiel kubernetes pour Ubuntu : `echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list`
- Mettre à jour les dépôts et installer **kubectl** : `sudo apt update && sudo apt install -y kubectl`
- Pour vérifier l'installation lancez : `kubectl version --short`

Configurerons maintenant kubectl pour se connecter au cluster microk8s.

La configuration par défaut de kubectl se trouve dans le fichier `~/.kube/config`

- Créons la bonne configuration en écrasant se fichier avec la config de microk8s: `microk8s.config > ~/.kube/config`
- Ouvrons la configuration YAML pour observer: `gedit ~/.kube/config`
- Testons la connexion avec la commande classique `kubectl get nodes`

##### Bash completion

Pour permettre au client kubectl de compléter le nom des commandes et ressources avec tab il est utile d'installer la completion bash:

```bash
sudo apt install bash-completion

source <(kubectl completion bash)

echo "source <(kubectl completion bash)" >> ${HOME}/.bashrc
```

### Explorons notre cluster k8s

Notre cluster k8s est plein d'objets divers, organisés entre eux de façon dynamique pour décrire des applications, taches de calcul, services et droits d'accès. La première étape consiste à explorer un peu le cluster:

- Listez les nodes pour récupérer le nom de l'unique node (`kubectl get nodes`) puis affichez ses caractéristiques avec `kubectl describe node/<votrenode>`.

La commande `get` est générique et peut être utilisée pour récupérer la liste de tous les types de ressources.

De même la commande `describe` peut s'appliquer à tout objet k8s. On doit cependant prefixer le nom de l'objet par son type `node/elkmaster` car k8s ne peut pas deviner ce que l'on cherche quand plusieurs ressources ont le même nom.

- Pour afficher tous les types de ressources à la on utilise : `kubectl get all`

```
NAME                 TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.152.183.1   <none>        443/TCP   2m34s
```

Il semble qu'il n'y a qu'une ressource dans notre cluster. Il s'agit du service d'API kubernetes pour qu'on puisse communiquer avec le cluster.

En réalité il y en a généralement d'autres cachés dans les autres `namespaces`. En effet les éléments interne de kubernetes tournent eux même sous forme de service et de daemon kubernetes. Les namespaces sont des groupes qui servent à isoler les ressources de façon logique et en terme de droits (avec le Role Based Access Control de kubernetes).

Pour vérifier cela on peut:

- Afficher les namespaces : `kubectl get namespaces`

Un cluster kubernetes a généralement un namespace appelé `default` dans lequel les commandes sont lancées et les ressources crées si on ne précise rien. Il a également aussi un namespace `kube-system` dans lequel résides les processus et ressources système de k8s. Pour préciser le namespace on peut rajouter l'argument `-n` à la plupart des commandes k8s.

- activons l'addon de dns de microk8s: `microk8s.enable dns`

- Pour lister les ressources liées au `kubectl get all -n kube-system`.

- Ou encore : `kubectl get all --all-namespaces` (peut être abrégé en `kubectl get all -A`) qui permet d'afficher le contenu de tous les namespaces en même temps.

- Pour avoir des informations sur un namespace: `kubectl describe namespace/kube-system`

Nous allons maintenant déployer une première application conteneurisée. Le déploiement est plus complexe qu'avec docker (et swarm) en particulier car il est séparé en plusieurs objet et plus configurable.

- Pour créer un déploiement en ligne de commande (par opposition au mode déclaratif que nous verrons plus loin), on peut lancer par exemple: `kubectl create deployment microbot --image=dontrebootme/microbot:v1`.

Cette commande crée un objet de type `deployment`. Nous pourvons étudier ce deployment avec la commande `kubectl describe deployment/microbot`.

- Agrandissons ce déploiement avec `kubectl scale deployment microbot --replicas=2`
- `kubectl describe deployment/microbot` permet de constater que le service est bien passé à 2 replicas.

A ce stade le déploiement n'est pas encore accessible de l'extérieur du cluster pour cela nous devons l'exposer en tant que service:

- `kubectl expose deployment microbot --type=NodePort --port=80 --name=microbot-service`

- affichons la liste des services pour voir le résultat: `kubectl get services`

Le service permet d'exposer un déploiement soit par port soit grace à un loadbalancer. Nous verrons cela plus en détail dans le TP2. Ici notre service est exposé par port: la commande précédent afficher `80:<3xxxx>/TCP` dans la colonne ports. copier le numéro de port de droite du type `32564`.

Pour voir notre application visitez : `localhost:<3xxxx>`. Un étrange robot noir s'affiche.


#### Simplifier les cli k8s

- Pour gagner du temps on dans les commandes kubernetes on définit généralement un alias: `alias kc='kubectl'`. Vous pouvez ensuite remplacer kubectl par kc dans les commandes.

- Également pour gagner du temps en cli, la plupart des mots clés de type kubernetes peuvent être abbrégés:
  - `services` devient `svc`
  - `deployments` devient `deploy`
  - etc

La liste complète [https://blog.heptio.com/kubectl-resource-short-names-heptioprotip-c8eff9fb7202](https://blog.heptio.com/kubectl-resource-short-names-heptioprotip-c8eff9fb7202)

### Dashboard Kubernetes

Kubernetes possède une dashboard officielle pour visualiser et contrôler les ressources. Cette dashboard est distribuée dans microk8s sous forme d'un addon à activer.

- Activer la dashboard: `microk8s.enable dashboard`
- Pour afficher la dashboard la méthode recommandée est de créer un forward du trafic local vers le pod de la dashboard avec `kc port-forward -n kube-system service/kubernetes-dashboard 10443:443`.
- Chargez la page : `https://localhost:10443`

Nous allons nous connecter par token : k8s gère en interne des identités appelées `serviceaccounts` pour lesquelles il génère des tokens d'identification c'est à dire des ressources de type `secret`.

- Listons les secrets du namespace `kube-system` : `kc -n kube-system get secret`
- Parsons ce résultat avec des commande unix pour récupérer le nom de secret du token : `token_name=$(kc -n kube-system get secret | grep default-token | cut -d " " -f1)`
- Affichons la valeur du token en le décrivant : `kc -n kube-system describe secret $token`

```
...
Data
====
ca.crt:     1103 bytes
namespace:  11 bytes
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6InhuNUhNMUZtZksydXhFSVJsRmZGcS1RdXJEZHNNc1dpdmNuVzdsWEVqbE0ifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJkZWZhdWx0LXRva2VuLXpiN3R3Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImRlZmF1bHQiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiJiYmQ1Zjg0Ni1lMjJiLTRlMzMtOTMyNy1hZjY0N2QxNTBkZGUiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZS1zeXN0ZW06ZGVmYXVsdCJ9.i8E4vGD1asP9Eicz2dSxRQmUPzQXbVFhnJXtvZuCS7NM72frGC8Cbz2uSpq-pNdOGqtiH2VLhl4Yh1YCj_pCD5D-CwiccqaubB5tbuTUMdua2vzxKi965HkNaD2-fqI7lVIv-SasZ-LACZXRj7IXXmma2BO6au1CjVD1FOwtrMYSgYY8pAg8e23esCPpW7bRsxwBtq5qzwsRF2rxUdoot0sG7T4CiWbOY19J9YXnWfC19K4Hehd8DIBhjHM5Zwpp0TZO0YlucFJTtLtDQ-1wvnh6Z00q5074yeikZTIKLz8usUhqvnmFdcNJ646eeKKCeh9HWmLG9W646EGFGgf9qQ
```

- Copiez le gros bloc de texte token et collez le dans la page `https://localhost:10443`

Observons la dashboard : Démo.

## Mettre en place un custer K8s dans le cloud avec DigitalOcean

- Clonez le projet modèle : [https://github.com/e-lie/cursus_janvier2020_tp1_k8s](https://github.com/e-lie/cursus_janvier2020_tp1_k8s)
- récupérez un token et ssh_key_fingerprint du TP4 Ansible.
- renommez `secrets.auto.tfvars` sans le .dist et complétez.
- Complétez le code terraform de `digitalocean_k8s.tf` pour changer `k8s-tp-cluster` par `k8s-<votre_nom>-tp-cluster`
- Lancez la création du cluster depuis le dossier terraform avec `terraform init` et `terraform apply`.

La création prend 5 minutes. un fichier de sortie terraform `kubeconfig_do` a été ajouté en local. Il contient la **configuration kubectl** adaptée pour la connexion à notre cluster.

## Merger la configuration kubectl

- Ouvrez avec gedit les fichier les fichiers `kubeconfig_do` et `~/.kube/config`.
- fusionnez dans `~/.kube/config` les éléments des listes YAML de:
  - `clusters`
  - `contexts`
  - `users`
- mettez la clé `current-context:` à `do-lon1-<nom_cluster>` (compléter avec votre valeur)

- Testons la connection avec `kc get nodes`.

## Déployer l'application

- Lancez `kc cluster-info`, l'API du cluster est accessible depuis un nom de domaine généré par digital ocean.
- Déployez l'application `microbot` comme dans la partie précédente avec `microk8s`
- Pour visitez l'application vous devez trouver l'IP publique d'un des noeuds du cluster:
  - relancez `terraform apply -auto-approve > output` et utilisez un editeur de texte pour chercher les ip publiques.


<!--   
### Accéder à la dashboard kubernetes

Les roles et les secrets

<!-- https://alexanderzeitler.com/articles/enabling-the-kubernetes-dashboard-for-digitalocean-kubernetes/ -->


wget https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-beta8/aio/deploy/recommended.yaml

ATTENTION PROBLEME de permission car déploie dans un autre namespace que le tuto précédent :/

kubectl apply -f recommended.yaml

kubectl proxy

http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/login

kubectl get secret

kubernetes-dashboard-token-2ddd2

kubectl  -n kubernetes-dashboard describe kubernetes-dashboard-token-2ddd2

token:      eyJhbGciOiJSUzI1NiIsImtpZCI6Im96ZmwxV2MwUHc3SFE3T3A0VGxVVmlYN09xOTFpWUdfSzBmaTVwcTJLZzgifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJrdWJlcm5ldGVzLWRhc2hib2FyZC10b2tlbi0yZGRkMiIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6ImU1YTY3YTkwLWQ5NzEtNGY3Ni05NzViLTA0M2NjMzNhMzFjYSIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlcm5ldGVzLWRhc2hib2FyZDprdWJlcm5ldGVzLWRhc2hib2FyZCJ9.fCR3b-zfNyehCBCzuEzUe2Dd0PmDiHbY3OPMKUJXNsIKv18iVbmSCEbKv2nAj3tbmPDb3JkRl_OjbVFVpHY6K0rybrwLOlroWvSCOAkWLV_4b0NtsJw0wrGsPeJz9arPjJFmZ_-Ol3s3Jgts30GQBLOh_CNwRcBix3ijHEN71CII-EZoBkTVpYHksmnYeBOmH0zqscZYf2UJ-kWE5LRk8OsJmZsHynO2lWBIG-hn5NWXQbGLc1M_2N9xmOQ_1zajvhfaErElctMME-1gx92bVGzpMDpCOAZH42AZm7LIZMIFW11Nt169YesFclgV2GRUk6anms8Hclo019KIaTp2EQ
 -->




<!-- 
## Installer un cluster manuellement avec `kubeadm`

Votre cluster comprendra les ressources matérielles suivantes :

- Un noeud "master" : Le noeud master est responsable de la gestion de l'état du cluster. Il exécute Etcd, qui stocke les données de cluster parmi les composants qui planifient les charges de travail sur les noeuds de travail.

- Deux noeuds "worker" : Les noeuds de travail sont les serveurs où vos "workloads" (c'est-à-dire les applications et services conteneurisés) s'exécuteront. Un travailleur continuera à exécuter votre charge de travail une fois qu'il y est affecté, même si le maître tombe en panne une fois la planification terminée. La capacité d'un cluster peut être augmentée par l'ajout de travailleurs.


### Provisionner les machines avec Terraform et digital ocean


### Générer un inventaire dynamique avec Ansible Terraform Provider


```ini
[k8s_masters]
master ansible_host=<master_ip> ansible_user=root

[k8s_workers]
worker1 ansible_host=<worker_1_ip> ansible_user=root
worker2 ansible_host=<worker_2_ip> ansible_user=root

[all:vars]
ansible_python_interpreter=/usr/bin/python3
``` -->