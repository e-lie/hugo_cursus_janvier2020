---
title: "Premier TD : on installe Docker et on joue avec"
weight: 15
---

# Premier TD : on installe Docker et on joue avec

Commandes utiles :

Mentalité :
![](../../images/changingThings.jpg)

## Manipuler un conteneur

A l'aide du cours et de `--help`, et en notant sur une feuille ou dans un fichier texte les commandes utilisées :

<!-- - Lancez simplement un conteneur Debian en mode *attached*. Que se passe-t-il ? -->

- Lancez un conteneur Debian (`docker run` puis les arguments nécessaires, cf. l'aide `--help`) en mode détaché avec la commande `echo "Debian container"`. Rien n'apparaît. En effet en mode détaché la sortie standard n'est pas connecté au terminal.
- Affichez la liste des conteneurs tournants et arrêtés.
- Lancez `docker logs` avec le nom ou l'id du conteneur. Vous devriez voir le résultat de la commande `echo` précédente.
<!-- - Réessayez en affichant le résultat cette fois-ci avec le mode *attached* -->
- Lancez un conteneur debian en mode détaché avec la commande `sleep 3600`
- Affichez la liste des conteneurs qui tournent
- Tentez de stopper le conteneur, que se passe-t-il ?
- Relancez un conteneur avec la commande `sleep 3600` en mode détaché et trouvez comment éteindre immédiatement un conteneur récalcitrant.
- Tentez de lancer deux conteneurs avec le nom `debian_container`
- Créez un conteneur avec le nom `debian2`
- Lancez un conteneur debian en mode interactif (options `-i -t`) avec la commande `/bin/bash` et le nom `debian_interactif`.
<!-- - Lancez Kitematic pour observer son interface (facultatif) -->
- Dans un nouveau terminal lancez `docker inspect <conteneur_debian>` (en rempaçant par le nom de votre conteneur Debian). Cette commande fournit plein d'informations utiles mais difficiles à lire.
- Lancez-la à nouveau avec `| grep IPAddress` à la fin. Vous récupérez alors l'adresse du conteneur dans le réseau virtuel Docker.

---

# Le processus de build Docker

- ## Un image Docker ressemble un peu à une appliance VM car il s'agit d'un linux "freezé" dans un état.

- ## En réalité c'est assez différent : il s'agit uniquement d'un système de fichier (par couches ou _layers_) et d'un manifeste JSON (des méta-données).

- Les images sont créés en empilant de nouvelles couches sur une image existante grâce à un système de fichiers qui fait du _union mount_.

--

- ## Chaque nouveau build génère une nouvelle image dans le répertoire des images (/var/lib/docker/images) (attention ça peut vite prendre énormément de place)

- On construit les images à partir d'un fichier `Dockerfile` en décrivant procéduralement (étape par étape) la construction.

---

# Le processus de build Docker

### Exemple de Dockerfile :

```Dockerfile
FROM debian:latest

RUN apt update && apt install htop

CMD ['sleep 1000']
```

- La commande pour construire l'image est :

```
docker build [-t tag] [-f dockerfile] <build_context>
```

- généralement pour construire une image on se place directement dans le dossier avec le `Dockerfile` et les élements de contexte nécessaire (programme, config, etc)

- exemple : `docker build -t mondebian .`

---

# Docker Hub : télécharger des images

Une des forces de Docker vient de la distribution d'images :

--

- ## pas besoin de dépendances, on récupère une boîte autonome

- pas besoin de multiples versions en fonction des OS

--

Dans ce contexte un élément qui a fait le succès de Docker est le Docker Hub : [hub.docker.com](https://hub.docker.com)

Il s'agit d'un répertoire public et souvent gratuit d'images (officielles ou non) pour des milliers d'applications pré-configurées.

---

# Docker Hub:

- ## On peut y chercher et trouver presque n'importe quel logiciel au format d'image Docker.

- ## Il suffit pour cela de chercher l'identifiant et la version de l'image désirée.

- ## Puis utiliser `docker run <id_image>:<version>`

- On peut aussi juste télécharger l'image : `docker pull <image>`

--

On peut également y créer un compte gratuit pour pousser et distribuer ses propres images, ou installer son propre serveur de distribution d'images privé ou public, appelé **registry**.

---

## Chercher sur Docker Hub

- Visitez [hub.docker.com](https://hub.docker.com)
- Cherchez l'image de Wordpress et téléchargez la dernière version (`pull`).
- Lancez Wordpress et tentez d'y accéder via `localhost:80`. Quel est le problème ? _Facultatif :_ Trouvez un moyen d'accéder quand même au Wordpress à partir de l'hôte Docker (indice : quelle adresse IP le conteneur possède-t-il ?).
  <!-- - *(facultatif)* Pour ouvrir le port a posteriori sur un conteneur existant, utilisez `docker commit` comme indiqué [sur ce post StackOverflow](https://stackoverflow.com/questions/19335444/how-do-i-assign-a-port-mapping-to-an-existing-docker-container/26622041#26622041). -->
- Arrêtez le(s) conteneur(s) `wordpress` créé(s). Relancez un nouveau conteneur avec cette fois-ci le port correctement configuré dès le début pour pouvoir visiter votre site Wordpress en local (regarder dans la doc officielle ou dans les cours la syntaxe de l'option qui permet de configurer les ports). Notez que lorsque l'image est déjà téléchargée le lancement d'un conteneur est quasi instantané.

<!-- ### MYSQL et les variables d'environnement

Depuis Ubuntu:

- Cherchez le conteneur `mysql` version 5.7.
- Lancez-le.
- Utilisez une variable d'environnement pour préciser que le mot de passe doit être vide (trouver la documentation).
- Mappez mysql sur le port 6666 (`-p`).
- Installez `mariadb` sur Ubuntu et connectez vous à votre conteneur en ligne de commande.
  - regardez les logs du conteneur avec `docker logs` ou inspectez le conteneur avec `docker inspect` (idéalement avec `grep`) pour trouver l'hôte à contacter
  - utilisez `--help` sur la commande mysql pour choisir le port et l'hôte -->

- Installer Portainer :

```bash
docker volume create portainer_data
docker run -d -p 8000:8000 -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer
```

- Naviguez sur `localhost:9000`. Pour installer Portainer, il faut choisir l'option "local" lors de la configuration.

- Lancez la commande `docker ps -aq -f status=exited`. Que fait-elle ?
- Combinez cette commande avec `docker rm` pour supprimer tous les conteneurs arrêtés (indice : `$()`)
- S'il y a encore des conteneurs qui tournent (`docker ps`), supprimez un des conteneurs restants en utilisant l'autocomplétion et l'option adéquate

- Listez les images
- Supprimez une image
- Que fait la commande `docker image prune -a` ?
