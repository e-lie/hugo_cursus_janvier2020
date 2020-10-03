---
title: Conteneurs Docker
---

# Conteneurs Docker

## _Modularisez et maîtrisez vos applications_

---

# Installation et prise en main

---

# Terminologie et concepts fondamentaux

Deux concepts centraux :

- Une **image** : un modèle pour créer un conteneur.
- Un **conteneur** : l'instance qui tourne sur la machine.

![](images/docker-components.png)

Autres concepts primordiaux :

- Un **volume** : un espace virtuel pour gérer le stockage d'un conteneur et le partage entre conteneurs.
- un **registry** : un serveur ou stocker des artefacts docker c'est à dire des images versionnées.
- un **orchestrateur** : un outil qui gère automatiquement le cycle de vie des conteneurs (création/suppression).

---

# Visualiser l'architecture Docker

## Daemon - Client - images - registry

![](images/archi1.png)

---

# L'écosystème Docker

- **Docker Compose** : Un outil pour décrire des applications multiconteneurs.

- **Docker Machine** : Un outil pour gérer le déploiement Docker sur plusieurs machines depuis un hôte.

- **Docker Hub** : Le service d'hébergement d'images proposé par Docker Inc. (le registry officiel)

---

<!-- # Docker **Community** ou **Enterprise**

Choisir une édition :

- **Community Edition** (Docker CE)
  - Version de référence la plus répandue.
  - Solide et déployée en production.
  - Peu de limitations, suffit pour quelqu'usage que ce soit.
  - Open source => potentielle vérification et corrections de bugs par la communauté.

---

- **Enterprise Edition** (Docker EE)
  - Version basée sur la version community
  - Version entreprise du Docker Engine
    - Une stack complète de plugins avec des fonctionnalités spécifiques de : - sécurité : principal argument mis en avant (contrôle des images, communications chiffrées, réseau sécurisé Kubernetes) ; - un panel d'outils pour une intégration plus facile de la CI/CD ;
    - du support technique.
    - Intéressant pour avoir une pile bien intégrée, sécurisée pour des entreprises qui veulent leur plateforme de conteneurs "on premise".

---
 -->

<!--
# L'environnement de développement


- Docker Engine pour lancer des commandes docker

- Docker Compose pour lancer des application multiconteneurs

- Portainer, un GUI Docker

- VirtualBox pour lancer des VM

- Vagrant pour piloter VirtualBox simplement depuis un fichier et en ligne de commande

---

-->

# Installer Docker sur Windows ou MacOS

Docker est basé sur le noyau Linux :

- En **production** il fonctionne nécessairement sur un **Linux** (virtualisé ou _bare metal_)
- Pour **développer et déployer**, il marche parfaitement sur **MacOS** et **Windows** mais avec une méthode de **virtualisation** :
  - virtualisation optimisée via un hyperviseur
  - ou virtualisation avec logiciel de virtualisation "classique" comme VMWare ou VirtualBox.

---

# Installer Docker sur Windows

Trois possibilités :

- Solution _legacy_ : on utilise **Docker Toolbox** pour configurer Docker avec le **driver VirtualBox** :
  - Change légèrement le workflow par rapport à la version Linux native
  - Marche sur les "vieux" Windows (sans hyperviseur)
  - Utilise une VM Linux avec bash

---

# Installer Docker sur Windows

Trois possibilités:

- Solution Windows : on utilise **Docker Desktop for Windows**:
  - Fonctionne avec Hyper-V (l'hyperviseur optimisé de Windows)
  - Casse VirtualBox/VMWare (incompatible avec la virtualisation logicielle)
  - Proche du monde Windows et de PowerShell

---

# Installer Docker sur Windows

Trois possibilités:

- Solution Linux : on utilise **Docker Engine** dans une VM Linux
  - Utilise une VM Linux avec bash
  - Workflow identique à celui d'un serveur Linux
  - Proche de la réalité de l'administration système actuelle

On va utiliser ça :)

---

# Installer Docker sous MacOS

- Solution _legacy_ avec VM possible
- Solution standard : on utilise **Docker Desktop for MacOS** (fonctionne avec la bibliothèque HyperKit qui fait de l'hypervision)

---

# Installer Docker sur Linux

Pas de virtualisation nécessaire car Docker (le Docker Engine) utilise le noyau du système natif.

- ## On peut l'installer avec le gestionnaire de paquets de l'OS mais cette version est souvent trop ancienne (à part sous Arch Linux)

- Sur **Ubuntu** ou **CentOS** la méthode conseillée est d'utiliser les paquets fournis dans le dépôt officiel Docker (vous pouvez avoir des surprises avec la version _snap_ d'Ubuntu).
  - Il faut pour cela ajouter le dépôt et les signatures du répertoire de packages Docker.
  - Documentation Ubuntu : https://docs.docker.com/install/linux/docker-ce/ubuntu/

---

# Allons-y, installons Docker !

## Importez une machine Linux

- Récupérez une machine virtualbox ubuntu (18.04)

- _(facultatif)_ Configurez-la avec 6 Go de RAM et 2 processeurs
- Démarrez la machine

<!-- - Faites les mises à jour via le Terminal (`apt update` et `apt upgrade`) -->

- Installez VSCode avec la commande suivante :

```bash
sudo snap install --classic code
```

- En ligne de commande installez `htop`

## Installer Docker sur Ubuntu

- Suivez la [documentation Docker pour installer Docker sur Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)

- Vérifiez l'installation en lançant `sudo docker run hello-world`. Cela devrait afficher les étapes effectuées pour afficher ce message.

