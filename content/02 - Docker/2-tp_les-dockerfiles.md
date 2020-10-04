---
title: TP - images et conteneurs
weight: 25
---

# TP time!

## I – Découverte d'une application web flask

- Récupérez d’abord une application Flask exemple en la clonant sur le Bureau :

```bash
git clone https://github.com/miguelgrinberg/microblog/
```

- Déplacez-vous dans le dossier `microblog`

- Nous allons activer une version simple de l’application grâce à git : `git checkout v0.2`
- Ouvrez le dossier microblog cloné avec VSCode (Open Folder). Dans VSCode, vous pouvez faire Terminal > New Terminal pour obtenir un terminal en bas de l'écran.

<!-- - Pour la tester d’abord en local (sans conteneur) nous avons besoin des outils python. Vérifions s'ils sont installés :
    `sudo apt install python-pip python-dev build-essential` -->

<!-- - Créons l’environnement virtuel : `virtualenv -p python3 venv`

- Activons l’environnement : `source venv/bin/activate` -->

<!-- - Installons la librairie `flask` et exportons une variable d’environnement pour déclarer l’application.
    a) `pip install flask`
    b) `export FLASK_APP=microblog.py` -->

<!-- - Maintenant nous pouvons tester l’application en local avec la commande : `flask run` -->

<!-- - Visitez l’application dans le navigateur à l’adresse indiquée. -->

- Observons le code dans VSCode. Qu’est ce qu’un fichier de template ? Où se trouvent les fichiers de templates dans ce projet ?

- Changez le prénom Miguel par le vôtre dans l’application.
<!-- - Relancez l'app flask et testez la modification en rechargeant la page. -->

## II – Passons à Docker

Déployer une application Flask manuellement à chaque fois est relativement pénible. Pour que les dépendances de deux projets Python ne se perturbent pas, il faut normalement utiliser un environnement virtuel `virtualenv` pour séparer ces deux apps.

Nous allons donc construire une image de conteneur pour empaqueter l’application et la manipuler plus facilement. Assurez-vous que Docker est installé.

- Dans le dossier du projet ajoutez un fichier nommé `Dockerfile`

- Ajoutez en haut du fichier : `FROM ubuntu:latest` Cette commande indique que notre conteneur de base est le conteneur officiel Ubuntu.
- Nous pouvons déjà contruire un conteneur à partir de ce modèle Ubuntu vide :
  `docker build -t microblog .`

- Une fois la construction terminée lancez le conteneur.
- Le conteneur s’arrête immédiatement. En effet il ne contient aucune commande bloquante et nous n'avons précisé aucune commande au lancement. Pour pouvoir observer le conteneur convenablement il fautdrait faire tourner quelque chose à l’intérieur. Ajoutez à la fin du fichier la ligne :
  `CMD ["/bin/sleep", "3600"]`
  Cette ligne indique au conteneur d’attendre pendant 3600 secondes comme au TP précédent.

- Reconstruisez le conteneur et relancez-le

- Affichez la liste des conteneurs en train de fonctionner

- Nous allons maintenant rentrer dans le conteneur en ligne de commande pour observer. Utilisez la commande : `docker exec -it <id_du_conteneur> /bin/bash`

- Vous êtes maintenant dans le conteneur avec une invite de commande. Utilisez quelques commandes Linux pour le visiter rapidement (`ls`, `cd`...).

- Il s’agit d’un Linux standard, mais il n’est pas conçu pour être utilisé comme un système complet, juste pour une application isolée. Il faut maintenant ajouter notre application flask à l’intérieur. Dans le Dockerfile supprimez la ligne CMD, puis ajoutez :

  - `RUN apt-get update -y`
  - `RUN apt-get install -y python-pip python-dev build-essential`

- Reconstruisez votre conteneur. Si tout se passe bien, poursuivez.

- Pour installer les dépendances python et configurer la variable d'environnement Flask ajoutez:

  - `RUN pip install flask`
  - `ENV FLASK_APP microblog.py`

