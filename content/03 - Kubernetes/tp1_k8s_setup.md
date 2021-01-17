---
title: TP1 - Installation et configuration de Kubernetes
draft: false
---

Au cours de nos TPs nous allons passer rapidement en revue deux manières de mettre en place Kubernetes :

- Un cluster de développement avec `minikube`
- Un cluster managed loué chez un provider (Scaleway, DigitalOcean, Azure ou Google Cloud)

Nous allons d'abord passer par la première option.

## Découverte de Kubernetes

### Installer le client CLI `kubectl`

kubectl est le point d'entré universel pour contrôler tous les type de cluster kubernetes. 
C'est un client en ligne de commande qui communique en REST avec l'API d'un cluster.

Nous allons explorer kubectl au fur et à mesure des TPs. Cependant à noter que :

- `kubectl` peut gérer plusieurs clusters/configurations et switcher entre ces configurations
- `kubectl` est nécessaire pour le client graphique `Lens` que nous utiliserons plus tard.

La méthode d'installation importe peu. Pour installer kubectl sur Ubuntu nous ferons simplement: `sudo snap install kubectl --classic`.

- Faites `kubectl version` pour afficher la version du client kubectl.
### Installer Minikube

**Minikube** est la version de développement de Kubernetes (en local) la plus répendue. Elle est maintenue par la cloud native foundation et très proche de kubernetes upstream. Elle permet de simuler un ou plusieurs noeuds de cluster sous forme de conteneurs docker ou de machines virtuelles.

- Pour installer minikube la méthode recommandée est indiquée ici: https://minikube.sigs.k8s.io/docs/start/

Nous utiliserons classiquement `docker` comme runtime pour minikube (les noeuds k8s seront des conteneurs simulant des serveurs). Ceci est, bien sur, une configuration de développement. Elle se comporte cependant de façon très proche d'un véritable cluster.

- Si Docker n'est pas installé, installer Docker avec la commande en une seule ligne : `curl -fsSL https://get.docker.com | sh`, puis ajoutez-vous au groupe Docker avec `sudo usermod -a -G docker <votrenom>`, et faites `sudo reboot` pour que cela prenne effet.

