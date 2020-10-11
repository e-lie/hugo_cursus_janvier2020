---
title: TP - Orchestration et clustering
weight: 55
---

<!-- - Cloner l'application exemple ici : [https://gitlab.com/e-lie/getstarted_docker.git](https://gitlab.com/e-lie/getstarted_docker.git) -->

## Introduction à Swarm

- Cloner l'application `example-voting-app` ici : [https://github.com/dockersamples/example-voting-app](https://github.com/dockersamples/example-voting-app)

- Lire le schéma d'architecture de l'app `example-voting-app` sur Github. A noter que les deux services de front-ends et le back-end existent en deux versions d'image : Python et .NET. Ces deux versions peuvent être déployées de manière interchangeable et ne modifient pas le fonctionnement de l'application multi-conteneur. C'est une démonstration de l'utilité du paradigme de la conteneurisation et de l'architecture dite "micro-service".

- Lire attentivement les fichiers `docker-compose.yml`, `docker-compose-simple.yml`, `docker-stack-simple.yml` et `docker-stack.yml`. Ce sont tous des fichiers Docker Compose classiques avec différentes options liées à un déploiement via Swarm. Quelles options semblent spécifiques à Docker Swarm ? Ces options permettent de configurer des fonctionnalités d'**orchestration**.

- Dessiner rapidement le schéma d'architecture associé au fichier `docker-compose-simple.yml`, puis celui associé à `docker-stack.yml` en indiquant bien à quel réseau quel service appartient.
<!-- - En suivant le [guide Docker de découverte de Swarm à partir de la partie 4](https://docs.docker.com/get-started/part4/), créez un fichier docker-compose qui package l'application exemple avec un container `redis` joignable via le hostname `redis` et le port 6379. -->

- Avec `docker swarm init | tee swarminit.txt`, transformer son installation Docker en une installation Docker compatible avec Swarm. Lisez attentivement le message qui vous est renvoyé. En pipant le résultat de la commande dans `tee`, nous l'avons enregistré dans un fichier texte car il sera utile plus tard.

- Déployer la stack du fichier `docker-stack.yml` : `docker stack deploy --compose-file docker-stack.yml vote`

- `docker stack ls` indique 6 services pour la stack `vote`. Observer également l'output de `docker stack ps vote` et de `docker stack services vote`. Qu'est-ce qu'un service dans la terminologie de Swarm ?

- Accéder aux différents front-ends de la stack grâce aux informations contenues dans les commandes précédentes. Sur le front-end lié au vote, actualiser plusieurs fois la page. Que signifie la ligne `Processed by container ID […]` ? Pourquoi varie-t-elle ?

- Scaler la stack en ajoutant des _replicas_ du front-end lié au vote avec l'aide de `docker service --help`. Accédez à ce front-end et vérifier que cela a bien fonctionné en actualisant plusieurs fois.

- Trouver la commande pour promouvoir (_promote_) un worker en manager

- Puis déchoir (_demote_) le manager pour le sortir du cluster (drain) : `docker node update --availability drain <node-name>`
<!-- - Comment ne pas exposer les ports de tous nos hôtes à tout l'internet ? -->

## Clustering

- En vous groupant par 2 ou 3 et avec l'aide des instructions notées lors de l'étape `docker swarm init`, créer un cluster de 2 ou 3 machines avec Docker Swarm (vous aurez sans doute à modifier l'IP externe par rapport aux instructions). Vous pouvez faire `docker swarm --help` pour obtenir des infos manquantes, ou faire `docker swarm leave --force` pour réinitialiser votre configuration Docker Swarm.

- Déchoir et promouvoir l'un de vos nœuds de `manager` à `worker` et vice-versa.

- Avec Portainer ou avec [docker-swarm-visualizer](https://github.com/dockersamples/docker-swarm-visualizer), explorer le cluster ainsi créé (le fichier `docker-stack.yml` de l'app `example-voting-app` contient déjà un exemplaire de `docker-swarm-visualizer`).

## Scaler une autre stack

- Prendre une stack Docker Compose du TP 4 et scalez-la (3 _replicas_) avec Swarm.
- Ajouter dans le Docker Compose des instructions pour scaler différemment deux services (1 _replica_ pour la base de données et 3 pour le service _front_ par exemple)

<!--
## Installons Portainer

Portainer est une interface web de base pour gérer un cluster docker.

```bash
docker service create \
      --name portainer \
      --publish 9000:9000 \
      --constraint 'node.role == manager' \
      --mount type=bind,src=/var/run/docker.sock,dst=/var/run/docker.sock \
      portainer/portainer \
      -H unix:///var/run/docker.sock
```

- Listez les services
- Inspectez le service portainer avec l'option --pretty
- Ouvrez la page avec `firefox http://$(docker-machine ip <machine_manager>):9000` -->

<!-- # Installer un loadbalancer HAProxy

- [https://github.com/docker/dockercloud-haproxy/tree/master](https://github.com/docker/dockercloud-haproxy/tree/master) -->
