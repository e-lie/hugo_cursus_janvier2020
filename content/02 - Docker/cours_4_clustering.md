---
title: Cours 4 - Swarm, orchestration et clustering 
draft: false
---


## L'orchestration

Un des intérêts principaux de Docker et des conteneurs en général est de:
  - favoriser la modularité et les architectures microservice.
  - permettre la scalabilité horizontale des applications en multipliant les conteneurs.

A partir d'une certaine échelle, il n'est plus question de gérer les serveurs et leurs conteneurs à la main.

Traditionnellement, l'**orchestration** consiste à automatiser la manipulation de nombreux serveurs pour gérer généralement un ensemble de services applicatifs de grande taille.
Il s'agit donc de développer des opération automatiques et synchronisées pour :

- Déployer des programmes divers sur différents type de serveurs (traditionnellement on parle d'infra multitiers).
- Gérer le trafic réseau entre plusieurs machines de backend et frontend.
- Etre capable de faire évoluer l'ensemble applicatif et d'opérations sans interrompre le service en particulier déployer de nouvelles versions d'une application progressivement (rolling upgrade).
- Faire grandir la quantité d'instances de chaque application facilement.
- Voir dans le cas de l'auto-scaling de faire grossir l'application automatiquement en fonction de la demande.
  
Nous avons vu rapidement que des outils comme Ansible pouvaient aider à :
  - **"bootstraper"** rapidement des environnements multitiers
  - s'assurer que la configuration est stable dans le temps et maîtrisée
  - **contrôler** des applications (conteneurisées ou non) assez **statiquement**

Cependant ce n'est pas idéal car
  - on ne réduit pas la complexité de l'application
  - on ne fournit pas de garantie que les opérations complexes d'orchestration sont implémentées correctement
  - il faut beaucoup de savoir faire pour implémenter une gestion correcte d'une application distribuée de grande taille

L'**orchestration modulaire immutable** est la raison d'exister des conteneurs (Docker, Rkt etc).

Concrêtement l'orchestration de conteneurs consiste à :

- Fournir à un cluster une description de quelques applications distribuées contenant plusieurs types de conteneurs applicatifs fonctionnant de concert.
  - frontends et backend applicatifs (idéalement stateless et microservice car plus simple mais pas obligatoire)
  - bases de données (statefull) idéalement distribuée grâce à un sharding intelligent (mongoDB, PGSQL cluster, cockroachDB, etc)
  - des outils autour de l'application (monitoring, security hardening, outils de DevOps, etc.)

- A partir de cette description laisser le cluster gérer automatiquement le placement, la durée de vie des conteneurs, leurs communications réseau, etc.

Le but est de permettre la manipulation dynamique et sure de d'application distribuées :

- Uniformiser et simplifier la gestion de l'infrastructure
- Diminuer le taux d'erreurs grace à cette simplification et automatisation
- Maîtriser l'aspect distribué d'une large application grâce à des outils éprouvés (ne pas réinventer des fusées spatiales dans son garage)

Cette approche, lorsque **correctement implémentée**, permet d'agrandir horizontalement (scaling out) sans effort une application complexe.


## Docker Swarm ou swarm mode de Docker 

Swarm est l'**outil de clustering et d'orchestration natif** développé par Docker Inc. autour du Docker engine pour déployer et orchestrer des stacks Docker. Depuis 2017, docker swarm est intégré directement dans le docker engine, on parle de **Swarm mode** de Docker.

Docker Swarm est donc facile à mettre en place et intégré au workflow docker => plus facile à installer et à apprendre au départ.

- Il s'intègre très bien avec les autre commande docker (on a même pas l'impression de faire du clustering).
- Il permet de gérer de très grosses productions Docker.
- Swarm utilise l'API standard du Docker Engine (sur le port 2376) et son API de management swarm sur le port 2377.

Depuis fin 2019, Docker Desktop intègre également une distribution de Kubernetes signe que ce dernier est devenu incontournable.

 ## Architecture de Docker Swarm

![](../../images/docker/archi_swarm.png)

Noeuds Manager et Worker pour une architecture "master/minion":

- Un ensemble de noeuds de contrôle pour gérer les conteneurs
- Un ensemble de noeuds worker pour faire tourner les conteneurs
- Les noeuds Manager et Worker sont en fait identique c'est leur rôles qui varient.
- On peut prendre un noeud Worker et le promouvoir en manager et inversement.

### Conscencus entre swarm managers

Pour voir un cluster haute disponibilité qui résiste à la mise hors service de ressources il faut multiplier les managers.
Ensuite en cas de chute du leader (le manager principal) il faut savoir qui a raison parmis les managers restant.

- http://thesecretlivesofdata.com/raft/

## Créer un cluster Docker Swarm

