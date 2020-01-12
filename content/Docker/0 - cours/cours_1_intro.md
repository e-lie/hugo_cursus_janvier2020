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

# Installation et prise en main

---

# Installer Docker sous Windows ou Mac OS

Docker est basé sur le noyau linux :
- => En **production** il fonctionne nécessairement sur un **Linux** (Virtualisé ou Bare Metal)
- => Pour **développer et déployer**, il marche parfaitement sur **Mac OS** et **Windows** mais avec une méthode de **virtualisation**.
--



Deux possibilités:

- Solution historique : on utilise le **Docker toolbox** et **Docker machine** avec le **driver virtualbox**:
  - Permet de faire fonctionner `docker-machine` en local et pour déployer
  - Change légèrement le workflow par rapport à la version linux native.

---

# Installer Docker sous Windows ou Mac OS

- Solution récent : on utilise **Docker Desktop for windows/mac os**:
  - Sur windows : fonctionne avec Hyper-V (l'hyperviseur de Windows Server Virtualisation)
--

  - Sur Mac OS: fonctionne avec Hyperkit
--

  - => on ne peut pas lancer virtualbox **en même temps** !
--

  - => on peut seulement utiliser `docker-machine` avec hôtes distant
--

  - mais le workflow est plus classique (pas besoinde charger le contexte docker-machine)

---

# Installer Docker sur Linux

Pas de virtualisation nécessaire car Docker (le docker engine) utilise le noyau du système natif.

- On peut l'installer avec les paquet de l'OS mais cette version est souvent trop ancienne (à part sous Arch Linux)
--

- Sur **Ubuntu** ou **CentOS** la méthode conseillée est d'utiliser les paquets fournits dans le dépôt officiel docker
  - Il faut pour cela ajouter le dépôt et les signature développeur Docker.
  - Documentation ubuntu : https://docs.docker.com/install/linux/docker-ce/ubuntu/


---

# L'environnement de développement

## Docker Toolbox

- Docker Engine pour lancer des commandes docker

- Docker Machine pour utiliser les fonction de déploiement docker-machine

- Docker Compose pour lancer des application multiconteneurs

- Kitematic, un GUI Docker

- Un shell preconfiguré pour avoir un environment CLI Docker

- VirtualBox pour lancer des hôtes locaux (avec docker machine)


---

# Pour vérifier l'installation

- Les commandes de base pour connaître l'état de Docker sont:

```bash
docker info  # affiche plein d'information sur l'engine avec lequel vous êtes en contact
docker ps    # affiche les conteneurs en train de tourner
docker ps -a # affiche  également les conteneurs arrêtés
```

---

# Les images et conteneurs

**Docker** est à la fois une **runtime** pour les applications et un **outil de build** d'application.

- Une image est le **résultat** d'un build:
  - on peut les voir comme un modèle de boîte
  - simplement comme un artefact applicatif qu'on peut lancer plusieurs fois

Pour lister les images on utilise:

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

- Un conteneur est une instance en cours de fonctionnement("vivante") d'une image.
--


```bash
docker run [-d] [-p port_h:port_c] [-v dossier_h:dossier_c] <image> <commande>
```

- => créé et lance le conteneur.
--

- Un nom est automatiquement généré pour le conteneur à moins de fixer le nom avec `--name`
--

- On peut facilement lancer autant d'instances que nécessaire tant qu'il n'y a **pas de collision** de nom, de port ou de volumes.
--

- Justement lorsqu'il y a plusieurs instance on préfère laisser docker générer les noms (différents à chaque fois) pour éviter les collison

---

# Options docker run

- Les options facultatives indiquées ici sont très courantes.
  - `-d` permet de lancer le conteneur en mode **daemon** ou **détaché** et libérer le terminal
  - `-p` permet de mapper un port réseau entre l'intérieur et l'extérieur du conteneur, typiquement lorsqu'on veut accéder à l'application depuis l'hôte.
  - `-v` permet de monter un volume partagé entre l'hôte et le conteneur.

---

# Commande docker

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

- Les conteneurs sont plus que des processus ce sont des boîtes isolées grâce aux **namespaces** et **cgroups**
--

- Depuis l'intérieur d'un conteneur, on a l'impression d'être dans un linux autonome.
--

- Les utilisateurs unix à l'intérieur du conteneur ont des UID et GID normaux mais ces UID et GID ne sont
--

- Malgré l'isolation il est possible d'exploiter des failles pour s'échapper d'un conteneur
- => il faut faire attention à ne pas lancer les applications en `root` à l'intérieur des conteneurs docker (on y reviendra)

---

# Introspection de conteneur

- La commande `docker exec` permet d'exécuter une commande à l'intérieur du conteneur.

- Une utilisation typique est d'introspecter un conteneur en lançant `bash`

```
docker exec -it <conteneur> /bin/bash
```

---

# Le processus de build Docker


- Un image docker ressemble un peu à une appliance VM car il s'agit d'un linux "freezé"
--

- En réalité c'est assez différent : il s'agit uniquement d'un système de fichier par couches et d'un manifeste JSON
--

- Les images sont créés empilant de nouvelle couches sur une image existante
--

- Chaque nouveau build génère une nouvelle image dans le répertoire des images (/var/lib/docker/images) (attention ça peut vite prendre énormément de place)
--

- On construit les images à partir d'un fichier `Dockerfile` décrivant procéduralement la construction.

---

# Le processus de build Docker

Exemple de Dockerfile

```Dockerfile
FROM debian:latest

RUN apt update && apt install htop

CMD ['sleep 1000']
```

- La commande pour construire l'image est :
```
docker build [-t tag] [-f dockerfile] <build_context>
```

- exemple : `docker build -t mondebian .`

- généralement pour construire une image on se place directement dans le dossier avec le `Dockerfile` et les élements de contexte nécessaire (programme, config, etc)

---

# Docker Hub : télécharger des images

Une des forces de Docker est la distribution logicielle:

--

- pas besoin de dépendance, on récupère une boîte autonome
--

- pas besoin de multiples version en fonction des OS

--

Dans ce contexte un élément qui a fait le succès de Docker est le Docker Hub : [hub.docker.com](https://hub.docker.com)

Il s'agit d'un répertoire public et souvent gratuit d'images (officielles ou non) pour des milliers d'applications

---

# Docker Hub: 

- On peut y chercher et trouver presque n'importe quel logiciel au format d'image docker.
--

- Il suffit pour cela de chercher l'identifiant et la version de l'image désirée.
--


- Puis utiliser `docker run <id_image>:<version>`
--

- Ou télécharger l'image d'abord `docker pull <image>`

--

On peut également y créer un compte gratuit pour pousser et distribuer ses propres images (on parle de **docker registry**)

