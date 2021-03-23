---
title: Cours 3 - Kubernetes Objects
draft: false
---

<!-- 
## Principes d'orchestration

#### Haute disponibilité

Faire en sorte qu'un service ai un "uptime" élevé.
On veut que le service soit tout le temps accessible même lorsque certaines ressources manquent c'est à dire :

- tombent en panne
- sont sorties du service pour mise à jours, maintenance ou modification

Pour cela on doit avoir des ressources multiples ...

- Plusieurs serveurs
- Plusieurs version des données
- Plusieurs accès réseau

Il faut que les ressources disponibles prennent automatiquement le relais des ressources indisponibles.
Pour cela on utilise généralement:

    - des "load balancers" aiguillages réseaux intelligents
    - des "healthchecks" ou vérification de la santé des applications

Aussi:

    - des IP flottantes qui sont en fait des endpoints IP qui fonctionnent comme des loadbalancers
    - des réseaux de secours
    - etc.

Nous allons voir que Kubernetes intègre automatiquement les principes de load balancing et de health check au coeur de l'orchestration de conteneur

#### Répartition de charge (load balancing)

- Un load balancer = une sorte d'**"aiguillage" de traffic réseau**, typiquement http(s) ou tcp.
- Un aiguillage **intelligent** qui se renseigne sur plusieurs critères avant de choisir la direction.
- Cas d'usages:
  - Éviter la surcharge : les requêtes sont réparties sur différents backend pour éviter de les saturer.

L'objectif est de permettre la haute disponibilité : on veut que notre service soit toujours disponible, même en période de panne/maintenance.

- Donc on va dupliquer chaque partie de notre service et mettre les différentes instances derrière un load balancer.
- Le load balancer va vérifier pour chaque backend s'il est disponible (**healthcheck**) avant de rediriger le traffic.
- Répartition géographique: en fonction de la provenance des requêtes on va rediriger vers un datacenter adapté (- proche)+

#### Health Check

Fournir à l'application une façon d'indiquer qu'elle est disponible, c'est à dire qu'elle est démarrée (liveliness) et qu'elle peut répondre aux requêtes (readiness)

#### Exemple: le loadbalancing de Swarm

- Loadbalancer intégré : Ingress
- Permet de router automatiquement le traffic d'un service vers les noeuds qui l'hébergent et sont disponibles.
- Pour héberger une production il suffit de rajouter un loadbalancer externe qui pointe vers un certain nombre de noeuds du cluster et le traffic sera routé automatiquement à partir de l'un des noeuds.

 ##### Solutions de loadbalancing Externe

 - **HAProxy** : Le plus répendu en loadbalancing
 - **Traefik** : Simple à configurer à la mode conteneur
 - **NGINX** : Serveur web central qui a depuis quelques années des fonctions puissantes de loadbalancing et TCP forwarding.

#### Découverte de service (Service discovery)

Classiquement les applications ne sont pas informées du contexte dans lequel elles tournent : la configuration doit être opérée de l'extérieur de l'application.

- par exemple avec des fichiers de configuration
- ou des variables d'environnement

Mais dans un environnement hautement dynamique comme Kubernetes ou Swarm la configuration externe ne suffit pas pour gérer des applications complexe distribuées qui doivent se déployer régulièremeent,
se parler et parler avec l'extérieur.

