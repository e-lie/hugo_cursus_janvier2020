---
title: TP - Images et conteneurs
weight: 25
---

# TP time!

## I – Découverte d'une application web flask

- Récupérez d’abord une application Flask exemple en la clonant :

```bash
git clone https://github.com/miguelgrinberg/microblog/
```

- Déplacez-vous dans le dossier `microblog`

<!-- - Nous allons activer une version non dockerisée de l’application grâce à git : `git checkout v0.18` -->

- Nous allons activer une version simple de l’application grâce à git : `git checkout v0.2`

<!-- - Ouvrez le dossier microblog cloné avec VSCode (Open Folder). Dans VSCode, vous pouvez faire Terminal > New Terminal pour obtenir un terminal en bas de l'écran. -->

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
<!-- prendre une autre image ? alpine ? -->

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

- Il s’agit d’un Linux standard, mais il n’est pas conçu pour être utilisé comme un système complet, juste pour une application isolée. Il faut maintenant ajouter notre application Flask à l’intérieur. Dans le Dockerfile supprimez la ligne CMD, puis ajoutez :

  - `RUN apt-get update -y`
  - `RUN apt-get install -y python3-pip`
  <!-- - `RUN apt-get install -y python3-pip python-dev build-essential` -->

- Reconstruisez votre conteneur. Si tout se passe bien, poursuivez.

- Pour installer les dépendances python et configurer la variable d'environnement Flask ajoutez:

  - `RUN pip3 install flask`
  <!-- - `RUN pip3 install -r requirements.txt` -->
  - `ENV FLASK_APP microblog.py`

- Ensuite, copions le code de l’application à l’intérieur du conteneur. Pour cela ajoutez les lignes :

```Dockerfile
COPY ./microblog.py /microblog.py
COPY ./app /app
WORKDIR /
```

La première ligne copie le fichier Python pour lancer l'app.
Ces deux autres lignes indiquent de copier tout le contenu du dossier courant sur l'hôte dans un dossier /app à l’intérieur du conteneur. Puis le dossier courant dans le conteneur est déplacé à `/`.


- Enfin, ajoutons la section de démarrage à la fin du Dockerfile :

```Dockerfile
CMD flask run -h 0.0.0.0
```

- Reconstruisez le conteneur et lancez-le en ouvrant le port `5000` avec la commande : `docker run -d -p 5000:5000 microblog`

- Naviguez dans le navigateur à l’adresse `localhost:5000` pour admirer le prototype microblog.

- Lancez un deuxième container cette fois avec : `docker run -d -p 5001:5000 microblog`

- Une deuxième instance de l’app est maintenant en fonctionnement et accessible à l’adresse `localhost:5001`



## Faire varier la configuration en fonction de l'environnement

Le serveur de développement Flask est bien pratique pour debugger en situation de développement, mais n'est pas adapté à la production.
Nous pourrions créer deux images pour les deux situations mais ce serait aller contre l'impératif DevOps de rapprochement du dev et de la prod.

Pour démarrer l’application, nous aurons donc besoin d’un script de boot :

- créez un fichier `boot.sh` dans `app` avec à l’intérieur :

```bash
#!/bin/bash
set -e
if [ "$APP_ENVIRONMENT" = 'DEV' ]; then
    echo "Running Development Server"
    exec flask run -h 0.0.0.0
else
    echo "Running Production Server"
    exec gunicorn -b :5000 --access-logfile - --error-logfile - app_name:app
fi
```


- Enfin, modifions la section de démarrage en remplaçant la ligne qui commence par `CMD` à la fin du `Dockerfile` par :

```Dockerfile
RUN chmod +x boot.sh
ENTRYPOINT ["./boot.sh"]
```

- Déclarez maintenant dans le Dockerfile la variable d'environnement `APP_ENVIRONMENT` avec comme valeur par défaut `PROD`.

- Construisez l'image avec `build`, puis lancez une instance de l'app en configuration `PROD` et une une instance en environnement `DEV`.
  Avec `docker ps`, vérifiez qu'il existe bien une différence dans le programme lancé.

<!-- - Ensuite, pour démarrer l’application nous aurons besoin d’un script de boot. Créez un fichier `boot.sh` dans `app` avec à l’intérieur :
```bash
#!/bin/sh
flask run -h 0.0.0.0
``` -->


## Docker Hub

- Avec `docker login`, `docker tag` et `docker push`, poussez l'image `microblog` sur le Docker Hub. Créez un compte sur le Docker Hub le cas échéant.

<!-- ```bash
docker login
docker tag microblog:latest <your-docker-registry-account>/microblog:latest
docker push <your-docker-registry-account>/microblog:latest
``` -->

<!-- TODO: transition with TP3, package app to use VOLUME -->
<!-- TODO: transition with TP3, package app to add pip package mysql connector -->
<!-- ## La version de `microblog` avec base de données
- Revenez au dossier de `microblog` puis committez les modifications de votre dépôt.

```
git add Dockerfile boot.sh
git commit -m "Dockerfile simple"
git tag "tp2-dockerfile-simple"
```

