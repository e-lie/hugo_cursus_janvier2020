title: Conteneurs Docker
class: animation-fade
layout: true

<!-- This slide will serve as the base layout for all your slides -->
<!--
.bottom-bar[
  {{title}}
]
-->

---

class: impact

# {{title}}
## *Modularisez et maîtrisez vos applications*

---

class: impact

# Orchestration et clustering

---

# Docker Machine

Est l'outil de gestion d'hôtes docker.
--

- Il est capable de créer des serveurs docker à la volée
  - chez différents fournisseurs de cloud
  - en local avec virtualbox pour tester son application en mode production et/ou distribué.
--

- L'intérêt de `docker-machine` est d'être **parfaitement intégré** dans l'outil de CLI Docker.
--

- Il sert également de base pour créer un Swarm Docker et distribuer les conteneurs entre plusieurs hôtes.

---

## Docker Machine

- Concrêtement, `docker-machine` permet de **créer automatiquement des machines** avec le **docker engine** et **ssh** configuré et de gérer les **certificats TLS** pour se connecter à l'API docker des différents serveurs.
--

- Il permet également de changer le contexte de la ligne de commande docker avec des variables d'environnement pour basculer sur l'un ou l'autre serveur
--

- Il permet également de se connecter à une machine en ssh en une simple commande.

---

## Docker Machine

Exemple :

```bash
 docker-machine create  --driver digitalocean \
      --digitalocean-ssh-key-fingerprint 41:d9:ad:ba:e0:32:73:58:4f:09:28:15:f2:1d:ae:5c \
      --digitalocean-access-token "a94008870c9745febbb2bb84b01d16b6bf837b4e0ce9b516dbcaf4e7d5ff2d6" \
      identihost-do
```

Pour basculer `eval $(docker env identihost-do);`

- `docker run -d nginx:latest` créé ensuite un conteneur **sur le droplet digitalocean** précédemment créé.

- `docker ps -a` affiche le conteneur en train de tourner à distance.
- `wget $(docker-machine ip identihost-do)` va récupérer la page nginx.

---

# Orchestration

- Un des intérêts principaux de Docker et des conteneurs en général est de:
  - favoriser la modularité et les architectures microservice.
  - permettre la scalabilité horizontale des applications en multipliant les conteneurs.

- A partir d'une certaine échelle, il n'est plus question de gérer les serveurs et leurs conteneurs à la main.
  
- Nous avons vu rapidement que des outils de provisionning comme Ansible pouvaient aider à :
  - **"bootstraper"** rapidement des environnements Docker.
  - **contrôler** des conteneurs assez **statiquement** dans une certaine mesure.
  - Ce n'est pas idéal...

---

# L'orchestration

L'orchestration consiste à automatiser la création et la répartion des conteneurs à travers un cluster de serveurs

- déployer de nouvelles versions d'une application progressivement.
- faire grandir la quantité d'instance de chaque application facilement.
- Voir dans le cas de l'auto-scaling de faire grossir l'application automatiquement en fonction de la demande.

---

# Qu'est-ce que Docker Swarm ?

- Swarm est l'**outil de clustering et d'orchestration natif** de Docker (développé par Docker Inc.).

- Il s'intègre très bien avec les autre commande docker (on a même pas l'impression de faire du clustering).

- Il permet de gérer de très grosses productions Docker.

- Swarm utilise l'API standard du Docker Engine (sur le port 2376) et son API de management swarm sur le port 2377.

- Il a perdu un peu en popularité face à Kubernetes mais c'est très relatif (voir comparaison plus loin).

---

 ## Architecture de Docker Swarm


.col-6[<br>![](img/docker/archi_swarm.png)]

.col-1[<br>]
.col-5[

Noeuds Manager et Worker pour une architecture "master/minion":

- Un ensemble de noeuds de contrôle pour gérer les conteneurs
  
- Un ensemble de noeuds worker pour faire tourner les conteneurs

- Les noeuds Manager et Worker sont en fait identique c'est leur rôles qui varient.
  
]

---

# Conscencus entre swarm manager

- http://thesecretlivesofdata.com/raft/


 - Pas d'intelligent balancing dans swarm
 -> l'algoritme de choix est "spread" c'est à dire répartir au maximum en remplissant tous les noeuds qui remplisse la contrainte.

---

 ## Docker Services and Stacks

- les services : la distribution d'un seul conteneur

- les stacks : des applications distribuées multiconteneurs basées sur docker-compose

---

```yml
version: "3"
services:
  web:
    image: username/repo
    deploy:
      replicas: 5
      resources:
        limits:
          cpus: "0.1"
          memory: 50M
      restart_policy:
        condition: on-failure
    ports:
      - "4000:80"
    networks:
      - webnet
networks:
  webnet:
```

---

# Service discovery

- Par défaut les applications ne sont pas informées du contexte dans lequel elles tournent
--

- La configuration doit être opérée de l'extérieur de l'application
  - par exemple avec des fichiers de configuration
--
 
  - ou des variables d'environnement
--

- La mise en place d'un système de **découverte de service** permet de rendre les applications plus autonomes dans leur (auto)configurations.
--

