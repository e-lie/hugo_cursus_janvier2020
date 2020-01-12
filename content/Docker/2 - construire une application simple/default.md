---
title: 'Docker 2 - Construire une application simple'
visible: 'yes'
---


## I – Packager une application web flask avec Docker

- Récupérez d’abord une application Flask exemple en la clonant sur le Bureau :
```
git clone https://github.com/miguelgrinberg/microblog/
```

- Nous allons activer une version simple de l’application grace à git : `git checkout v0.2`
   
- Ouvrez le dossier microblog cloné avec VSCode (Open Folder)
   
- Pour la tester d’abord en local (sans conteneur) nous avons besoin des outils python. Vérifions si ils sont installées :
    `sudo apt install python3-virtualenv virtualenv`

- Créons l’environnement virtuel : `virtualenv -p python3 venv`

- Activons l’environnement : `source venv/bin/activate`

- Installons la librairie flask et exportons une variable d’environnement pour déclarer l’application.
    a) `pip install flask`
    b) `export FLASK_APP=microblog.py`

- Maintenant nous pouvons tester l’application en local avec la commande : `flask run`

- Visitez l’application dans le navigateur à l’adresse indiquée.
    
- Observons le code dans VSCode. Qu’est ce qu’un fichier de template  ? Où se trouve les fichiers de templates dans ce projet.
 
- Changez le prénom Miguel par le votre dans l’application. Testez à chaque modification en rechargeant la page.

## II – Passons à Docker

Déployer une application flask manuellement est relativement pénible.

Nous allons donc construire une image de conteneur pour empaqueter l’application et la manipuler plus facilement. Assurez vous que docker est installé.

- Dans le dossier du projet ajoutez un fichier nommé `Dockerfile`

-  Ajoutez en haut du fichier : `FROM ubuntu:latest` Cette commande indique que notre conteneur de base est le conteneur officiel ubuntu.
   
- Nous pouvons déjà contruire un conteneur à partir de ce modèle ubuntu vide :
   `docker build -t microblog .`

- Une fois la construction terminée lancez le conteneur.
   
-  Le conteneur s’arrête immédiatement. En effet il ne contient aucune commande bloquante et nous n'avons préciser aucune commande au lancement. Pour pouvoir observer le conteneur convenablement il fautdrait faire tourner quelque chose à l’intérieur. Ajoutez à la  fin du fichier la ligne :
   `CMD ["/bin/sleep", "1000"]`
   Cette ligne indique au conteneur d’attendre pendant 1000 secondes comme au TP précédent.

- Reconstruisez le conteneur et relancez le

- Affichez la liste des conteneurs en train de fonctionner

- Nous allons maintenant rentrer dans le conteneur en ligne de commande pour observer. Utilisez la commande : `docker exec -it <id_du_conteneur> /bin/bash`

- Vous etes maintenant dans le conteneur avec une invite de commande. Utilisez quelques commandes linux pour le visiter rapidement.

- Il s’agit d’un linux standard, mais il n’est pas conçu pour être utilisé comme un système complet mais pour isoler une application. Il faut maintenant ajouter notre application flask à l’intérieur. Dans le Dockerfile supprimez la ligne CMD, puis ajoutez :
    - `RUN apt-get update -y`
    - `RUN apt-get install -y python-pip python-dev build-essential`

- Reconstruisez votre conteneur. Si tout se passe bien poursuivez.

- Il s’agit maintenant de copier le code de l’application à l’intérieur du conteneur. Pour cela ajoutez les lignes :
`COPY . /app`
`WORKDIR /app`
Ces deux lignes indiquent de copier tout le contenu du dossier courant dans un dossier /app à l’intérieur du conteneur. Puis le dossier courant est déplacé à `/app`.

- Pour installer les dépendances python et configurer la variable d'environnement Flask ajoutez:
    - `RUN pip install flask`
    - `ENV FLASK_APP microblog.py`

- Enfin pour démarrer l’application nous auront besoin d’un script de boot. Créez un fichier `boot.sh` avec à l’intérieur :

```
#!/bin/sh
flask run -h 0.0.0.0
```

-  Ensuite ajoutons la section de démarrage à la fin du Dockerfile :
  
```
RUN chmod +x boot.sh
ENTRYPOINT ["./boot.sh"]
```

- Reconstruisez le conteneur et lancez le en ouvrant le port `5000` avec la commande : `docker run -d -p 5000:5000 microblog`

- Naviguez dans le navigateur à l’adresse `localhost:5000` pour admirer le prototype microblog.

- Lancez un deuxième container cette fois avec : `docker run -d -p 5001:5000 microblog`

- Une deuxième instance de l’app est maintenant en fonctionnement et accessible à l’adresse localhost:5001

## La version plus complexe

- Committez les modifications de votre dépot.
- basculez au niveau de la version plus complexe : `git checkout v0.18`
- Suivez le [tutoriel de michael Grindberg](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-xix-deployment-on-docker-containers) pour packager cette version plus complexe avec un mysql.
- La partie finale sur elasticsearch est facultative pour ce TP
- Poussez bien l'image microblog sur le Docker Hub comme indiqué dans le tutoriel. Créez un compte le cas échéant.

- Affichez la liste des images présentes dans votre Docker Engine.

- Inspectez la dernière image que vous venez de créez.

- Observez l'historique de construction de l'image avec `docker image history <image>`


## Observons et optimisons l'image

- Changez le contenu du fichier `requirements.txt` puis relancez le build.

- Changez ensuite le contenu d'un des fichier python de l'application et relancez le build.

- Observez comme le build recommence à partir de l'instruction modifiée. Les layers précédents sont mis en cache par le docker engine

- Pour optimiser, rassemblez les commandes `RUN` liées à pip en une seule  commande avec `&&` et sur plusieurs lignes avec `\`.
- Faites de même pour les `RUN` avec `chown/chmod`

- Restestez votre image.

- Finalement, avons nous besoin d'un **virtualenv** à l'intérieur d'un **conteneur docker** ? La boîté isole déjà les dépendances d'une seule application.
- => modifier le Dockerfile pour installer les dépendances directement dans l'OS du conteneur (sur une seule ligne).

- Ajoutez au début du `Dockerfile` une commande `LABEL maintainer=<votre_nom>`

- Ajoutez une commande `LABEL version=<votre_version>`

- Le shell par défaut de Docker est `SHELL ["/bin/sh", "-c"]`. Cependant ce shell a certains comportement inhabituels et la commande de construction **n'échoue pas forcément** si une commande s'est mal passée. Dans une optique d'intégration continue, pour rendre la modification ultérieure de l'image plus sure ajoutez au début (en dessous des `LABEL`) `SHELL ["/bin/bash", "-eux", "-o", "pipefail", "-c"]`.

- Cependant le shell bash est non standard sur docker. Pour ne pas perturber les utilisateurs de l'image qui voudrait lancer des commande il peut être intéressant de rebasculer sur `sh` à la fin de la construction. Ajoutez à l'avant dernière ligne: `SHELL ["/bin/sh", "-c"]`
  
- Visitons **en root** le dossier `/var/lib/docker/` en particulier `image/overlay2/layerdb/sha256/`:
  - On y trouve une sorte base de données de tous les layers d'images avec leurs ancêtres.
  - Il s'agit une arborescence.

- Pour explorer la hiérarchie des images vous pouvez installer `https://github.com/wagoodman/dive`
