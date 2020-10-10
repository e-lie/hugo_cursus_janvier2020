---
title: "TP - Créer une application multiconteneur"
draft: false
weight: 45
---

## Articuler deux images avec Docker compose

### Dans une VM

- Si Docker n'est pas déjà installé, installez Docker par la méthode officielle accélérée et moins sécurisée (un _one-liner™_) avec `curl -fsSL https://get.docker.com -o get-docker.sh && sudo sh get-docker.sh`. Que fait cette commande ? Pourquoi est-ce moins sécurisé ?
- Installez VSCode avec la commande `sudo snap install --classic code`
- Installez docker-compose avec `sudo apt install docker-compose`.

<!-- ### Avec Gitpod

`brew update` (si ça reste bloqué plus de 5min, arrêtez avec Ctrl+C)
`brew install docker-compose`
Si la dernière commande ne marche pas, installez `docker-compose` de la façon suivante :

````bash
mkdir bin
curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o bin/docker-compose
chmod +x bin/docker-compose
export PATH="./bin:$PATH"
``` -->

### `identidock` : une application Flask qui se connecte à `redis`

- Démarrez un nouveau projet dans VSCode (créez un dossier appelé `identidock` et chargez-le avec la fonction _Add folder to workspace_)
- Dans un sous-dossier `app`, ajoutez une petite application python en créant ce fichier `identidock.py` :

```python
from flask import Flask, Response, request
import requests
import hashlib
import redis

app = Flask(__name__)
cache = redis.StrictRedis(host='redis', port=6379, db=0)
salt = "UNIQUE_SALT"
default_name = 'Joe Bloggs'

@app.route('/', methods=['GET', 'POST'])
def mainpage():

    name = default_name
    if request.method == 'POST':
        name = request.form['name']

    salted_name = salt + name
    name_hash = hashlib.sha256(salted_name.encode()).hexdigest()
    header = '<html><head><title>Identidock</title></head><body>'
    body = '''<form method="POST">
                Hello <input type="text" name="name" value="{0}">
                <input type="submit" value="submit">
                </form>
                <p>You look like a:
                <img src="/monster/{1}"/>
            '''.format(name, name_hash)
    footer = '</body></html>'
    return header + body + footer


@app.route('/monster/<name>')
def get_identicon(name):

    image = cache.get(name)

    if image is None:
        print ("Cache miss", flush=True)
        r = requests.get('http://dnmonster:8080/monster/' + name + '?size=80')
        image = r.content
    cache.set(name, image)

    return Response(image, mimetype='image/png')

if __name__ == '__main__':
  app.run(debug=True, host='0.0.0.0', port=9090)

```

- `uWSGI` est un serveur python de production très adapté pour servir notre serveur intégré Flask, nous allons l'utiliser.

- Dockerisons maintenant cette nouvelle application avec le Dockerfile suivant :

```Dockerfile
FROM python:3.7

# Ajouter tout le contexte
ADD . .
RUN groupadd -r uwsgi && useradd -r -g uwsgi uwsgi
RUN pip install Flask uWSGI requests redis
WORKDIR /app
COPY app/identidock.py /app

EXPOSE 9090 9191
USER uwsgi
CMD ["uwsgi", "--http", "0.0.0.0:9090", "--wsgi-file", "/app/identidock.py", \
"--callable", "app", "--stats", "0.0.0.0:9191"]
```

- Observons le code du Dockerfile ensemble s'il n'est pas clair pour vous. Juste avant de lancer l'application, nous avons changé d'utilisateur avec l'instruction `USER`, pourquoi ?.

- Construire l'application, pour l'instant avec `docker build`, la lancer et vérifier avec `docker exec`, `whoami` et `id` l'utilisateur avec lequel tourne le conteneur.

#### Le fichier Docker Compose

- A la racine de notre projet `identidock` (à côté du Dockerfile), créez un fichier déclaration de notre application `docker-compose.yml` avec à l'intérieur:

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

- Ajoutons également un conteneur `redis`, la base de données qui sert à mettre en cache les images et à ne pas les recalculer à chaque fois :

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

## Le Docker Compose de `microblog`

<!-- Refaire plutôt avec un wordpress, un ELK, un nextcloud, et le microblog, et traefik, recentraliser les logs -->

<!-- Nous allons ensuite installer le reverse proxy Traefik pour accéder à ces services. -->

<!-- On se propose ici d'essayer de déployer plusieurs services pré-configurés comme le microblog, et d'installer le reverse proxy Traefik pour accéder à ces services. -->

Créons un fichier Docker Compose pour faire fonctionner [l'application Flask finale du TP précédent](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-xix-deployment-on-docker-containers) (à cloner avec `git`) avec MySQL.

### Plein d'autres services

On se propose ici d'essayer de déployer plusieurs services pré-configurés comme Wordpress, Nextcloud et le microblog.

Ensuite, assemblez à partir d'Internet un fichier `docker-compose.yml` permettant de lancer un Wordpress et un Nextcloud **déjà pré-configurés**.

### _Avancé_ : utiliser Traefik pour le routage

Avec l'aide de la documentation Traefik et des labels Traefik ajoutés dans votre `docker-compose.yml` précédent, installer le reverse proxy Traefik pour accéder à ces services. Explorer le dashboard Traefik.

## Une stack Elastic

Testez la stack suivante puis ajoutez un nœud Elastic. A l'aide de la documentation Elasticsearch, vérifiez que ce nouveau nœud communique bien avec le premier.

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

### _Avancé_ : ajouter une stack ELK à `microblog`

Dans la dernière version de l'app `microblog`, Elasticsearch est utilisé pour fournir une fonctionnalité de recherche puissante dans les posts de l'app.
Avec l'aide du [tutoriel de Miguel Grinberg](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-xix-deployment-on-docker-containers), écrivez le `docker-compose.yml` qui permet de lancer une stack entière pour `microblog`. Elle devra contenir un conteneur `microblog`, un conteneur `mysql`, un conteneur `elasticsearch` et un conteneur `kibana`.

### _Avancé_ : centraliser les logs de microblog sur ELK

Avec la [documentation de Filebeat](https://www.elastic.co/guide/en/beats/filebeat/current/configuration-autodiscover.html) et des [hints Filebeat](https://www.elastic.co/guide/en/beats/filebeat/current/configuration-autodiscover-hints.html) ainsi que grâce à [cette page](https://discuss.elastic.co/t/nginx-filebeat-elk-docker-swarm-help/130512/2), trouvez comment centraliser les logs Flask de l'app `microblog` grâce au système de labels Docker de Filebeat.

Tentons de centraliser les logs de
de ces services dans ELK.

---