- Ensuite, copions le code de l’application à l’intérieur du conteneur. Pour cela ajoutez les lignes :

```Dockerfile
COPY . /app
WORKDIR /app
```

Ces deux lignes indiquent de copier tout le contenu du dossier courant sur l'hôte dans un dossier /app à l’intérieur du conteneur. Puis le dossier courant dans le conteneur est déplacé à `/app`.

- Ensuite, pour démarrer l’application nous aurons besoin d’un script de boot. Créez un fichier `boot.sh` dans `app` avec à l’intérieur :

```bash
#!/bin/sh
flask run -h 0.0.0.0
```

- Enfin, ajoutons la section de démarrage à la fin du Dockerfile :

```Dockerfile
RUN chmod +x boot.sh
ENTRYPOINT ["./boot.sh"]
```

- Reconstruisez le conteneur et lancez-le en ouvrant le port `5000` avec la commande : `docker run -d -p 5000:5000 microblog`

- Naviguez dans le navigateur à l’adresse `localhost:5000` pour admirer le prototype microblog.

- Lancez un deuxième container cette fois avec : `docker run -d -p 5001:5000 microblog`

- Une deuxième instance de l’app est maintenant en fonctionnement et accessible à l’adresse localhost:5001

## Observons et optimisons l'image

<!-- - Changez le contenu du fichier `requirements.txt` puis relancez le build. -->

- Changez le contenu d'un des fichiers python de l'application et relancez le build.

- Observez comme le build recommence à partir de l'instruction modifiée. Les layers précédents sont mis en cache par le Docker Engine

<!-- - Pour optimiser, rassemblez les commandes `RUN` liées à apt-get en une seule commande avec `&&` et sur plusieurs lignes avec `\`. -->

<!-- - Re-testez votre image. -->

<!-- - Finalement, avons nous besoin d'un **virtualenv** à l'intérieur d'un **conteneur Docker** ? C'est redondant : le conteneur isole déjà les dépendances d'une seule application.
  - modifier le Dockerfile pour installer les dépendances directement dans l'OS du conteneur (sur une seule ligne). -->

- Ajoutez après le `FROM` du `Dockerfile` une commande `LABEL maintainer=<votre_nom>`

- Ajoutez une commande `LABEL version=<votre_version>`

<!-- - Le shell par défaut de Docker est `SHELL ["/bin/sh", "-c"]`. Cependant ce shell a certains comportement inhabituels et la commande de construction **n'échoue pas forcément** si une commande s'est mal passée. Dans une optique d'intégration continue, pour rendre la modification ultérieure de l'image plus sûre ajoutez au début (en dessous des `LABEL`) `SHELL ["/bin/bash", "-eux", "-o", "pipefail", "-c"]`.

- Pour autant, le shell bash est non standard sur docker. Pour ne pas perturber les utilisateurs de l'image qui voudraient lancer des commande il peut être intéressant de rebasculer sur `sh` à la fin de la construction. Ajoutez à l'avant dernière ligne: `SHELL ["/bin/sh", "-c"]` -->

- Visitons **en root** (`sudo su`) le dossier `/var/lib/docker/` sur l'hôte. En particulier, `image/overlay2/layerdb/sha256/` :
  - On y trouve une sorte de base de données de tous les layers d'images avec leurs ancêtres.
  - Il s'agit d'une arborescence.

<!-- - Pour explorer la hiérarchie des images vous pouvez installer `https://github.com/wagoodman/dive` -->

- Trouvez la commande pour pousser l'image `microblog` sur le Docker Hub. Créez un compte le cas échéant.

- Affichez la liste des images présentes dans votre Docker Engine.

- Inspectez la dernière image que vous venez de créez.

- Observez l'historique de construction de l'image avec `docker image history <image>`

<!-- - Committez les modifications de votre dépôt avec `git` (faire le commit en local est suffisant). -->

