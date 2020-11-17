---
title: Cours 1 Docker
weight: 1
draft: false
---

## *Modularisez et maîtrisez vos applications*

## Introduction



### Des conteneurs


![](../../images/docker/docker.png?width=500)

- La métaphore docker : "box it, ship it"


- Une abstraction qui ouvre de nouvelles possibilités pour la manipulation logicielle.
- Permet de standardiser et de contrôler la livraison et le déploiement.





### Retour sur les technologies de virtualisation


On compare souvent les conteneur aux machines virtuelles

![](../../images/devops/windows-server-virtual-machines-vs-containers.png)


**VM** : une abstraction complète pour simuler des machines

- => un processeur, mémoire, appels systèmes, réseau, virtuels (etc.)

- **conteneur** : un découpage dans linux pour séparer des ressources.
]



### Origine : LXC (LinuX Containers)

- Originellement Docker était basé sur le projet **lxc**.


- Les conteneurs sont un vieux concept qui se rapproche de `chroot`, présent dans les systèmes unix depuis longtemps : "comme tout est fichier, changer la racine c'est comme changer de système".


- `jail` introduit par FreeBSD pour compléter chroot en isolant les processus (pour des raisons de sécurité)


- En 2005 Google commence le développement des **cgroups** : une façon de tagger les demandes de processeur et les appels systèmes pour pour les grouper et les isoler.

- En 2008 démarre le projet LXC qui chercher à rassembler, les **cgroups**, le **chroot** et les **namespaces**


- **namespaces**: une façon de compartimenter la mémoire et le systeme de fichier



### Origine : LXC (LinuX Containers)

- En 2013: docker Commence à proposer une meilleure finition et une interface simple qui facilite l'utilisation des conteneurs **lxc**. Puis propose aussi son cloud **Docker hub** pour faciliter la gestion e


