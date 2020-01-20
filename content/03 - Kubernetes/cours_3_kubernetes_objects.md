---
title: Cours 3 - Kubernetes Objects
draft: true
---


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

- Kubernetes propose un service discovery extrêment flexible grace aux `deployments` et `services`


## L'API et les Objets Kubernetes

Utiliser Kubernetes consiste à déclarer des objets grâce à l’API Kubernetes pour décrire l’état souhaité d'un cluster: quelles applications ou autres processus exécuter, quelles images elles utilisent, le nombre de réplicas, les ressources réseau et disque que vous mettez à disposition, etc.

Définit des objets généralement via l’interface en ligne de commande, kubectl de deux façons:
- en lançant une commande `kubectl run <conteneur> ...`, `kubectl expose ...`
- en décrivant un objet dans un fichier YAML ou JSON et en le passant au client `kubectl apply -f monpod.yml`

Vous pouvez également écrire des porgramment qui utilisent directement l’API Kubernetes directement pour interagir avec le cluster et définir ou modifier l’état souhaité. **Kubernetes est complètement automatisable !**

## Objets de base

### Les namespaces

Tous les objets kubernetes sont rangés dans différents espaces de travails isolés appelés `namespaces`.

Lorsqu'on lit ou créé des objets sans préciser le namespace, ces objets sont liés au namespace `default`

Pour utiliser un namespace autre que `default` avec `kubectl` il faut :

- le préciser avec l'option `-n` : `kubectl get pods -n kube-system`
- créer une nouvelle configuration dans la kubeconfig  pour changer le namespace par defaut.

Kubernetes gère lui-même ses composants interne sous forme de pods et services.

Si vous ne trouvez pas un objet essayez de lancer la commande kubectl avec l'option `-A` ou `--all-namespaces`

### Les Pods

Un Pod est l’unité d’exécution de base d’une application Kubernetes–l’unité la plus petite et la plus simple dans le modèle d’objets de Kubernetes–que vous créez ou déployez. Un Pod représente des process en cours d’exécution dans votre Cluster.

Un Pod encapsule un conteneur applicatif (ou, dans certains cas, plusieurs conteneurs), des ressources de stockage, **une IP réseau unique**, et des options qui contrôlent comment le ou les conteneurs doivent s’exécuter.

Un Pod représente une unité de déploiement : un petit nombre de conteneurs qui sont étroitement liés et qui partagent des ressources.

Chaque Pod est destiné à exécuter une instance unique d’une application donnée. Si vous désirez mettre à l’échelle votre application horizontalement, (par ex., exécuter plusieurs instances), vous devez utiliser plusieurs Pods, un pour chaque instance

### Les ReplicaSet

Un ReplicatSet ou rs est une ressource qui permet de spécifier finement le nombre de réplication d'un pod à un moment donné.
Un peu comme le paramètre `replicas:`  d'un service docker mais en plus précis.

- `kubectl get rs` pour afficher la liste des replicas.

En général on ne les manipule pas directement

### Les Deployments

Plutôt que d'utiliser les replicaset il est recommander d'utiliser un objet de plus haut niveau les `deployments`.

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

- La commande `kubectl run` sert à créer un `deployment` à partir d'un modèle.
- Sinon on utilise généralement la commande `kubectl apply -f`

- pour supprimer un deployment `kubectl 



## Role Based Access Control

`kubectl get clusterroles`

la plupart de ces rôles intégrés sont destinés au kube-system c'est à dire aux processus internes du cluster.

Cependant quatre roles sont conçus pour les utilisateurs finaux génériques :
- Le rôle `cluster-admin` fournit un accès complet à l'ensemble du cluster.
- Le rôle `admin` fournit un accès complet à un espace de noms complet.
- Le rôle `edit` permet à un utilisateur final de modifier des choses dans un espace de noms.
- Le rôle `view` permet l'accès en lecture seule à un espace de noms.