- _(Facultatif)_ En suivant [les instructions du site officiel](https://docs.docker.com/registry/deploying/), créez votre propre registry.
- Puis trouvez les commandes pour le configurer et poussez-y une image dessus.

## Une application Flask qui se connecte à `redis`

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

#Ajouter tout le contexte
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

## Faire varier la configuration en fonction de l'environnement

Le serveur de développement Flask est bien pratique pour debugger en situation de développement, mais n'est pas adapté à la production.
Nous pourrions créer deux images pour les deux situations mais ce serait aller contre l'impératif DevOps de rapprochement du dev et de la prod.

- Créons à la racine du projet (pas dans `app`) un script bash `boot.sh` pour adapter le lancement de l'application au contexte :

```bash
#!/bin/bash
set -e
if [ "$APP_ENVIRONMENT" = 'DEV' ]; then
    echo "Running Development Server"
    exec python "/app/identidock.py"
else
    echo "Running Production Server"
    exec uwsgi --http 0.0.0.0:9090 --wsgi-file /app/identidock.py --callable app --stats 0.0.0.0:9191
fi
```

- Déclarez maintenant dans le Dockerfile la variable d'environnement `APP_ENVIRONMENT` avec comme valeur par défaut `PROD`.

- Ajoutez une instruction pour copier le script de boot, le rendre exécutable, puis remplacez l'instruction `CMD` pour lancer le script de boot à la place.

- Lançons le conteneur, puis le conteneur redis dont l'app a besoin.

<!-- ## Faire parler la vache
- Changez de répertoire et créez un nouveau Dockerfile qui permet de faire dire des choses à une vache grâce à la commande `cowsay`. Indice : utilisez la commande `ENTRYPOINT`.
Le but est de la faire fonctionner dans un conteneur à partir de commandes de type :
- `docker run cowsay Coucou !`
- `docker run cowsay Salut !`
- `docker run cowsay Bonjour !` -->

<!-- Faites que l'image soit la plus légère possible en utilisant l'image de base `alpine`. Attention, alpine possède des commandes légèrement différentes (`apk add` pour installer) et la plupart des programmes nes ont pas installés par défaut. -->

## L'instruction HEALTHCHECK

`HEALTHCHECK` permet de vérifier si l'app contenue dans un conteneur est en bonne santé.

- Dans un nouveau répertoire, créez un fichier `Dockerfile` dont le contenu est le suivant :

```Dockerfile
FROM ubuntu:16.04

RUN apt-get update && apt-get -y upgrade
RUN apt-get -y install python-pip curl
RUN pip install flask==0.10.1

ADD /app.py /app/app.py
WORKDIR /app

HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit 1

CMD python app.py
```

- Créez aussi un fichier `app.py` avec ce contenu :

```python
from flask import Flask

healthy = True

app = Flask(__name__)

@app.route('/health')
def health():
    global healthy

    if healthy:
        return 'OK', 200
    else:
        return 'NOT OK', 500

@app.route('/kill')
def kill():
    global healthy
    healthy = False
    return 'You have killed your app.', 200


if __name__ == "__main__":
    app.run(host="0.0.0.0")
```

- Observez bien le code Python et la ligne `HEALTHCHECK` du `Dockerfile` puis lancez l'app. A l'aide de `docker ps`, relevez où Docker indique la santé de votre app.
- Visitez l'URL `/kill` de votre app dans un navigateur. Refaites `docker ps`. Que s'est-il passé ?

## Une version du microblog plus complexe avec MySQL

_Facultatif : si vous êtes en avance_

- Basculez sur la version plus complexe : `git checkout v0.18`
- Suivez le [tutoriel de Miguel Grinberg](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-xix-deployment-on-docker-containers) pour packager cette version plus complexe avec un MySQL (à partir du paragraphe _Using Third-Party "Containerized" Services"_, mais en **s'arrêtant avant la partie finale sur Elasticsearch**), et adaptez/simplifiez si besoin.

<!-- ## Toujours plus loin : avec une base de données Elasticsearch

- **Facultatif :** suivez la fin du [tutoriel de Miguel Grinberg sur l'application Flask](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-xix-deployment-on-docker-containers) pour configurer Elasticsearch. -->
