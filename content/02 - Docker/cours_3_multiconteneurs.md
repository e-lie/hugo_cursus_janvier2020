---
title: Cours 3 - description multiconteneur
draft: false
---


- Nous avons pu constater que lancer plusieurs conteneurs liés avec leur mapping réseaux et liens implique des commandes assez lourdes. Cela devient ingérable si l'ont a de grosses applications microservice avec des réseaux et des volumes spécifiques.

- Pour faciliter tout cela et dans l'optique de l'**Infrastructure as Code**, docker introduit un outil nommé **docker-compose** qui permet décrire de applications multiconteneurs grâce à des fichiers **YAML**



```yml
version: '2.2'
services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:6.7.1
    container_name: elasticsearch
    environment:
      - cluster.name=docker-cluster
    volumes:
      - esdata1:/usr/share/elasticsearch/data
    ports:
      - 9200:9200
    networks:
      - esnet
  elasticsearch2:
    ...

volumes:
  esdata1:
    driver: local
  ...

networks:
  esnet:
```



## Le workflow de Docker Compose

Les commandes suivantes sont couramment utilisées lorsque vous travaillez avec Compose. 

- `up` démarre tous les conteneurs définis dans le fichier compose et agrège la sortie des logs. Normalement, vous voudrez utiliser l'argument `-d` pour exécuter Compose en arrière-plan.
  
- `build` reconstruit toutes les images créées à partir de Dockerfiles. La commande up ne construira pas une image à moins qu'elle n'existe pas, donc utilisez cette commande à chaque fois que vous avez besoin de mettre à jour une image.

- `ps` Fournit des informations sur le statut des conteneurs gérés par Compose.


- `run` Fait tourner un conteneur pour exécuter une commande unique. Cela aura aussi pour effet de faire tourner tout conteneur lié à moins que l'argument --no-deps ne soit donné.

- De façon générale la sortie des logs est colorés et agrégés pour les conteneurs gérés par Compose-Managed.
  
- `stop` Arrête les conteneurs sans les enlever.

- `rm` Enlève les contenants à l'arrêt. N'oubliez pas d'utiliser l'argument `-v` pour supprimer tout les volumes gérés par docker.


## Visualisation des applications Microservice complexes

- Certaines applications microservice peuvent avoir potentiellement des dizaines de petits conteneurs spécialisés. Le service devient alors difficile à lire dans le compose file.

- Il est possible de visualiser l'architecture d'un fichier Docker Compose en utilisant docker-compose-viz (https://github.com/pmsipilot/docker-compose-viz]

- Cet outil peut-être utilisé dans le cadre d'un automatisation d'intégration continue pour produire la documentation agrémentée d'une image automatiquement en fonction du code.


## Un GUI d'admin simple pour Docker (et Swarm): Portainer

Un bonne façon de visualiser l'état d'un application complexe est d'utiliser une application classique d'admin Docker: Portainer.

Elle s'installer sous forme d'un conteneur (avec un volume pour pas perdre les données):

```bash
docker run --detach --name portainer \
    -p 9000:9000 \
    -v portainer_data:/data \
    -v /var/run/docker.sock:/var/run/docker.sock \
    portainer/portainer
```

Pour administrer l'engine docker (ou le cluster) une façon simple de procéder est de donner au conteneur portainer l'accès au socket docker sous forme d'un bind mount (`-v /var/run/docker.sock:/var/run/docker.sock`). On parle alors d'accès en local. Sur un cluster on connecte généralement portainer en mode remote sur le socket du cluster.

Comme portainer peut virtuellement tout faire sur votre cluster et donc sur les machines hôtes, il faut bien sécuriser l'interface par exemple la laisser dans un réseau privé et activer le RBAC (role based acces control (payant)).