- Au fur et à mesure Docker abandonne le code de **lxc** (mais continue d'utiliser les **cgroups** et **namespaces**)


- Le code de base par Docker (**runC**) est néanmoins open source : il s'agit autour de l'**Open Container Initiative** de mutualiser le travail spécifique pour qui soit solide et spécifique.



### Bénéfices par rapport aux machines virtuelles

Docker permet de faire des "quasi machines" avec des performances proches du natif. 

- Vitesse d'exécution.
- Flexibilité sur les ressources (mémoire partagée).
- Moins **complexe** que la virtualisation
- Plus **standard** que les multiples hyperviseurs
- notamment => Moins de bugs d'interaction entre l'hyperviseur et le noyau
- Pas besoin de spécialiste



### Bénéfices par rapport aux machines virtuelles
VM et conteneurs proposent une flexibilité de manipulation des ressources de calculs mais les machines virtuelles était trop lourdes pour être multipliées librement :
- elle n'ont pas pu être utilisées pour isoler **chaque application**
- elles ne permettent pas la transformation profonde que permettent les conteneurs :
  - le passage à une architecture **microservices**
  - et donc la **scalabilité horizontale** à l'heure du cloud



### Pourquoi utiliser Docker ?

Docker est pensé dès le départ pour faire des conteneurs applicatifs:

- **Isoler** les modules applicatifs.


- Gérer les **dépendances** en les embarquants dans le conteneur.


- **Immutabilité**.


- **Cycle de vie court** -> pas de persistance (DevOps).



### Pourquoi utiliser Docker ?

Docker transforme littéralement la **"logistique"** applicative.

- **Uniformisation** face aux divers langages de programmation


- **Installation sans accrocs** donc **automatisation** beaucoup plus facile.


- Permet de démocratiser l'**intégration continue** et la **livraison continue** voire le **déploiement continu**.


- **Rapprocher les développeurs** des **opérations** (tout le monde a la même technologie).
  
- Adoption plus large de la philosophie DevOps.



### Positionnement sur le marché

- Docker est la technologie ultra-dominante sur le marché de la conteneurisation
  - La simplicité d'usage et le travail de standardisation (OCI) lui on donné légitimité et fiabilité
  - La métaphore du conteneur de transport et la bonne documentation on 


- **LXC** existe toujours et avec **LXD** il est devenu très agréable à utiliser.
- Il a cependant un positionnement différent = faire des conteneurs pour faire tourner un OS linux complet. 


- **Apache Mesos**: Un logiciel de gestion de cluster qui permet de se passer de docker mais finalement propose un support pour docker depuis 2016.



### Terminologie et concepts fondamentaux

Ne pas confondre :

- Une **image** : un modèle pour créer un conteneur.
- Un **conteneur** : l'instance qui tourne sur la machine.
- Un **volume** : un espace virtuel pour gérer le stockage d'un conteneur et le partage entre conteneurs.

Autres concepts centraux:

- un **registry**: un serveur ou stocker des artefacts docker c'est à dire des images versionnées.
- un **orchestrateur**: un outil qui gère automatiquement le cycle de vie des conteneurs (création/suppression).



### Visualiser l'architecture Docker

#### Daemon - Client - images - registry

![](../../images/docker/archi1.png)




### L'écosystème Docker

- **Docker Compose** : Un outil pour décrire des applications multiconteneurs.


- **Docker Machine** : Un outil pour gérer des hôtes de déploiement docker


- **Docker Hub** : Le service d'hébergement universel d'images proposé par Docker Inc. (le registry officiel)


- (Docker **cloud** et Docker **store** ont fusionné avec **Docker Hub**)

## Installation et prise en main



### Installer Docker sous Windows ou Mac OS

Docker est basé sur le noyau linux :
- => En **production** il fonctionne nécessairement sur un **Linux** (Virtualisé ou Bare Metal)
- => Pour **développer et déployer**, il marche parfaitement sur **Mac OS** et **Windows** mais avec une méthode de **virtualisation**.


Deux possibilités:

- Solution historique : on utilise le **Docker toolbox** et **Docker machine** avec le **driver virtualbox**:
  - Permet de faire fonctionner `docker-machine` en local et pour déployer
  - Change légèrement le workflow par rapport à la version linux native.



### Installer Docker sous Windows ou Mac OS

- Solution récente : on utilise **Docker Desktop for windows/mac os**:
  - Sur windows : fonctionne avec Hyper-V (l'hyperviseur de Windows Server Virtualisation)


  - Sur Mac OS: fonctionne avec Hyperkit


  - => on ne peut pas lancer virtualbox **en même temps** !


  - => on peut seulement utiliser `docker-machine` avec hôtes distant


  - mais le workflow est plus classique (pas besoinde charger le contexte docker-machine)



### Installer Docker sur Linux

Pas de virtualisation nécessaire car Docker (le docker engine) utilise le noyau du système natif.

- On peut l'installer avec les paquet de l'OS mais cette version est souvent trop ancienne (à part sous Arch Linux)


- Sur **Ubuntu** ou **CentOS** la méthode conseillée est d'utiliser les paquets fournits dans le dépôt officiel docker
  - Il faut pour cela ajouter le dépôt et les signature développeur Docker.
  - Documentation ubuntu : https://docs.docker.com/install/linux/docker-ce/ubuntu/




### L'environnement de développement

#### Docker Toolbox

- Docker Engine pour lancer des commandes docker

- Docker Machine pour utiliser les fonction de déploiement docker-machine

- Docker Compose pour lancer des application multiconteneurs

- Kitematic, un GUI Docker

- Un shell preconfiguré pour avoir un environment CLI Docker

- VirtualBox pour lancer des hôtes locaux (avec docker machine)




### Pour vérifier l'installation

- Les commandes de base pour connaître l'état de Docker sont:

```bash
docker info  # affiche plein d'information sur l'engine avec lequel vous êtes en contact
docker ps    # affiche les conteneurs en train de tourner
docker ps -a # affiche  également les conteneurs arrêtés
```



### Les images et conteneurs

**Docker** est à la fois une **runtime** pour les applications et un **outil de build** d'application.

- Une image est le **résultat** d'un build:
  - on peut les voir comme un modèle de boîte
  - simplement comme un artefact applicatif qu'on peut lancer plusieurs fois

Pour lister les images on utilise:

```bash
docker images
docker image ls
```



## Commandes Docker 

Docker fonctionne avec des sous-commandes et propose de grandes quantités d'options pour chaque commande.

Utilisez `--help` au maximum après chaque commande, sous-commande ou sous-sous-commandes

```bash
docker image --help
```



### Créer et lancer un conteneur

- Un conteneur est une instance en cours de fonctionnement("vivante") d'une image.



```bash
docker run [-d] [-p port_h:port_c] [-v dossier_h:dossier_c] <image> <commande>
```

- => créé et lance le conteneur.


- Un nom est automatiquement généré pour le conteneur à moins de fixer le nom avec `--name`


- On peut facilement lancer autant d'instances que nécessaire tant qu'il n'y a **pas de collision** de nom, de port ou de volumes.


- Justement lorsqu'il y a plusieurs instance on préfère laisser docker générer les noms (différents à chaque fois) pour éviter les collison



### Options docker run

- Les options facultatives indiquées ici sont très courantes.
  - `-d` permet de lancer le conteneur en mode **daemon** ou **détaché** et libérer le terminal
  - `-p` permet de mapper un port réseau entre l'intérieur et l'extérieur du conteneur, typiquement lorsqu'on veut accéder à l'application depuis l'hôte.
  - `-v` permet de monter un volume partagé entre l'hôte et le conteneur.



### Commande docker

- Le démarrage d'un conteneur est lié à une **commande**.

- Si le conteneur n'a pas de commande, il s'arrête dès qu'il a fini de démarrer

```bash
docker run debian # s'arrête tout de suite
```

- Pour utiliser une commande on peut simplement l'ajouter à la fin de la commande run.

```bash
docker run debian echo 'attendre 10s' && sleep 10 # s'arrête après 10s
```



### Stopper et redémarrer un conteneur

`docker run` créé un nouveau conteneur à chaque fois.


```bash
docker stop <nom_ou_id_conteneur> # ne détruit pas le conteneur
docker start <nom_ou_id_conteneur> # le conteneur a déjà été créé
docker start --attach <nom_ou_id_conteneur> # lance le conteneur et s'attache à la sortie standard
```



### Isolation des conteneurs

- Les conteneurs sont plus que des processus ce sont des boîtes isolées grâce aux **namespaces** et **cgroups**
- Depuis l'intérieur d'un conteneur, on a l'impression d'être dans un linux autonome.
- Malgré l'isolation il est possible d'exploiter des failles pour s'échapper d'un conteneur
- => il faut faire attention à ne pas lancer les applications en `root` à l'intérieur des conteneurs docker (on y reviendra)

### Introspection de conteneur

- La commande `docker exec` permet d'exécuter une commande à l'intérieur du conteneur.
- Une utilisation typique est d'introspecter un conteneur en executant `bash` à l'intérieur.

```
docker exec -it <conteneur> /bin/bash
```

### Le processus de build Docker


- Un image docker ressemble un peu à une appliance VM car il s'agit d'un linux "freezé"
- En réalité c'est assez différent : il s'agit uniquement d'un système de fichier par couches et d'un manifeste JSON
- Les images sont créés empilant de nouvelle couches sur une image existante
- Chaque nouveau build génère une nouvelle image dans le répertoire des images (/var/lib/docker/images) (attention ça peut vite prendre énormément de place)
- On construit les images à partir d'un fichier `Dockerfile` décrivant procéduralement la construction.

### Le processus de build Docker

- Exemple de Dockerfile:

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


### Docker Hub : télécharger des images

Une des forces de Docker est la distribution logicielle:

- pas besoin de dépendance, on récupère une boîte autonome
- pas besoin de multiples version en fonction des OS il suffit d'avoir la runtime docker sur son système

Dans ce contexte un élément qui a fait le succès de Docker est le Docker Hub : [hub.docker.com](https://hub.docker.com)
Il s'agit d'un répertoire public et souvent gratuit d'images (officielles ou non) pour des milliers d'applications

#### Conlusion : les développeurs en raffolent

### Docker Hub: 

- On peut y chercher et trouver presque n'importe quel logiciel au format d'image docker.
- Il suffit pour cela de chercher l'identifiant et la version de l'image désirée.
- Puis utiliser `docker run <id_image>:<version>`
- Ou télécharger l'image d'abord `docker pull <image>`

On peut également y créer un compte gratuit pour pousser et distribuer ses propres images (on parle de **docker registry**)

## Images et conteneurs

### Créer une image en utilisant un Dockerfile

- Jusqu'ici nous avons utilisé des images toutes prêtes principalement.
- Une des fonctionnalité principale de docker est de pouvoir facilement construire ses images à partir d'un simple fichier texte: **le Dockerfile**
- Le **Dockerfile** est un fichier procédural de construction qui permet décrire l'installation d'un logiciel en enchaînant des instructions Dockerfile (en MAJUSCULE)

- Exemple:
```Dockerfile
FROM debian:stretch
RUN apt-get update && apt-get install -y cowsay fortune
ENTRYPOINT["/usr/games/cowsay"]
```

#### Instruction `FROM`

- L'image de base à partir de laquelle est construite l'image.

#### Instruction `RUN`

- Permet de lancer une commande shell (installation, configuration).

#### Instruction `ADD`

- Permet d'ajouter des fichier depuis le contexte de build à l'intérieur du conteneur.
- Généralement utilisé pour ajouter le code du logiciel en cours de développement et sa configuration au conteneur.

#### Instruction CMD

- Généralement à la fin du `Dockerfile` : elle permet de préciser la commande par défaut lancée à la création d'une instance du conteneur avec `docker run`. on l'utilise avec une liste de paramètres
```Dockerfile
CMD ["echo", "Conteneur démarré"]
```

#### Instruction ENTRYPOINT

- Précise le programme de base avec lequel sera lancé la commande
  
```Dockerfile
ENTRYPOINT ["/usr/bin/python3"]
```

#### Instruction ENV

- Une façon recommandée de configurer vos applications Docker est d'utiliser les variables d'environnement UNIX ce qui permet une configuration "à runtime". 

#### Documentation

- Il existe de nombreuses autres instructions possible très clairement décrites dans la documentation officielle : [https://docs.docker.com/engine/reference/builder/](https://docs.docker.com/engine/reference/builder/)



### Lancer la construction

- La commande pour lancer la construction d'une image est:

```bash
docker build -t <tag:version> -f <chemin_du_dockerfile> <contexte_de_construction>
```

- Lors de la construction Docker télécharge l'image de base. On constate plusieurs téléchargement en parallèle. (démo)
- Il lance ensuite la séquence des instruction du Dockerfile.
- Observez l'historique de construction de l'image avec `docker image history <image>`
- Il lance ensuite la série d'instructions du Dockerfile et indique un hash pour chaque étape. Pourquoi ?

### Les layers et la mise en cache

- Docker construit les images comme une série de couches compactées.

- On parle d'**Union Filesystem** car chaque couche (les fichiers) écrase la précédente

- Chaque couche correspond à une instruction du Dockerfile.

- `docker image history <conteneur>` permet d'afficher les layers, leur date de construction et taille respectives.

- Ce principe est au coeur de l'**immutabilité** des images docker.

- Au lancement d'un container, le docker Engine rajoute une nouvelle couche de filesystem "normal" read/write par dessus la pile des couches de l'image.

- `docker diff <container>` permet d'observer les changements apportés au conteneur depuis le lancement.


### Optimiser la création d'images

- Les images Docker ont souvent une taille de plusieurs centaines de **megaoctets** voir parfois **gigaoctets**. `docker image ls` pour voir la taille des images.
- Or on construit souvent plusieurs dizaines de versions d'une application par jours. (souvent automatiquement sur les serveurs d'intégration continue).
- => L'espace disque devient alors un sérieux problème.

- Le principe de Docker est justement d'avoir des images légères car on va créer beaucoup de conteneurs (un par instance d'application/service)

- De plus on télécharge souvent les images depuis un registry ce qui consomme de la bande passante

- => La principale **bonne pratique** dans la construction d'images est de **limiter leur taille au maximum**.


### Limiter la taille d'une image

- Choisir une image linux de base **minimale**:
  - Une image `ubuntu` complète pèse déjà presque une centaine de megaoctets.
  - mais une image trop rudimentaire (`busybox`) est difficile à debugger et peu bloquer pour certaines tache (compilation par exemple)
  - Souvent on utilise des images de base construite à partir de alpine Linux qui est un bon compromis.
  - Ainsi par exemple `python` est fournit en une version `python3.7-alpine`

- Limiter le nombre commandes de modification du conteneur:
  - `RUN`, `ADD` et toute commande impliquant une modification du système de fichier du conteneur vas créer un nouveau layer dans l'image.
  - => Souvent on enchaine les commandes en une seule pour économiser des couches. 


### Publier des images vers un registry privé

- Généralement les images spécifiques produites par une entreprise n'ont pas vocation à finir dans un dépot public.

- On peut installer des **registry privés** grâce à de nombreuses solutions.

- On utilise alors `docker login <adresse_repo>` pour se logger au répository.

- Plusieurs options:
  - **Gitlab** fournit un registry intégré très intéressant car intégré dans leur workflow DevOps.
  - **Docker Trusted Registry (DTR)** fait partie de **Docker Enterprise** pratique des tests de sécurité sur les images.
  - Plein d'autre solution plus ou moins générique pour stocker des artefacts logiciel.


### Créer des conteneurs personnalisés

- Il n'est pas nécessaire de partir d'une image linux vierge pour construire un conteneur.

- On peut utiliser la directive `FROM` avec n'importe quelle image.

- De nombreuses application peuvent être configurées en étendant une image officielle
  - Exemple: Jenkins dans le TP du 3e jour, pour configurer l'utilisateur par défaut.

- L'intérêt ensuite est que l'image est disponible préconfigurée pour construire ou mettre à jour une infrastructure

- C'est grâce à cette fonctionnalité que Docker peu être considéré comme un outil d'Infrastructure As Code.

- On peut également prendre une sorte snapshot de conteneur en train de tourner sous forme d'image avec `docker commit <image>`.
