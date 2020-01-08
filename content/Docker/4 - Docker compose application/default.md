---
title: 'Docker 4 - Créer une application multiconteneur'
visible: true
---

## Démarrons une nouvelle application Flask 

- Démarrez un nouveau projet dans VSCode (créez un dossier et chargez le avec la fonction `add folder to workspace`)
- Dans un sous dossier `app`, ajoutez une petite application python `identidock.py`:
  
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
app.run(debug=True, host='0.0.0.0')

```

- En vous inspirant du TP précédent (version simple), dockerisez cette application (les dépendances sont
  `Flask==0.10.1 uWSGI==2.0.8 requests==2.5.1 redis==2.10.3`)

- `uWSGI` est un serveur python de production très adapté pour servir des applications flask. Notre serveur intégré flask.
 nous allons l'utiliser.

- Au début du Dockerfile, créez l'instruction pour ajouter un utilisateur et groupe dédié avec `groupadd -r uwsgi && useradd -r -g uwsgi uwsgi`

- Exposez le port `9090` et `9191` plutôt que `5000`.

- Juste avant de lancer l'application, changez d'utilisateur avec l'instruction `USER`.

- A la fin , plutôt que d'utiliser `flask run` pour lancer l'application nous allons appeler uwsgi :

```Dockerfile
CMD ["uwsgi", "--http", "0.0.0.0:9090", "--wsgi-file", "/app/identidock.py", "--callable", "app", "--stats", "0.0.0.0:9191"]
- Juste avant de lancer l'application, changez d'utilisateur avec l'instruction `USER`.

- A la fin , plutôt que d'utiliser `flask run` pour lancer l'application nous allons appeler uwsgi :

```Dockerfile
CMD ["uwsgi", "--http", "0.0.0.0:9090", "--wsgi-file", "/app/identidock.py", "--callable", "app", "--stats", "0.0.0.0:9191"]
```

- Construire l'application, la lancer et vérifier avec `docker exec`,  `whoami` et `id` l'utilisateur avec lequel tourne le conteneur.

## Faire varier la configuration en fonction de l'environnement

Finalement le serveur de développement flask est bien pratique pour debugger en situation de développement bien que pas adapté à la production.
Nous pourrions créer deux images pour les deux situations mais ce serait aller contre l'imperatif DevOps de rapprochement du dév et de la production.

- Créons un script bash `boot.sh` pour adapter le lancement de l'application au contexte:

```bash
#!/bin/bash
set -e
if [ "$ENV" = 'DEV' ]; then
    echo "Running Development Server"
    exec python "/app/identidock.py"
else
    echo "Running Production Server"
    exec uwsgi --http 0.0.0.0:9090 --wsgi-file /app/identidock.py --callable app --stats 0.0.0.0:9191
fi
```

- Déclarez maintenant la variable d'environnement `ENV` avec comme valeur par défaut `PROD` (cela ne sert pas fonctionnellement (else) mais par soucis de déclarativité il est intéressant de l'ajouter).

- Ajoutez une instruction `CMD` pour lancer le script de boot.


## Articuler deux images avec Docker compose

- Observons le code de l'application ensemble s'il n'est pas clair pur vous.

- A la racine de notre projet (à côté du Dockerfile), créez un fichier déclaration de notre application `docker-compose.yml` avec à l'intérieur:
  
```yml
version: '3'
services:
  identidock:
    build: .
    ports:
      - "5000:5000"
    environment:
      ENV: DEV
    volumes:
      - ./app:/app
```

- Plusieurs remarques:
  - la première ligne déclare le conteneur de notre application
  - les lignes suivante permette de décrire comment lancer notre conteneur
  - `build: .` d'abord l'image d'origine de notre conteneur est le résultat de la construction du répertoire courant
  - la ligne suivante décrit le mapping de ports.
  - on définit ensuite la valeur de l'environnement de lancement du conteneur
  - on définit un volume (le dossier `app` dans le conteneur sera le contenu de notre dossier de code)

- Lancez le service (pour le moment mono conteneur) avec `docker-compose up`
- Visitez la page web.
- Essayez de modifier l'application et de recharger la page web. Voilà comment, grâce à un volume on peut développer sans reconstruire l'image à chaque fois !

- Ajoutons maintenant un deuxième conteneur. Nous allons tirer parti d'une image déjà créée qui permet de récupérer une "identicon". Ajoutez à la suite du compose file (attention aux identation !!):

```yml
    links:
      - dnmonster

  dnmonster:
    image: amouat/dnmonster:1.0
```

Cette fois plutôt de construire l'image, nous indiquons de simplement de la récupérer sur le docker hub. Nous ajoutons également un lien qui indique à docker de configurer le réseau convenablement.

- Ajoutons également un conteneur redis:

```yml
    redis:
      image: redis:3.0
```

- Et un deuxième lien `- redis`

- Créez un deuxième fichier compose `docker-compose.prod.yml` pour lancer l'application en configuration de production.

- N'hésitez pas à passer du temps à explorer les options et commande de `docker-compose`. Ainsi que [la documentation du langage (DSL) des compose-file](https://docs.docker.com/compose/compose-file/).


## Assembler l'application Flask avec compose

(Facultatif si vous êtes en avance)

- Créer un fichier docker compose pour faire fonctionner l'application Flask "complexe" du TP précédent (v0.18) avec mysql et elasticsearch.

- Pour elasticsearch utilisez la [documentation officielle pour docker](https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html).

- Vous pouvez ajouter un [kibana](https://www.elastic.co/guide/en/kibana/current/docker.html) (interface web de elasticsearch et bien plus) pour découvrir cet outil de recherche.
  - les paramètres par défaut suffisent pour kibana en mettant simplement le conteneur dans un    réseau commun. Vous pouvez donc supprimez les variables d'environnement de configuration.