- Elles vont pouvoir récupérer des information sur leur contexte (dev ou prod, us ou fr?)
--

- Ce type d'automatisation de l'intérieur permet de limiter la complexité du déploiement.


---

# Service Discovery

- Concrêtement un système de découverte de service est un serveur qui est au courant automatiquement:
--

  - de chaque conteneur lancé
--

  - du contexte dans lequel il a été lancé.
--

- Ensuite il suffit aux applications de pouvoir interroger ce serveur pour s'autoconfigurer.
--

- Utiliser un outil dédié permet d'éviter de s'enfermer.

---

# Service Discovery - Solutions

- Le DNS du réseau overlay de Docker Swarm avec des stacks permet une forme extrêmement simple et implicite de service discovery. En résumé, votre application microservice docker compose est automatiquement distribuée.

- Avec le protocole de réseau overlay **Weave Net** il y a de même un service de DNS accessible à chaque conteneur qui permet

- Deux autre solutions populaires mais plus manuelles à mettre en oeuvre :
  - **Consul** (Hashicorp): Assez simple d'installation et fournit avec une sympathique interface web.
  - **Etcd** : A prouvé ses performances aux plus grandes échelle mais un peu plus complexe.

---

# Répartition de charge (load balancing)

- Un load balancer = une sorte d'**"aiguillage" de traffic réseau**, typiquement http(s) ou tcp.
--

- Un aiguillage **intelligent** qui se renseigne sur plusieurs critères avant de choisir la direction.
--

- Cas d'usages:
--

  - Éviter la surcharge : les requêtes sont réparties sur différents backend pour éviter de les saturer.
---

# Loadbalancer suite

- Haute disponibilité : on veut que notre service soit toujours disponible, même en période de panne/maintenance.
  
- Donc on va dupliquer chaque partie de notre service et mettre les différentes instances derrière un load balancer.
  
- Le load balancer va vérifier pour chaque backend s'il est disponible (**healthcheck**) avant de rediriger le traffic.

--

  - Répartition géographique: en fonction de la provenance des requêtes on va rediriger vers un datacenter adapté (+- proche)

---

# Le loadbalancing de Swarm est automatique

- Loadbalancer intégré : Ingress
--

- Permet de router automatiquement le traffic d'un service vers les noeuds qui l'hébergent et sont disponibles.
--

- Pour héberger une production il suffit de rajouter un loadbalancer externe qui pointe vers un certain nombre de noeuds du cluster et le traffic sera routé automatiquement à partir de l'un des noeuds.

---

 # Solutions de loadbalancing Externe

 - **HAProxy** : Le plus répendu en loadbalancing
 - **Traefik** : Simple à configurer
 - **NGINX** : Serveur web générique mais a depuis quelques années des fonctions puissantes de loadbalancing et TCP forwarding.

---

# Gérer les données sensibles dans Swarm avec les secrets Docker

- `echo "This is a secret" | docker secret create my_secret_data`

- `docker service  create --name monservice --secret my_secret_data redis:alpine`
=> monte le contenu secret dans `/var/run/my_secret_data`

---

# Présentation de Kubernetes

.col-6[![](/img/docker/kubernetes.png)]

.col-1[<br>]


.col-5[
  - Les **pods** kubernetes servent à grouper des conteneurs en unité d'application (microservices ou non) fortement couplées
  
- Les **services** sont des groupes de pods exposés à l'extérieur
  
- Les **deployments** sont une abstraction pour scaler ou mettre à jours des groupes de **pods**.

- Ces derniers tendent à se rapprocher d'une VM du point de vue de l'application.

]

---

# Présentation de Kubernetes

- Une autre solution très à la mode depuis 4 ans. Un buzz word du DevOps en France :)

- Une solution **robuste**, **structurante** et **open source** d'orchestration docker.

- Au coeur du consortium **Cloud Native Computing Foundation** très influent dans le monde de l'informatique.

- Hébergeable de façon identique dans le cloud, on-premise ou en mixte.

- Kubernetes a un flat network (un overlay de plus bas niveau que Swarm) : https://neuvector.com/network-security/kubernetes-networking/

---

# Comparer Swarm et Kubernetes

- Swarm plus intégré avec la CLI et le workflow docker.
- Swarm est plus fluide, moins structurant mais moins automatique que Kubernetes.
- Swarm groupes les containers entre eux par **stack** mais c'est un groupement assez lâche à l'aide d'un serveur DNS.
- Kubernetes au contraire créé des **pods** avec une meilleure cohésion qui sont toujours déployés ensembles
  - => Kubernetes à une meilleure fault tolerance que Swarm
  - un service swarm est un seul conteneur répliqué, un service Kubernetes est un groupes de conteneur (pod) répliqué.

---

# Comparer Swarm et Kubernetes

- Kubernetes a plus d'outils intégrés. Il s'agit plus d'un écosystème qui couvre un large panel de cas d'usage.
- Swarm a un mauvais monitoring et le stockage distribué n'est pas intégré de façon standard.
- Swarm est beaucoup plus simple à mettre en oeuvre et plus rapide à migrer qu'une stack Kubernetes.
- Swarm est mieux pour les cluster moyen et Kubernetes pour les très gros ?

---