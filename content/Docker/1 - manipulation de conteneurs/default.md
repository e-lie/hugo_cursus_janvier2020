---
title: 'Docker 1 - Manipulation de conteneurs'
visible: 'yes'
---


## Importez une machine Linux

- Récupérez dans le partage une machine virtualbox ubuntu (18.04)
  
- Configurez la avec 6Go de RAM et 2 processeurs
  
- Démarrez la machine
  
- Faites le mises à jours (`update` et `upgrade`)

- Installez VSCode depuis la logitèque (ou votre éditeur favoris)
  
- En ligne de commande installez `htop`, `ncdu` et `ansible`

## Installer Docker sur Ubuntu

- Suivez la [documentation docker pour installer docker sur ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)

- Vérifiez l'installation en lançant `docker info`.

- Il manque les droits à l'utilisateur pour exécuter docker.
  - Le daemon tourne toujours en `root`
  - Un utilisateur ne peut accéder au client que s'il est membre du groupe `docker`
  - Ajoutez le au groupe avec la commande `usermod -aG <groupe> <user>`

- Relancez la session


## Manipuler un conteneur

A l'aide du cours et de `--help`:

- Lancez simplement un conteneur debian en mode attaché.
- Lancez un conteneur debian en mode détaché avec la commande `echo "Debian container"`. Rien n'apparaît. En effet en mode détaché la sortie standard n'est pas connecté au terminal.
- Lancez `docker logs` avec le nom ou l'id du conteneur. Vous devriez voir le résultat de la commande `echo` précédente.
- Affichez la liste des conteneur tournants
- Lancez un conteneur debian en mode détaché avec la commande `sleep 200`
- Affichez la liste des conteneur tournants
- Stopper le conteneur ?
- Trouvez comment vous débarrasser d'un conteneur récalcitrant.
- Affichez la liste des conteneurs tournants et arrêtés.
- Tentez de lancer deux conteneurs avec le nom `debian_container`
- Créez un conteneur avec le nom `debian2`

## Installez Docker desktop sur Windows

- Suivez la [documentation officielle](https://docs.docker.com/docker-for-windows/install/)

- Testez à nouveau les quelques commandes précédentes sous windows.
  
- Lancez un conteneur debian en mode interactif (options `-i -t`) avec la commande `/bin/bash` et le nom `debian_interactif`.
- Lancez kitematic pour observer son interface (facultatif)
- Dans un nouveau terminal lancez `docker inspect <conteneur_debian>`. Cette commande fournis plein d'informations utiles mais difficile à lire.
- Lancez la à nouveau avec `| grep IPAddress`. Vous récupérez alors l'adresse du conteneur dans le réseau virtuel Docker.

## Chercher sur Docker Hub

- Visitez [hub.docker.com](hub.docker.com)
- Cherchez l'image de wordpress et téléchargez la dernière version (`pull`).
- Lancez wordpress. Notez que lorsque l'image est déjà téléchargée le lancement d'un conteneur est quasi instantané.
- Ouvrez le port correctement pour pouvoir visiter votre site wordpress en local.
  - Pour ouvrir le port a postériori sur un conteneur existant utilisez `docker commit` commen indiqué [ici](https://stackoverflow.com/users/671479/fujimoto-youichi) (en fait c'est impossible il faut crez un snapshot de notre conteneur et en démarrer un nouveau).

### MYSQL et les variables d'environnement

Depuis Ubuntu:

- Chercher le conteneur `mysql` version 5.7.
- Lancez le.
- Utilisez une variable d'environnement pour préciser que le mot de passe doit être vide.
- Mappez le port mysql sur le port 6666 (`-p`).
- Installez mariadb sur ubuntu et connectez vous à votre conteneur en ligne de commande.
  - regardez les logs du conteneur avec `docker logs` pour trouver l'hôte à contacter
  - utiliser `--help` sur la commande mysql pour choisir le port et l'hôte

- Lancez la commande `docker ps -aq -f status=exited`. Que fait elle ?
- Combinez cette commande avec `docker rm` pour supprimer tous les conteneurs arrêtés (`$()` ?)

- Listez les images
- supprimez une image