- Pour lancer le cluster faites simplement: `minikube start` (il est également possible de préciser le nombre de coeurs de calcul, la mémoire et et d'autre paramètre pour adapter le cluster à nos besoins.)

Minikube configure automatiquement kubectl (dans le fichier `~/.kube/config`) pour qu'on puisse se connecter au cluster de développement.

- Testez la connexion avec `kubectl get nodes`.

Affichez à nouveau la version `kubectl version`. Cette fois-ci la version de kubernetes qui tourne sur le cluster actif est également affichée. Idéalement le client et le cluster devrait être dans la même version mineure par exemple `1.20.x`.

##### Bash completion

Pour permettre à `kubectl` de compléter le nom des commandes et ressources avec `<Tab>` il est utile d'installer l'autocomplétion pour Bash :

```bash
sudo apt install bash-completion

source <(kubectl completion bash)

echo "source <(kubectl completion bash)" >> ${HOME}/.bashrc
```

**Vous pouvez désormais appuyer sur `<Tab>` pour compléter vos commandes `kubectl`, c'est très utile !**

### Explorons notre cluster k8s

Notre cluster k8s est plein d'objets divers, organisés entre eux de façon dynamique pour décrire des applications, tâches de calcul, services et droits d'accès. La première étape consiste à explorer un peu le cluster :

- Listez les nodes pour récupérer le nom de l'unique node (`kubectl get nodes`) puis affichez ses caractéristiques avec `kubectl describe node/minikube`.

La commande `get` est générique et peut être utilisée pour récupérer la liste de tous les types de ressources.

De même, la commande `describe` peut s'appliquer à tout objet k8s. On doit cependant préfixer le nom de l'objet par son type (ex : `node/minikube` ou `nodes minikube`) car k8s ne peut pas deviner ce que l'on cherche quand plusieurs ressources ont le même nom.

- Pour afficher tous les types de ressources à la fois que l'on utilise : `kubectl get all`

```
NAME                 TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1   <none>        443/TCP   2m34s
```

Il semble qu'il n'y a qu'une ressource dans notre cluster. Il s'agit du service d'API Kubernetes, pour qu'on puisse communiquer avec le cluster.

En réalité il y en a généralement d'autres cachés dans les autres `namespaces`. En effet les éléments internes de Kubernetes tournent eux-mêmes sous forme de services et de daemons Kubernetes. Les *namespaces* sont des groupes qui servent à isoler les ressources de façon logique et en termes de droits (avec le *Role-Based Access Control* (RBAC) de Kubernetes).

Pour vérifier cela on peut :

- Afficher les namespaces : `kubectl get namespaces`

Un cluster Kubernetes a généralement un namespace appelé `default` dans lequel les commandes sont lancées et les ressources créées si on ne précise rien. Il a également aussi un namespace `kube-system` dans lequel résident les processus et ressources système de k8s. Pour préciser le namespace on peut rajouter l'argument `-n` à la plupart des commandes k8s.

- Pour lister les ressources liées au `kubectl get all -n kube-system`.

- Ou encore : `kubectl get all --all-namespaces` (peut être abrégé en `kubectl get all -A`) qui permet d'afficher le contenu de tous les namespaces en même temps.

- Pour avoir des informations sur un namespace : `kubectl describe namespace/kube-system`

### Déployer une application
<!-- TODO: TEST THIS -->

Nous allons maintenant déployer une première application conteneurisée. Le déploiement est plus complexe qu'avec Docker (et Swarm), en particulier car il est séparé en plusieurs objets et plus configurable.

- Pour créer un déploiement en ligne de commande (par opposition au mode déclaratif que nous verrons plus loin), on peut lancer par exemple: `kubectl create deployment microbot --image=monachus/rancher-demo`.
<!-- - Pour créer un déploiement en ligne de commande (par opposition au mode déclaratif que nous verrons plus loin), on peut lancer par exemple: `kubectl create deployment microbot --image=dontrebootme/microbot:v1`. -->

Cette commande crée un objet de type `deployment`. Nous pourvons étudier ce deployment avec la commande `kubectl describe deployment/microbot`.

- Agrandissons ce déploiement avec `kubectl scale deployment microbot --replicas=5`
- `kubectl describe deployment/microbot` permet de constater que le service est bien passé à 5 replicas.

A ce stade le déploiement n'est pas encore accessible de l'extérieur du cluster pour cela nous devons l'exposer en tant que service :

<!-- - `kubectl expose deployment microbot --type=LoadBalancer --port=8080 --name=microbot-service` -->
- `kubectl expose deployment microbot --type=NodePort --port=8080 --name=microbot-service`
<!-- Doesn't work with minikube expose: --port=8765 --target-port=9376 -->

- affichons la liste des services pour voir le résultat: `kubectl get services`

Un service permet d'exposer un déploiement soit par port soit grâce à un loadbalancer.

Pour exposer cette application sur le port de notre choix, nous devrions avoir recours à un LoadBalancer. 

Nous verrons cela plus en détail dans le TP2. 

Nous ne verrons pas ça ici (il faudrait utiliser l'addon MetalLB de Minikube). 

Mais nous pouvons quand même lancer une commande dans notre environnement de dev :
`kubectl port-forward svc/microbot-service 8080:8080 --address 0.0.0.0`

Vous pouvez désormais accéder à votre app via :
`http://localhost:8080`
<!-- `http://votreprenom.lab.doxx.fr:8080` -->

Minikube intègre aussi une façon d'accéder à notre service : c'est la commande `minikube service microbot-service`

<!-- Ici notre service est exposé par port : la commande précédente affiche `8080:<3xxxx>/TCP` dans la colonne ports. -->
<!-- Copier le numéro de port de droite, du type `32564`. -->

<!-- Pour voir notre application visitez : `localhost:80`. -->

<!-- L'application devrait s'être ouverte dans votre navigateur, sinon vous pouvez lancer `minikube service microbot-service --url` pour en obtenir l'URL. -->

Sauriez-vous expliquer ce que l'app fait ?


#### Simplifier les lignes de commande k8s

- Pour gagner du temps on dans les commandes Kubernetes on définit généralement un alias: `alias kc='kubectl'` (à mettre dans votre `.bash_profile` en faisant `echo "alias kc='kubectl'" >> ~/.bash_profile`, puis en faisant `source ~/.bash_profile`).
- Vous pouvez ensuite remplacer `kubectl` par `kc` dans les commandes.

- Également pour gagner du temps en ligne de commande, la plupart des mots-clés de type Kubernetes peuvent être abrégés :
  - `services` devient `svc`
  - `deployments` devient `deploy`
  - etc.

La liste complète : <https://blog.heptio.com/kubectl-resource-short-names-heptioprotip-c8eff9fb7202>

- Essayez d'afficher les serviceaccounts (users) et les namespaces avec une commande courte.

#### Installer Lens

Lens est une interface graphique sympatique pour Kubernetes.

Elle se connecte en utilisant la configuration `~/.kube/config` par défaut et nous permettra d'accéder à un dashboard bien plus agréable à utiliser.

Vous pouvez l'installer en lançant ces commandes :
```bash
curl -fsSL https://github.com/lensapp/lens/releases/download/v4.0.6/Lens-4.0.6.AppImage -o ~/Lens.AppImage
chmod +x ~/Lens.AppImage
~/Lens.AppImage
```

## Mettre en place un cluster K8s dans le cloud avec un provider type DigitalOcean ou Scaleway

- Créez un compte (ou récupérez un accès) sur [DigitalOcean](https://cloud.digitalocean.com/) ou [Scaleway](https://console.scaleway.com/)
- Créez un cluster Kubernetes avec [l'interface DigitalOcean](https://cloud.digitalocean.com/kubernetes/clusters/new) ou bien [l'interface Scaleway](https://console.scaleway.com/kapsule/clusters/create)

La création prend environ 5 minutes.

- Sur DigitalOcean, il vous est proposé dans l'étape 3 ou sur la page de votre cluster Kubernetes de télécharger le fichier `kubeconfig`. (*download the cluster configuration file*, ou bien *Download Config File*).
- De même, sur Scaleway, sur la page décrivant votre cluster, un gros bouton en bas de la page vous incite à télécharger ce même fichier `kubeconfig` (*Download Kubeconfig*).

Ce fichier contient la **configuration kubectl** adaptée pour la connexion à notre cluster.

<!-- - Clonez le projet modèle : <https://github.com/Uptime-Formation/cursus_janvier2020_tp1_k8s> -->
<!-- - récupérez un token depuis votre compte de cloud (Scaleway ou).
- renommez `secrets.auto.tfvars` sans le .dist et complétez.
- Complétez le code terraform de `digitalocean_k8s.tf` pour changer `k8s-tp-cluster` par `k8s-<votre_nom>-tp-cluster`
- Lancez la création du cluster depuis le dossier terraform avec `terraform init` et `terraform apply`. -->

## Merger la configuration kubectl

- Ouvrez avec `gedit` les fichiers `kubeconfig` et `~/.kube/config`.
- fusionnez dans `~/.kube/config` les éléments des listes YAML de:
  - `clusters`
  - `contexts`
  - `users`
- mettez la clé `current-context:` à `<nom_cluster>` (compléter avec votre valeur)

- Testons la connection avec `kubectl get nodes`.

## Déployer l'application

- Lancez `kubectl cluster-info`, l'API du cluster est accessible depuis un nom de domaine généré par le provider.
- Déployez l'application `microbot` comme dans la partie précédente avec `minikube`
- Pour visitez l'application vous devez trouver l'IP publique d'un des nœuds du cluster en listant les objets de type `Service`, ou sur la page du fournisseur de cloud.
 
<!--  - relancez `terraform apply -auto-approve > output` et utilisez un editeur de texte pour chercher les ip publiques. -->


<!--   
### Accéder à la dashboard kubernetes

Les roles et les secrets

<!-- https://alexanderzeitler.com/articles/enabling-the-kubernetes-dashboard-for-digitalocean-kubernetes/ -->


<!-- wget https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-beta8/aio/deploy/recommended.yaml

ATTENTION PROBLEME de permission car déploie dans un autre namespace que le tuto précédent :/

kubectl apply -f recommended.yamlwget https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-beta8/aio/deploy/recommended.yaml

ATTENTION PROBLEME de permission car déploie dans un autre namespace que le tuto précédent :/

kubectl apply -f recommended.yaml

kubectl proxy

http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/login

kubectl get secret

kubernetes-dashboard-token-2ddd2

kubectl  -n kubernetes-dashboard describe kubernetes-dashboard-token-2ddd2

token:      eyJhbGciOiJSUzI1NiIsImtpZCI6Im96ZmwxV2MwUHc3SFE3T3A0VGxVVmlYN09xOTFpWUdfSzBmaTVwcTJLZzgifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJrdWJlcm5ldGVzLWRhc2hib2FyZC10b2tlbi0yZGRkMiIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6ImU1YTY3YTkwLWQ5NzEtNGY3Ni05NzViLTA0M2NjMzNhMzFjYSIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlcm5ldGVzLWRhc2hib2FyZDprdWJlcm5ldGVzLWRhc2hib2FyZCJ9.fCR3b-zfNyehCBCzuEzUe2Dd0PmDiHbY3OPMKUJXNsIKv18iVbmSCEbKv2nAj3tbmPDb3JkRl_OjbVFVpHY6K0rybrwLOlroWvSCOAkWLV_4b0NtsJw0wrGsPeJz9arPjJFmZ_-Ol3s3Jgts30GQBLOh_CNwRcBix3ijHEN71CII-EZoBkTVpYHksmnYeBOmH0zqscZYf2UJ-kWE5LRk8OsJmZsHynO2lWBIG-hn5NWXQbGLc1M_2N9xmOQ_1zajvhfaErElctMME-1gx92bVGzpMDpCOAZH42AZm7LIZMIFW11Nt169YesFclgV2GRUk6anms8Hclo019KIaTp2EQ

kubectl proxy

http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/login

kubectl get secret

kubernetes-dashboard-token-2ddd2

kubectl  -n kubernetes-dashboard describe kubernetes-dashboard-token-2ddd2

token:      eyJhbGciOiJSUzI1NiIsImtpZCI6Im96ZmwxV2MwUHc3SFE3T3A0VGxVVmlYN09xOTFpWUdfSzBmaTVwcTJLZzgifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJrdWJlcm5ldGVzLWRhc2hib2FyZC10b2tlbi0yZGRkMiIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6ImU1YTY3YTkwLWQ5NzEtNGY3Ni05NzViLTA0M2NjMzNhMzFjYSIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlcm5ldGVzLWRhc2hib2FyZDprdWJlcm5ldGVzLWRhc2hib2FyZCJ9.fCR3b-zfNyehCBCzuEzUe2Dd0PmDiHbY3OPMKUJXNsIKv18iVbmSCEbKv2nAj3tbmPDb3JkRl_OjbVFVpHY6K0rybrwLOlroWvSCOAkWLV_4b0NtsJw0wrGsPeJz9arPjJFmZ_-Ol3s3Jgts30GQBLOh_CNwRcBix3ijHEN71CII-EZoBkTVpYHksmnYeBOmH0zqscZYf2UJ-kWE5LRk8OsJmZsHynO2lWBIG-hn5NWXQbGLc1M_2N9xmOQ_1zajvhfaErElctMME-1gx92bVGzpMDpCOAZH42AZm7LIZMIFW11Nt169YesFclgV2GRUk6anms8Hclo019KIaTp2EQ -->


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