- Pour créer un swarm il faut simplement **un ou plusieurs serveurs avec docker** installé et configuré.
- Ensuite on initialise le swarm sur un des noeuds avec `docker swarm init`. Ce noeud devient le premier manager.
- On peut utiliser un cluster à un seul noeud pour tester des stacks applicatives docker mais alors pas de haute disponibilité.
- Cette commande se charge aussi d'ouvrir l'accès au port swarm 2377 et à API Docker du manager 2376.
- Il faut ensuite éventuellement attacher d'autres noeuds. Lancer depuis un autre noeud la commande `docker swarm join --token SWMTKN-1-30c3zzjk0861pahlfpar1umomur1tbmm53dttf9qmw855wq8t4-6la83l2h9fhmhdpbwnumbbvar 192.168.43.234:2377` )
- Enfin on peut promouvoir d'autres noeuds en manager  (`docker node demote <node>`) pour la haute disponibilité.

Doc :

- [Manage nodes in a swarm](https://docs.docker.com/engine/swarm/manage-nodes/)
- [ancien quickstart swarm très instructif](https://web.archive.org/web/20190322051605/https://docs.docker.com/get-started/part4/)

### Docker Machine

Une façon simple de créer un cluster Docker en mode Swarm est **Docker Machine**.
C'est l'outil officiel de gestion d'hôtes docker.

- Il est capable de créer des serveurs docker à la volée
  - chez différents fournisseurs de cloud
  - en local avec virtualbox pour tester son application en mode production et/ou distribué.
- L'intérêt de `docker-machine` est d'être **parfaitement intégré** avec l'outil de CLI Docker.

Concrêtement, `docker-machine` permet de **créer automatiquement des machines** avec le **docker engine** et **ssh** configurés et de gérer les **certificats TLS** pour se connecter à l'API docker des différents serveurs.
- Il permet également de changer facilement le contexte du client en ligne de commande docker avec des variables d'environnement pour basculer sur l'un ou l'autre serveur
- Il permet enfin de se connecter à une machine en ssh en une simple commande.

Exemple :

```bash
 docker-machine create  --driver digitalocean \
      --digitalocean-ssh-key-fingerprint 41:d9:ad:ba:e0:32:73:58:4f:09:28:15:f2:1d:ae:5c \
      --digitalocean-access-token "a94008870c9745febbb2bb84b01d16b6bf837b4e0ce9b516dbcaf4e7d5ff2d6" \
      identihost-do
```

Pour basculer `eval $(docker env identihost-do);`

- `docker run -d nginx:latest -v /path/website:/usr/share/nginx/html` créé ensuite un conteneur **sur le droplet digitalocean** précédemment créé.

- `docker ps -a` affiche le conteneur en train de tourner à distance.
- `wget $(docker-machine ip identihost-do)` va récupérer ip du noeud pour télécharger la page web.


### Installer un cluster Swarm avec Ansible

Pourquoi privilégier cette approche ?

- Docker machine propose une approche en ligne de commande qui n'est pas conforme avec l'IaC et le versionning des opérations.
- Docker machine est paramétrable et s'intègre avec les différents provider de cloud mais se cantonne au périmètre de Docker:
  - Il est limité et ne permet de configuration très spécifique
  - Il doit donc être complémenté par un outil de gestion de configuration (comme Ansible)

Dans le TP5 nous installerons donc un cluster à l'aide d'un role Ansible.

## Ressources Docker Swarm

Globalement le Swarm mode de docker introduit 4 nouvelles sous commandes et 3 types principaux de ressources :

- les nodes
- les services
- les stacks

 ## Docker Nodes

 Les noeuds ajoutés au cluster sont manipulables avec la sous commande  `docker node ...`.

 ## Docker Services
 
 En mode swarm, docker ne gère plus les conteneurs un par un mais sour forme de services répliqués. `docker service create` ressemble beaucoup à `docker run` mais on peut préciser le nombre d'instances identiques à lancer, le placement sur le cluster etc.

 `docker service create --name monster_icon -p 9090:9090 --replicas 3 tecpi/monster_icon:0.1`
 
 ## Docker Stacks

De la même façon qu'on utilise docker compose pour décrire un déploiement de développement multiconteneur, Docker Swarm propose le concept de **stack** qui consiste en la description en YAML d'un ensemble de services répliqués et déployés d'un certaine façon. En simplifié un fichier de stack docker est simplement un docker-compose file, en version 3 avec une section `deploy` pour chaque service du type:

```yml
version: '3'
services:
  monster_icon:
    image: tecpi/monster_icon:0.1
    ports:
      - "9090:9090"
      - "9191:9191"
    environment:
      - CONTEXT=PROD
    networks:
      - monster_network
    deploy:
      replicas: 5
      resources:
        limits:
          cpus: "0.1"
          memory: 50M
      restart_policy:
        condition: on-failure
      placement:
        constraints:
          - node.role == manager
          - engine.labels.operatingsystem == ubuntu 18.04

networks:
  monster_network:
    driver: overlay
```



## TP5