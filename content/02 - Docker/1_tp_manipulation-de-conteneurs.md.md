---
title: "TP - Installer Docker et jouer avec"
weight: 15
---

⚠️ **Pour l'anglais, si un texte ne vous paraît pas clair, quelques liens :**

- Pour les textes : https://www.deepl.com/translator
- Pour les pages web : https://translate.google.com/
- Pour les mots : https://linguee.fr/

# Premier TD : on installe Docker et on joue avec

<!-- ## Solution 1 : importez une machine Linux

- Récupérez une machine virtualbox ubuntu (18.04)

- _(facultatif)_ Configurez-la avec 6 Go de RAM et 2 processeurs
- Démarrez la machine

- Faites les mises à jour via le Terminal (`apt update` et `apt upgrade`)

- Installez VSCode avec la commande suivante :

```bash
sudo snap install --classic code
```

-->

## Installer Docker sur la VM Ubuntu dans Scaleway

- Accédez à votre VM via l'interface Scaleway

- Vérifiez l'installation de Docker en lançant `sudo docker info`. Si Docker n'est pas installé, suivez la [documentation officielle pour installer Docker sur Ubuntu](https://docs.docker.com/engine/install/ubuntu/).

- Lancez `sudo docker run hello-world`. Bien lire le message renvoyé (le traduire sur [Deepl](https://www.deepl.com/translator) si nécessaire). Que s'est-il passé ?

- Il manque les droits à l'utilisateur pour exécuter docker sans passer par `sudo`.

  - Le daemon tourne toujours en `root`
  - Un utilisateur ne peut accéder au client que s'il est membre du groupe `docker`
  - Ajoutez-le au groupe avec la commande `usermod -aG <groupe> <user>` (en remplaçant `<groupe>` et `<user>` par ce qu'il faut)
  - Pour actualiser la liste de groupes auquel appartient l'utilisateur, déconnectez-vous de votre session puis reconnectez-vous pour que la modification sur les groupes prenne effet.

  <!-- **à l'aide du bouton en haut à droite de l'écran sur Ubuntu (pas simplement le terminal mais bien la session Ubuntu, redémarrer marche aussi)**  -->

<!-- - Relancez la session de terminal (en quittant le terminal puis en le relançant)

- Faites un snapshot de la VM Ubuntu avec VirtualBox -->

### Autocomplétion

- Pour vous faciliter la vie, ajoutez le plugin _autocomplete_ pour Docker et Docker Compose à `bash` en copiant les commandes suivantes :

```bash
sudo apt update
sudo apt install bash-completion curl
sudo mkdir /etc/bash_completion.d/
sudo curl -L https://raw.githubusercontent.com/docker/docker-ce/master/components/cli/contrib/completion/bash/docker -o /etc/bash_completion.d/docker.sh
sudo curl -L https://raw.githubusercontent.com/docker/compose/1.24.1/contrib/completion/bash/docker-compose -o /etc/bash_completion.d/docker-compose
```

**Important:** Vous pouvez désormais appuyer sur la touche <TAB> pour utiliser l'autocomplétion quand vous écrivez des commandes Docker

---

# Pour vérifier l'installation

- Les commandes de base pour connaître l'état de Docker sont :

```bash
docker info  # affiche plein d'information sur l'engine avec lequel vous êtes en contact
docker ps    # affiche les conteneurs en train de tourner
docker ps -a # affiche  également les conteneurs arrêtés
```

## Manipuler un conteneur

**Commandes utiles :** https://devhints.io/docker

Mentalité :
![](../../images/changingThings.jpg)
Il faut aussi prendre l'habitude de bien lire ce que la console indique après avoir passé vos commandes.

Avec l'aide du support et de `--help`, et en notant sur une feuille ou dans un fichier texte les commandes utilisées :

- Lancez simplement un conteneur Debian en mode _attached_. Que se passe-t-il ?

```
docker run debian
=> Il ne se passe rien car comme debian ne contient pas d'application bloquante le conteneur s'arrête
```

- Lancez un conteneur Debian (`docker run` puis les arguments nécessaires, cf. l'aide `--help`) en mode détaché avec la commande `echo "Debian container"`. Rien n'apparaît. En effet en mode détaché la sortie standard n'est pas connectée au terminal.

- Lancez `docker logs` avec le nom ou l'id du conteneur. Vous devriez voir le résultat de la commande `echo` précédente.

```
docker logs <5b91aa9952fa>
=> Debian container
```

<!-- - Réessayez en affichant le résultat cette fois-ci avec le mode *attached* -->

- Affichez la liste des conteneurs en cours d'exécution

```bash
docker ps
```

- Affichez la liste des conteneurs en cours d'exécution et arrêtés.

```bash
docker ps -a
```

- Lancez un conteneur debian **en mode détaché** avec la commande `sleep 3600`

- Réaffichez la liste des conteneurs qui tournent

- Tentez de stopper le conteneur, que se passe-t-il ?

```
docker stop <conteneur>
```

**NB:** On peut désigner un conteneur soit par le nom qu'on lui a donné, soit par le nom généré automatiquement, soit par son empreinte (toutes ces informations sont indiquées dans un `docker ps` ou `docker ps -a`)

- Trouvez comment vous débarrasser d'un conteneur récalcitrant (si nécessaire, relancez un conteneur avec la commande `sleep 3600` en mode détaché).

```
docker kill <conteneur>
```

- Tentez de lancer deux conteneurs avec le nom `debian_container`

```
docker run debian -d --name debian_container sleep 500
docker run debian -d --name debian_container sleep 500
```

Le nom d'un conteneur doit être unique (à ne pas confondre avec le nom de l'image qui est le modèle utilisé à partir duquel est créé le conteneur).

- Créez un conteneur avec le nom `debian2`

```
docker run debian -d --name debian2 sleep 500
```

- Lancez un conteneur debian en mode interactif (options `-i -t`) avec la commande `/bin/bash` et le nom `debian_interactif`.
<!-- - Lancez Kitematic pour observer son interface (facultatif) -->
- Dans un nouveau terminal lancez `docker inspect <conteneur_debian>` (en rempaçant par le nom de votre conteneur Debian). Cette commande fournit plein d'informations utiles mais difficiles à lire.

- Lancez-la à nouveau avec `| grep IPAddress` à la fin. Vous récupérez alors l'adresse du conteneur dans le réseau virtuel Docker.

---

## Chercher sur Docker Hub

- Visitez [hub.docker.com](https://hub.docker.com)
- Cherchez l'image de Wordpress et téléchargez la dernière version (`pull`).

```
docker pull wordpress
```

- Lancez un conteneur Wordpress. Notez que lorsque l'image est déjà téléchargée le lancement d'un conteneur est quasi instantané.

```
docker run wordpress
```

Ce conteneur n'est pas très utile, car on a oublié de configurer un port ouvert.

<!-- - Trouvez un moyen d'accéder quand même au Wordpress à partir de l'hôte Docker (indice : quelle adresse IP le conteneur possède-t-il ?). -->
  <!-- - *(facultatif)* Pour ouvrir le port a posteriori sur un conteneur existant, utilisez `docker commit` comme indiqué [sur ce post StackOverflow](https://stackoverflow.com/questions/19335444/how-do-i-assign-a-port-mapping-to-an-existing-docker-container/26622041#26622041). -->

- Arrêtez le(s) conteneur(s) `wordpress` créé(s). Relancez un nouveau conteneur avec cette fois-ci le port correctement configuré dès le début pour pouvoir visiter votre site Wordpress en local.

```
docker run -d --name wp --port 8080:80 wordpress
```

### MYSQL et les variables d'environnement

Depuis Ubuntu:

- Cherchez le conteneur `mysql` version 5.7.
- Lancez-le.

```
docker run --name mysql -d mysql:5.7
```

- Utilisez une variable d'environnement pour préciser que le mot de passe doit être vide (trouver la documentation sur le Docker Hub).

- Mappez mysql sur le port 6666 (`-p`).
- Installez `mariadb-client` (ou `mariadb`) sur Ubuntu et connectez-vous à votre conteneur en ligne de commande.

```bash
sudo apt install mariadb-client
mysql --user=root --host=127.0.0.1 --port=6666
```

- regardez les logs du conteneur avec `docker logs` ou inspectez le conteneur avec `docker inspect` (idéalement avec `grep`) pour trouver l'hôte à contacter
- utilisez `--help` sur la commande mysql pour choisir le port et l'hôte

### Portainer

- Lancer une instance de Portainer :

```bash
docker volume create portainer_data
docker run -d -p 8000:8000 -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer
```

- Naviguez sur l'adresse IP publique de votre serveur Docker sur le port 9000. Pour installer Portainer, il faut choisir l'option "local" lors de la configuration.

- Lancez la commande `docker ps -aq -f status=exited`. Que fait-elle ?

- Combinez cette commande avec `docker rm` pour supprimer tous les conteneurs arrêtés (indice : en Bash, une commande entre les parenthèses de "`$()`" est exécutée avant et utilisée comme chaîne de caractère dans la commande principale)

```
docker rm $(docker ps -aq -f status=exited)
```

- S'il y a encore des conteneurs qui tournent (`docker ps`), supprimez un des conteneurs restants en utilisant l'autocomplétion et l'option adéquate

- Listez les images
- Supprimez une image
- Que fait la commande `docker image prune -a` ?

- En utilisant la commande `docker save`, utilisez `tar` pour décompresser une image Docker puis explorez jusqu'à trouver l'exécutable principal contenu dans l'image.

<!-- - Facultatif : installez l'extension VSCode "Docker" par Microsoft pour vous faciliter la vie. Explorez l'interface. -->

## Utiliser Gitpod

- Se créer un compte sur [github.com](https://github.com)

- Créer un dépôt vide (vous pouvez l'appeler `tp_docker` par exemple) et l'ouvrir avec Gitpod

- Dans Gitpod, lancer la commande suivante pour installer Docker (`brew` est un gestionnaire de packet créé initialement pour MacOS et qui ne nécessite pas de droits `sudo`) :
  `brew install docker`

- Dans Gitpod, copiez-collez la clé privée fournie dans le dossier de partage dans un fichier appelé `ssh.private`, puis (après avoir retrouvé l'IP publique de votre VM Scaleway) lancez le tunnel SSH qui nous permettra d'accéder à notre dæmon Docker comme ceci :

```
export DOCKER_IP="INSEREZ_VOTRE_IP_ICI"
ssh -nNT -L localhost:23750:var/run/docker.sock $DOCKER_IP -N -l root -i ssh.private &
export DOCKER_HOST="localhost:23750"

```

- Vérifiez en faisant `docker info` et `docker ps` que vous avez bien accès à la même installation de Docker.
