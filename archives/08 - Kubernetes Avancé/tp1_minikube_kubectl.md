---
title: TP1 - Prise en main - Minikube et kubectl
draft: false
---

## Découverte de Kubernetes avec minikube

**Minikube** est la version de développement la plus classique de Kubernetes. Elle permet de configurer rapidement un cluster de un ou plusieurs noeud en utilisant des conteneurs docker ou des machines virtuelles par exemple virtualbox.


### Installer minikube

TODO

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

- activons l'addon de dns de microk8s: `microk8s.enable dns` TODO

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

#### Raccourcir les commandes k8s

- Pour gagner du temps on dans les commandes kubernetes on peut définir un alias: `alias kc='kubectl'`. Vous pouvez ensuite remplacer kubectl par kc dans les commandes.

- Également pour gagner du temps en cli, la plupart des mots clés de type kubernetes peuvent être abbrégés:
  - `services` devient `svc`
  - `deployments` devient `deploy`
  - etc

La liste complète [https://blog.heptio.com/kubectl-resource-short-names-heptioprotip-c8eff9fb7202](https://blog.heptio.com/kubectl-resource-short-names-heptioprotip-c8eff9fb7202)

- Essayez d'afficher les serviceaccounts (users) et les namespaces avec une commande courte.

### Kubernetes avec une interface graphique

Kubernetes possède une [dashboard officielle (WebUI)](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) pour visualiser et contrôler les ressources. Cette dashboard est généralement préinstallée lorsqu'on loue un cluster géré par un provider de cloud et disponible également dans minikube.

Pour activer et lancer la dashboard dans minikube vous pouvez utiliser :  `minikube dashboard` et visiter la dashboard dans un navigateur.

Plutôt que cette dashboard, nous allons utiliser `Kontena Lens`.

#### Installer Lens, un IDE pour kubernetes

Lens est une interface graphique sympatique et puissante pour kubernetes. Elle se connecte en utilisant kubectl en arrière plan avec la configuration `~/.kube/config` par défaut. Elle Offre de nombreuses fonctions puissantes pour explorer et manipuler un cluster. Entre autre fonctions pratiques:

- Visualiser les différentes ressources de façon organisée et réactive
- Manipuler les ressources en mode texte de façon conviviale
- Lancer des shells rapidement dans les conteneurs pour explorer l'environnement
- Basculer entre plusieurs clusters rapidement

- Pour l'installer nous pouvons utiliser snap: `sudo snap install kontena-lens --classic`
- Ensuite lancez `lens`.

#### Explorer l'interface de Lens: Démo

TODO