- Il manque les droits à l'utilisateur pour exécuter docker.

  - Le daemon tourne toujours en `root`
  - Un utilisateur ne peut accéder au client que s'il est membre du groupe `docker`
  - Ajoutez-le au groupe avec la commande `usermod -aG <groupe> <user>` (en remplaçant `<groupe>` et `<user>` par ce qu'il faut)
  - déconnectez-vous de votre session **à l'aide du bouton en haut à droite de l'écran sur Ubuntu (pas simplement le terminal mais bien la session Ubuntu, redémarrer marche aussi)** puis reconnectez-vous pour que la modification sur vos droits utilisateur prenne effet

- Pour vous faciliter la vie, ajoutez le plugin _autocomplete_ pour Docker et Docker Compose à `bash` en copiant les commandes suivantes :

```bash
sudo apt update
sudo apt install bash-completion curl
sudo mkdir /etc/bash_completion.d/
sudo curl -L https://raw.githubusercontent.com/docker/docker-ce/master/components/cli/contrib/completion/bash/docker -o /etc/bash_completion.d/docker.sh
sudo curl -L https://raw.githubusercontent.com/docker/compose/1.24.1/contrib/completion/bash/docker-compose -o /etc/bash_completion.d/docker-compose
```

Vous pouvez désormais appuyer sur la touche <TAB> pour utiliser l'autocomplétion quand vous écrivez des commandes Docker

- Relancez la session en quittant le terminal et en en relançant un

- Faites un snapshot de la VM Ubuntu avec VirtualBox

---

# Pour vérifier l'installation

- Les commandes de base pour connaître l'état de Docker sont :

```bash
docker info  # affiche plein d'information sur l'engine avec lequel vous êtes en contact
docker ps    # affiche les conteneurs en train de tourner
docker ps -a # affiche  également les conteneurs arrêtés
```

---

# Les images et conteneurs

![](images/docker-cycle.jpg)
**Docker** possède à la fois un module pour lancer les applications (runtime) et un **outil de build** d'application.

- Une image est le **résultat** d'un build :
  - on peut la voir un peu comme une boîte "modèle" : on peut l'utiliser plusieurs fois comme base de création de containers identiques, similaires ou différents.

Pour lister les images on utilise :

```bash
docker images
docker image ls
```

---

# Commandes Docker

Docker fonctionne avec des sous-commandes et propose de grandes quantités d'options pour chaque commande.

Utilisez `--help` au maximum après chaque commande, sous-commande ou sous-sous-commandes

```bash
docker image --help
```

---

# Créer et lancer un conteneur

- ## Un conteneur est une instance en cours de fonctionnement ("vivante") d'une image.

```bash
docker run [-d] [-p port_h:port_c] [-v dossier_h:dossier_c] <image> <commande>
```

> créé et lance le conteneur

## **L'ordre des arguments est important !**

- ## Un nom est automatiquement généré pour le conteneur à moins de fixer le nom avec `--name`
- On peut facilement lancer autant d'instances que nécessaire tant qu'il n'y a **pas de collision** de **nom**, de **port** ou de **volumes**.

---

# Options docker run

- Les options facultatives indiquées ici sont très courantes.
  - `-d` permet\* de lancer le conteneur en mode **daemon** ou **détaché** et libérer le terminal
  - `-p` permet de mapper un _port réseau_ entre l'intérieur et l'extérieur du conteneur, typiquement lorsqu'on veut accéder à l'application depuis l'hôte.
  - `-v` permet de monter un _volume_ partagé entre l'hôte et le conteneur.
  - `--rm` (comme _remove_) permet de supprimer le conteneur dès qu'il s'arrête.
  - `-it` permet de lancer une commande en mode _interactif_ (un terminal comme `bash`).
  - `-a` (ou `--attach`) permet de se connecter à l'entrée-sortie du processus dans le container.

---

# Commandes Docker

- Le démarrage d'un conteneur est lié à une **commande**.

- Si le conteneur n'a pas de commande, il s'arrête dès qu'il a fini de démarrer

```bash
docker run debian # s'arrête tout de suite
```

- Pour utiliser une commande on peut simplement l'ajouter à la fin de la commande run.

```bash
docker run debian echo 'attendre 10s' && sleep 10 # s'arrête après 10s
```

---

# Stopper et redémarrer un conteneur

`docker run` créé un nouveau conteneur à chaque fois.

```bash
docker stop <nom_ou_id_conteneur> # ne détruit pas le conteneur
docker start <nom_ou_id_conteneur> # le conteneur a déjà été créé
docker start --attach <nom_ou_id_conteneur> # lance le conteneur et s'attache à la sortie standard
```

---

# Isolation des conteneurs

- ## Les conteneurs sont plus que des processus, ce sont des boîtes isolées grâce aux **namespaces** et **cgroups**

- ## Depuis l'intérieur d'un conteneur, on a l'impression d'être dans un Linux autonome.

- ## Les utilisateurs Unix à l'intérieur du conteneur ont des UID et GID normaux mais ils peuvent correspondre à un utilisateur Unix sans droits sur l'hôte si on utilise les _user namespaces_.

<!-- - Malgré l'isolation il est possible d'exploiter des failles de configuration pour s'échapper d'un conteneur
- => il faut faire attention à ne pas lancer les applications en `root` à l'intérieur des conteneurs Docker et/ou à utiliser les *user namespaces* -->

---

# Introspection de conteneur

- La commande `docker exec` permet d'exécuter une commande à l'intérieur du conteneur **s'il est lancé**.

- Une utilisation typique est d'introspecter un conteneur en lançant `bash` (ou `sh`).

```
docker exec -it <conteneur> /bin/bash
```

---

# Premier TD : on installe Docker et on joue avec

Commandes utiles :

Mentalité :
![](images/changingThings.jpg)

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