- basculez grâce à Git au code de la version du code qui utilise une base de données : `git checkout v0.4`.

La version v0.4 de l'app peut fonctionner de deux façons :

- en stockant un fichier `sqlite` servant de base de données `SQL`.
- avec un autre conteneur servant de base de données `SQL` (par exemple `mysql`).

Nous verrons comment manipuler des volumes de bases de données et cet autre conteneur `mysql` dans le TP suivant. En attendant, nous allons packager l'app `microblog` pour lui permettre d'utiliser une base de données des deux façons (via un fichier dans un volume ou via la connexion à un conteneur `mysql`).

- récupérez vos fichiers `Dockerfile` et `boot.sh` créés précédemment depuis le tag Git créé à l'occasion (`tp2-dockerfile-simple`) grâce à la commande Git suivante :
  - `git checkout tp2-dockerfile-simple -- Dockerfile boot.sh`
  
  
  ---
  
   -->




<!-- TODO: add instruction and see how cache is busted -->

  <!-- - `RUN pip3 install flask` -->

<!-- - Ajoutez cette ligne après la première, ajoutez les ensuite sur une même ligne. Que remarque-t-on ?

La construction reprend depuis la dernière étape modifiée (l'ajout de requirements.txt). Sinon, la construction utilise le cache.

- Changez ensuite le contenu d'un des fichier python de l'application et relancez le build.

- Observez comme le build recommence à partir de l'instruction modifiée. Les layers précédents sont mis en cache par le docker engine -->
<!-- - Pour optimiser, rassemblez les commandes `RUN` liées à pip en une seule commande avec `&&` et sur plusieurs lignes avec `\`. 


---
-->

<!-- TODO: multi-stage build -->




<!-- - Voici à quoi doit ressembler ce `Dockerfile` plus complexe :

```Dockerfile
FROM python:3.7-buster

RUN useradd microblog

WORKDIR /home/microblog

COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt
RUN pip install gunicorn pymysql

COPY app app
COPY migrations migrations
COPY microblog.py config.py boot.sh ./
RUN chmod a+x boot.sh

ENV FLASK_APP microblog.py

RUN chown -R microblog:microblog ./
USER microblog

EXPOSE 5000
CMD ["/bin/bash", "./boot.sh"]
```

- `boot.sh`

```bash
#!/bin/sh
flask db upgrade
flask translate compile
exec gunicorn -b :5000 --access-logfile - --error-logfile - microblog:app
``` -->

- Poussez l'image microblog sur le Docker Hub comme indiqué dans le tutoriel.

```bash
docker login
docker tag microblog:latest <your-docker-registry-account>/microblog:latest
docker push <your-docker-registry-account>/microblog:latest
```



<!-- ## Décortiquer une image

- Affichez la liste des images présentes dans votre Docker Engine.


- Inspectez la dernière image que vous venez de créez (`docker image --help` pour trouver la commande)


- Observez l'historique de construction de l'image avec `docker image history <image>`


- Visitons **en root** (`sudo su`) le dossier `/var/lib/docker/` sur l'hôte. En particulier, `image/overlay2/layerdb/sha256/` :

  - On y trouve une sorte de base de données de tous les layers d'images avec leurs ancêtres.
  - Il s'agit d'une arborescence.

- Vous pouvez aussi utiliser la commande `docker save votre_image -o image.tar`, et utiliser `tar -C image_decompressee/ -xvf image.tar` pour décompresser une image Docker puis explorer les différents layers de l'image.

- Pour explorer la hiérarchie des images vous pouvez installer `https://github.com/wagoodman/dive` -->

## L'instruction HEALTHCHECK

`HEALTHCHECK` permet de vérifier si l'app contenue dans un conteneur est en bonne santé.

- Dans un nouveau dossier ou répertoire, créez un fichier `Dockerfile` dont le contenu est le suivant :

```Dockerfile
FROM python:alpine

RUN apk add curl
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

- _(Facultatif)_ Rajoutez une instruction `HEALTHCHECK` au `Dockerfile` de notre app microblog.

---

## _Facultatif:_ un Registry privé

- _(Facultatif)_ En suivant [les instructions du site officiel](https://docs.docker.com/registry/deploying/), créez votre propre registry.
- Puis trouvez les commandes pour le configurer et poussez-y une image dessus.

<!-- ## Faire parler la vache
- Changez de répertoire et créez un nouveau Dockerfile qui permet de faire dire des choses à une vache grâce à la commande `cowsay`. Indice : utilisez la commande `ENTRYPOINT`.
Le but est de la faire fonctionner dans un conteneur à partir de commandes de type :
- `docker run cowsay Coucou !`
- `docker run cowsay Salut !`
- `docker run cowsay Bonjour !` -->

<!-- Faites que l'image soit la plus légère possible en utilisant l'image de base `alpine`. Attention, alpine possède des commandes légèrement différentes (`apk add` pour installer) et la plupart des programmes nes ont pas installés par défaut. -->
