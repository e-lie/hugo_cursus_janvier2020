---
title: Manipulation des conteneurs
weight: 10
---

# Terminologie et concepts fondamentaux

Deux concepts centraux :

- Une **image** : un modèle pour créer un conteneur.
- Un **conteneur** : l'instance qui tourne sur la machine.

![](../../images/docker-components.png)

Autres concepts primordiaux :

- Un **volume** : un espace virtuel pour gérer le stockage d'un conteneur et le partage entre conteneurs.
- un **registry** : un serveur ou stocker des artefacts docker c'est à dire des images versionnées.
- un **orchestrateur** : un outil qui gère automatiquement le cycle de vie des conteneurs (création/suppression).

---

# Visualiser l'architecture Docker

## Daemon - Client - images - registry

![](../../images/archi1.png)

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

- On peut l'installer avec le gestionnaire de paquets de l'OS mais cette version est souvent trop ancienne (à part sous Arch Linux)

- Sur **Ubuntu** ou **CentOS** la méthode conseillée est d'utiliser les paquets fournis dans le dépôt officiel Docker (vous pouvez avoir des surprises avec la version _snap_ d'Ubuntu).
  - Il faut pour cela ajouter le dépôt et les signatures du répertoire de packages Docker.
  - Documentation Ubuntu : https://docs.docker.com/install/linux/docker-ce/ubuntu/

---

# Les images et conteneurs

![](../../images/docker-cycle.jpg)
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

- Un conteneur est une instance en cours de fonctionnement ("vivante") d'une image.

```bash
docker run [-d] [-p port_h:port_c] [-v dossier_h:dossier_c] <image> <commande>
```

> créé et lance le conteneur

## **L'ordre des arguments est important !**

- **Un nom est automatiquement généré pour le conteneur à moins de fixer le nom avec `--name`**
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

- Les conteneurs sont plus que des processus, ce sont des boîtes isolées grâce aux **namespaces** et **cgroups**

- Depuis l'intérieur d'un conteneur, on a l'impression d'être dans un Linux autonome.

- Les utilisateurs Unix à l'intérieur du conteneur ont des UID et GID normaux mais ils peuvent correspondre à un utilisateur Unix sans droits sur l'hôte si on utilise les _user namespaces_.

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

# Docker Hub : télécharger des images

Une des forces de Docker vient de la distribution d'images :

- pas besoin de dépendances, on récupère une boîte autonome

- pas besoin de multiples versions en fonction des OS

Dans ce contexte un élément qui a fait le succès de Docker est le Docker Hub : [hub.docker.com](https://hub.docker.com)

Il s'agit d'un répertoire public et souvent gratuit d'images (officielles ou non) pour des milliers d'applications pré-configurées.

---

# Docker Hub:

- On peut y chercher et trouver presque n'importe quel logiciel au format d'image Docker.

- Il suffit pour cela de chercher l'identifiant et la version de l'image désirée.

- Puis utiliser `docker run <id_image>:<version>`

- On peut aussi juste télécharger l'image : `docker pull <image>`

On peut également y créer un compte gratuit pour pousser et distribuer ses propres images, ou installer son propre serveur de distribution d'images privé ou public, appelé **registry**.

---
