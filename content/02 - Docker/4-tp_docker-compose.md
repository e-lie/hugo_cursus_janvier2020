---
title: "TP - Créer une application multiconteneur"
draft: false
weight: 45
---

## TP 1 : Articuler deux images avec Docker compose

### Dans une VM

- Installez docker-compose avec `sudo apt install docker-compose`.

### Avec Gitpod

<!-- `brew update` (si ça reste bloqué plus de 5min, arrêtez avec Ctrl+C)
`brew install docker-compose`
Si la dernière commande ne marche pas, installez `docker-compose` de la façon suivante : -->

```bash
mkdir bin
curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o bin/docker-compose
chmod +x bin/docker-compose
export PATH="./bin:$PATH"
```

- A la racine de notre projet précédent `identidock` (à côté du Dockerfile), créez un fichier déclaration de notre application `docker-compose.yml` avec à l'intérieur:

```yml
version: "2"
services:
  identidock:
    build: .
    ports:
      - "9090:9090"
    environment:
      APP_ENVIRONMENT: DEV
    volumes:
      - ./app:/app
```

- Plusieurs remarques :

  - la première ligne déclare le conteneur de notre application
  - les lignes suivantes permettent de décrire comment lancer notre conteneur
  - `build: .` d'abord l'image d'origine de notre conteneur est le résultat de la construction du répertoire courant
  - la ligne suivante décrit le mapping de ports.
  - on définit ensuite la valeur de l'environnement de lancement du conteneur
  - on définit un volume (le dossier `app` dans le conteneur sera le contenu de notre dossier de code)

  - n'hésitez pas à passer du temps à explorer les options et commandes de `docker-compose`. Ainsi que [la documentation du langage (DSL) des Compose files](https://docs.docker.com/compose/compose-file/).

- Lancez le service (pour le moment mono-conteneur) avec `docker-compose up`
- Visitez la page web.
- Essayez de modifier l'application et de recharger la page web. Voilà comment, grâce à un volume on peut développer sans reconstruire l'image à chaque fois !

- Ajoutons maintenant un deuxième conteneur. Nous allons tirer parti d'une image déjà créée qui permet de récupérer une "identicon". Ajoutez à la suite du fichier Compose **_(attention aux indentations !)_** :

```yml
version: "2"
services:
  identidock:
    build: .
    ports:
      - "9090:9090"
    environment:
      APP_ENVIRONMENT: DEV
    volumes:
      - ./app:/app

    links:
      - dnmonster

  dnmonster:
    image: amouat/dnmonster:1.0
```

Cette fois plutôt de construire l'image, nous indiquons simplement comment la récupérer sur le Docker Hub. Nous ajoutons également un lien qui indique à Docker de configurer le réseau convenablement.

- Ajoutons également un conteneur redis, la base de données qui sert à mettre en cache les images et à ne pas les recalculer à chaque fois :

```yml
version: "2"
services:
  identidock:
    build: .
    ports:
      - "9090:9090"
    environment:
      APP_ENVIRONMENT: DEV
    volumes:
      - ./app:/app

    links:
      - dnmonster

  dnmonster:
    image: amouat/dnmonster:1.0

  redis:
    image: redis:3.0
```

- Et un deuxième lien `- redis` **_(attention aux indentations !)_**.

- Créez un deuxième fichier compose `docker-compose.prod.yml` pour lancer l'application en configuration de production.

- Vérifiez dans les logs de l'application quand l'image a été générée et quand elle est bien mise en cache dans redis.

- N'hésitez pas à passer du temps à explorer les options et commandes de `docker-compose`. Ainsi que [la documentation du langage (DSL) des Compose files](https://docs.docker.com/compose/compose-file/).

## TP 2 : Déployons plusieurs services avec Docker-Compose et Traefik

<!-- Refaire plutôt avec un wordpress, un ELK, un nextcloud, et le microblog, et traefik, recentraliser les logs -->

<!-- On se propose ici d'essayer de déployer plusieurs services pré-configurés comme Wordpress, Nextcloud et le microblog, et d'installer le reverse proxy Traefik pour accéder à ces services. -->

On se propose ici d'essayer de déployer plusieurs services pré-configurés comme le microblog, et d'installer le reverse proxy Traefik pour accéder à ces services.
Créons un fichier Docker Compose pour faire fonctionner [l'application Flask finale du TP précédent](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-xix-deployment-on-docker-containers) (à partir du tag git `v0.18`) avec MySQL.

### Si vous êtes en avance

Assemblez à partir d'Internet un fichier `docker-compose.yml` permettant de lancer un Wordpress et un Nextcloud **déjà pré-configurés**.

### Si vous êtes en avance : ajouter ELK et centraliser les logs

Avec la [documentation de Filebeat](https://www.elastic.co/guide/en/beats/filebeat/current/configuration-autodiscover.html) et des [hints Filebeat](https://www.elastic.co/guide/en/beats/filebeat/current/configuration-autodiscover-hints.html) ainsi que grâce à [cette page](https://discuss.elastic.co/t/nginx-filebeat-elk-docker-swarm-help/130512/2), trouvez comment centraliser les logs Flask de l'app `microblog` grâce au système de labels Docker de Filebeat.

Tentons de centraliser les logs de
de ces services dans ELK.

```yaml
version: "3"

services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.5.0
    environment:
      - discovery.type=single-node
      - xpack.security.enabled=false
    networks:
      - logging-network

  filebeat:
    image: docker.elastic.co/beats/filebeat:7.5.0
    user: root
    depends_on:
      - elasticsearch
    volumes:
      - ./filebeat.yml:/usr/share/filebeat/filebeat.yml:ro
      - /var/lib/docker/containers:/var/lib/docker/containers:ro
      - /var/run/docker.sock:/var/run/docker.sock:ro
    networks:
      - logging-network
    environment:
      - -strict.perms=false

  kibana:
    image: docker.elastic.co/kibana/kibana:7.5.0
    depends_on:
      - elasticsearch
    ports:
      - 5601:5601
    networks:
      - logging-network

networks:
  logging-network:
    driver: bridge
```

---