La découverte de service désigne généralement les méthodes qui permettent à un programme de chercher autour de lui (généralement sur le réseau ou dans l'environnement) ce dont il a besoin.


- La mise en place d'un système de **découverte de service** permet de rendre les applications plus autonomes dans leur (auto)configurations.
- Elles vont pouvoir récupérer des information sur leur contexte (dev ou prod, us ou fr?)
- Ce type d'automatisation de l'intérieur permet de limiter la complexité du déploiement.

Concrêtement dans au sein d'un orchestrateur un système de découverte de service est un serveur qui est au courant automatiquement:

  - de chaque conteneur lancé.
  - du contexte dans lequel il a été lancé.

Ensuite il suffit aux applications de pouvoir interroger ce serveur pour s'autoconfigurer.

Un exemple historique de découverte de service est le DNS avec lequel on fait une requête vers un serveur spécial pour retrouver une adresse IP (on découvre le serveur dont on a besoin)
Cependant le DNS n'a pas été pensé pour ça:

  - certaines application de rafraichissent pas assez souvent leur enregistrements DNS en cache
  - le DNS devient trop complexe à partir de quelques dizaines d'enregistrement

- Le service DNS du réseau overlay de Docker Swarm avec des stacks permet une forme extrêmement simple et implicite de service discovery. Avec le DNS automatique de Swarm votre application microservice docker compose est automatiquement distribuée.

On peut compléter Swarm avec d'autre découvertes de services comme:
  - **Consul**: (Hashicorp): Assez simple d'installation et fournit avec une sympathique interface web.
  - **Etcd** : A prouvé ses performances aux plus grandes échelle mais un peu plus complexe. (à la base de kubernetes mais côté control plane et non pas application)

- Kubernetes propose un service discovery extrêment flexible grace aux `deployments` et `services` -->


## L'API et les Objets Kubernetes

Utiliser Kubernetes consiste à déclarer des objets grâce à l’API Kubernetes pour décrire l’état souhaité d'un cluster: quelles applications ou autres processus exécuter, quelles images elles utilisent, le nombre de réplicas, les ressources réseau et disque que vous mettez à disposition, etc.

Définit des objets généralement via l’interface en ligne de commande, kubectl de deux façons:
- en lançant une commande `kubectl run <conteneur> ...`, `kubectl expose ...`
- en décrivant un objet dans un fichier YAML ou JSON et en le passant au client `kubectl apply -f monpod.yml`

Vous pouvez également écrire des porgramment qui utilisent directement l’API Kubernetes directement pour interagir avec le cluster et définir ou modifier l’état souhaité. **Kubernetes est complètement automatisable !**

### La commande `apply`

Kubernetes encourage le principe de l'infrastructure as code : il est recommandé d'utiliser une description YAML et versionnée des objets et configurations kubernetes plutôt que la CLI.

Pour cela la commande de base est `kubectl apply -f object.yaml`.

La commande inverse `kubectl delete -f object.yaml` permet de détruire un objet précédement appliqué dans le cluster à partir de sa description.

Lorsqu'on vient d'appliquer une description on peut l'afficher dans le terminal avec `kubectl apply -f myobj.yaml view-last-applied`

Globalement Kubernetes garde un historique de toutes les transformations des objets exploration avec la commande `kubectl history ...`

### Syntaxe de base d'une description YAML Kubernetes:


Les description Yaml permettent de décrire de façon lisibles et manipulable de nombreuses caractéristiques à la fois des ressources Kubernetes (un peu comme un compose-file par rapport à la CLI Docker).

##### Exemples

Création d'un service simple:

```yaml
kind: Service
apiVersion: v1
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard
  namespace: kubernetes-dashboard
spec:
  ports:
    - port: 443
      targetPort: 8443
  selector:
    k8s-app: kubernetes-dashboard
  type: NodePort
```

Création d'un "compte utiliseur" `ServiceAccount`

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard
  namespace: kubernetes-dashboard
```

Remarques de syntaxe:

- Toutes les descriptions doivent commencer par spécifier la version d'API (minimale) avec laquelle ils sont sensés être créé
- Il faut également préciser le type d'objet avec `kind`
- Le nom dans `metadata:\n  name: value` est également obligatoire.

- On rajoute généralement une description longue démarrant par `spec:`

#### Description multiressources

- On peut mettre plusieurs ressources à la suite dans un fichier k8s :  permet de décrire une installation complexe en un seul fichier
  - exemple la dashboard kubernetes [https://github.com/kubernetes/dashboard/blob/master/aio/deploy/recommended.yaml](https://github.com/kubernetes/dashboard/blob/master/aio/deploy/recommended.yaml)

- L'ordre n'importe pas car les ressources sont décrites déclarativement c'est-à-dire que:
  - Les dépendances entre les ressources sont déclarées
  - Le control plane de Kubernetes se charge de plannifier l'ordre correct de création en fonction des dépendances (pods avant le déploiement, role avec l'utilisateur lié au role)
  - On préfère cependant les mettre dans un ordre logique pour que les humain puissent les lire.

- On peut sauter des lignes dans le YAML et rendre plus lisible les descriptions
- On sépare les différents objets par `---`

## Les fichier kustomization

Décrire un ensemble de resources dans le même fichier est intéressant mais on pourrait préférer rassembler plusieurs fichiers dans un même dossier et les appliquer d'un coup

Pour cela K8s propose le concept de `kustomization`.

Exemple:

```yaml
k8s-mysql/
├── kustomization.yaml
├── mysql-deployment.yaml
└── wordpress-deployment.yaml
```

`kustomization.yaml`

```yaml
secretGenerator:
  - name: mysql-pass
    literals:
      - password=YOUR_PASSWORD
resources:
  - mysql-deployment.yaml
  - wordpress-deployment.yaml
```

On peut ensuite l'appliquer avec `kubectl apply -k ./`

A notre que `kubectl kustomize .` permet de visualiser l'ensemble de modification avant de les appliquer (`kubectl kustomize . | less` pour mieux lire)

## Objets de base

### Les namespaces

Tous les objets kubernetes sont rangés dans différents espaces de travails isolés appelés `namespaces`.

Cette isolation permet 3 choses:
  - ne voir que ce qui concerne une tache particulière (diminuer la charge cognitive lorsqu'on opère un cluster)
  - créer des limites de ressources CPU RAM etc pour le namespace
  - définir des roles et permissions sur le namespace qui s'appliquent à toutes les ressources à l'intérieur.

Lorsqu'on lit ou créé des objets sans préciser le namespace, ces objets sont liés au namespace `default`

Pour utiliser un namespace autre que `default` avec `kubectl` il faut :

- le préciser avec l'option `-n` : `kubectl get pods -n kube-system`
- créer une nouvelle configuration dans la kubeconfig  pour changer le namespace par defaut.

Kubernetes gère lui-même ses composants interne sous forme de pods et services.

Si vous ne trouvez pas un objet essayez de lancer la commande kubectl avec l'option `-A` ou `--all-namespaces`


### Les Pods

Un Pod est l’unité d’exécution de base d’une application Kubernetes–l’unité la plus petite et la plus simple dans le modèle d’objets de Kubernetes–que vous créez ou déployez. Un Pod représente des process en cours d’exécution dans votre Cluster.

Un Pod encapsule un conteneur applicatif (ou souvent plusieurs conteneurs), des ressources de stockage, **une IP réseau unique**, et des options qui contrôlent comment le ou les conteneurs doivent s’exécuter (restart policy). Cette collection de conteneurs et volumes tournent dans le même environnement d'exécution mais les processus sont isolés.

Un Pod représente une unité de déploiement : un petit nombre de conteneurs qui sont étroitement liés et qui partagent:
  - les même ressources de calcul
  - des volumes communs
  - la même IP donc le même nom de domaine
  - peuvent se parler sur localhost
  - peuvent se parler en IPC
  - on un nom différent et des logs différents

Chaque Pod est destiné à exécuter une instance unique d’un workload donné. Si vous désirez mettre à l’échelle votre workload horizontalement, (par ex., exécuter plusieurs instances), vous devez utiliser plusieurs Pods, un pour chaque instance.

Pour plus de détail sur la philosophie des pods regardez [ce bon article](https://www.mirantis.com/blog/multi-container-pods-and-container-communication-in-kubernetes/)

K8s fournit un ensemble de commande pour débugger des conteneurs

- `kubectl logs <pod-name> -c <conteneur_name>` (le nom du conteneur est inutile si un seul)
- `kubectl exec -it <pod-name> -c <conteneur_name> -- bash`
- `kubectl attach -it <pod-name>`

Enfin pour debugger la sortie réseau d'un programme on peut rapidement forwarder un port depuis un pods vers l'extérieur du cluster:

- `kubectl port-forward <pod-name> <port_interne>:<port_externe>`
- C'est une commande de debug seulement : pour exposer correctement des processus k8s il faut créer un service par exemple de type NodePort pour un port.

Pour copier un fichier dans un pod on peut utiliser: `kubectl cp <pod-name>:</path/to/remote/file> </path/to/local/file>`

Pour monitorer rapidement les ressources consommées par un ensemble de processus il existe les commande `kubectl top nodes` et `kubectl top pods`

##### Un manifeste de Pod

`kuard-pod.yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nom_pod
spec:
  containers:
    - image: tecpi/pod_image:0.1
      name: nom_conteneur
      ports:
        - containerPort: 8080
          name: http
          protocol: TCP
```

### Les ReplicaSet

Un ReplicatSet ou rs est une ressource qui permet de spécifier finement le nombre de réplication d'un pod à un moment donné.
Un peu comme le paramètre `replicas:`  d'un service docker mais en plus précis.

- `kubectl get rs` pour afficher la liste des replicas.

En général on ne les manipule pas directement.

### Les Deployments

Plutôt que d'utiliser les replicaset il est recommander d'utiliser un objet de plus haut niveau les `deployments`.

De la même façon que les ReplicaSets gèrent les pods, les Deployments gèrent les ReplicaSet.

Un déploiement sert surtout comme son nom l'indique à gérer le déploiement d'une nouvelle version d'un pod.

Un `deployment` est un peu l'équivalent d'un `service` docker : il demande la création d'un ensemble de Pods désignés par une étiquette `label`

exemple:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  strategy:
    type: Recreate
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```

- Pour les afficher `kubectl get deployments`

- La commande `kubectl run` sert à créer un `deployment` à partir d'un modèle. Il vaut mieux utilisez `apply -f`.

### Les Services

Dans Kubernetes, un service est une abstraction qui rassemble un ensemble de pods grace à des tags configure une politique permettant d’y accéder depuis l'intérieur ou l'extérieur du cluster.

Un service K8s est en particulier adapté pour implémenter une architecture micro-service.

L’ensemble des pods ciblés par un service est déterminé par un `selector`

Par exemple, considérons un backend de traitement d’image stateless qui s’exécute avec 3 replicas. Ces réplicas sont interchangeables et les frontends ne se soucient pas du backend qu’ils utilisent. Bien que les pods réels qui composent l’ensemble backend puissent changer, les clients frontends ne devraient pas avoir besoin de le savoir, pas plus qu’ils ne doivent suivre eux-mêmes l’ensemble des backends.

L’abstraction du service permet ce découplage en 


Les Services sont de quatre types

- ClusterIP: expose le service sur une IP interne au cluster appelée ClusterIP. Les autres pod peuvent alors accéder au service mais pas l'extérieur.

- NodePort: expose le service depuis l'IP publique de n'importe lequel des noeuds du cluster en faisant un mapping static avec un port personnalisé typiquement dans les 30000. Cela permet d'accéder aux pods interne répliqués. Comme l'IP est stable on peut faire pointer un DNS ou Loadbalancer classique dessus.

- LoadBalancer: expose le service en externe à l’aide d'un Loadbalancer de fournisseur de cloud. Les services NodePort et ClusterIP, vers lesquels le Loadbalancer est dirigés sont automatiquement créés.

- ExternalName: Mappe le service grace à coreDNS au contenu du champ externalName (par exemplefoo.bar.example.com), en renvoyant un enregistrement CNAME avec sa valeur. Aucun proxy d’aucune sorte n’est mis en place.

- On peut aussi créer des services headless en spécifiant `ClusterIP: none` pour les communication interne au cluster non basé sur les IP.

## DaemonSets

Une autre raison de répliquer un ensemble de Pods est de programmer un seul Pod sur chaque nœud du cluster. En général, la motivation pour répliquer un Pod sur chaque nœud est de faire atterrir une sorte d'agent ou de démon sur chaque nœud, et l'objet Kubernetes pour y parvenir est le DaemonSet. Par exemple pour des besoins de monitoring. 

Étant donné les similitudes entre les DaemonSets et les ReplicaSets, il est important de comprendre quand utiliser l'un plutôt que l'autre. Les ReplicaSets doivent être utilisés lorsque votre application est complètement découplée du nœud et que vous pouvez en exécuter plusieurs copies sur un nœud donné sans considération particulière. Les DaemonSets doivent être utilisés lorsqu'une seule copie de votre application doit être exécutée sur tous les nœuds du cluster ou sur un sous-ensemble de ces nœuds.

## Jobs

Les jobs sont utiles pour les choses que vous ne voulez faire qu'une seule fois, comme les migrations de bases de données ou les travaux par lots. Si vous exécutez une migration en tant que Pod normal, votre tâche de migration de base de données se déroulera en boucle, en repeuplant continuellement la base de données.


## Role Based Access Control

Kubernetes intègre depuis quelques versions un système de permissions fines sur les ressources et les namespaces.

- Classiquement on crée des `roles` comme admin ou monitoring qui désignent un ensemble de permission
- On crée ensuite des utilisateurs appelés `serviceaccounts` dans K8s.
- On lie les roles et serviceaccounts à l'aide d'objets rolebindings. Exemple de la dashboard.

A coté des roles crées pour les utilisateurs et processus du cluster il existe des modèles de role prédéfinits qui sont affichables avec:

`kubectl get clusterroles`

la plupart de ces rôles intégrés sont destinés au kube-system c'est à dire aux processus internes du cluster.

Cependant quatre roles sont conçus pour les utilisateurs finaux génériques :
- Le rôle `cluster-admin` fournit un accès complet à l'ensemble du cluster.
- Le rôle `admin` fournit un accès complet à un espace de noms complet.
- Le rôle `edit` permet à un utilisateur final de modifier des choses dans un espace de noms.
- Le rôle `view` permet l'accès en lecture seule à un espace de noms.

microk8s n'a pas les fonctionnalités de RBAC activées par defaut. Il faut lancer `microk8s.enable rbac` pour les configurer. (Mais ne le faite pas pour ne pas perturber le TPà

