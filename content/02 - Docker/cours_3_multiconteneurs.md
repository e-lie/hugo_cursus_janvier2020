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

# Docker Compose

---

# Docker compose

- Nous avons pu constater que lancer plusieurs conteneurs liés avec leur mapping réseaux et liens implique des commandes assez lourdes. Cela devient ingérable si l'ont a de grosses applications microservice avec des réseaux et des volumes spécifiques.

- Pour faciliter tout cela et dans l'optique de l'**Infrastructure as Code**, docker introduit un outil nommé **docker-compose** qui permet décrire de applications multiconteneurs grâce à des fichiers **YAML**

---

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

---

## Le workflow de Docker Compose

Les commandes suivantes sont couramment utilisées lorsque vous travaillez avec Compose. La plupart se passent d'explications et ont des équivalents Docker directs, mais il vaut la peine d'en être conscient :

- `up` démarre tous les conteneurs définis dans le fichier compose et agrège la sortie des logs. Normalement, vous voudrez utiliser l'argument -d pour exécuter Compose en arrière-plan.
  
- `build` reconstruit toutes les images créées à partir de Dockerfiles. La commande up ne construira pas une image à moins qu'elle n'existe pas, donc utilisez cette commande à chaque fois que vous avez besoin de mettre à jour une image.

---

- `ps` Fournit des informations sur le statut des conteneurs gérés par Compose.


- `run` Fait tourner un conteneur pour exécuter une commande unique. Cela aura aussi pour effet de faire tourner tout conteneur lié à moins que l'argument --no-deps ne soit donné.

- De façon générale la sortie des logs est colorés et agrégés pour les conteneurs gérés par Compose-Managed.
  
- `stop` Arrête les conteneurs sans les enlever.

- `rm` Enlève les contenants à l'arrêt. N'oubliez pas d'utiliser l'argument `-v` pour supprimer tout les volumes gérés par docker.

---

# Visualisation des applications Microservice complexes

- Certaines applications microservice peuvent avoir potentiellement des dizaines de petits conteneurs spécialisés. Le service devient alors difficile à lire dans le compose file.

- Il est possible de visualiser l'architecture d'un fichier Docker Compose en utilisant docker-compose-viz (https://github.com/pmsipilot/docker-compose-viz]

- Cet outil peut-être utilisé dans le cadre d'un automatisation d'intégration continue pour produire la documentation agrémentée d'une image automatiquement en fonction du code.

