---
title: 'TP1 - Manipulation de conteneurs'
draft: false
---

## Installer Docker sur Ubuntu

- Suivez la [documentation docker pour installer docker sur ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)

- Vérifiez l'installation en lançant `docker info`.

- Il manque les droits à l'utilisateur pour exécuter docker.
  - Le daemon tourne toujours en `root`
  - Un utilisateur ne peut accéder au client que s'il est membre du groupe `docker`
  - Ajoutez le au groupe avec la commande `usermod -aG <groupe> <user>`

- Relancez la session


## Manipuler un conteneur

A l'aide du memento et de `--help`:

- Lancez simplement un conteneur debian en mode attaché. Que se passe-t-il ? 
```
docker run debian
=> Il ne se passe rien car comme debian ne contient pas d'application bloquante le conteneur s'arrête
```
- Lancez un conteneur debian en mode détaché avec la commande `echo "Debian container"`. Rien n'apparaît. En effet en mode détaché la sortie standard n'est pas connecté au terminal.

- Lancez `docker logs` avec le nom ou l'id du conteneur. Vous devriez voir le résultat de la commande `echo` précédente.
```
docker logs <5b91aa9952fa>
=> Debian container
```
- Affichez la liste des conteneur tournants
```bash
docker ps
```
- Lancez un conteneur debian en mode détaché avec la commande `sleep 200`
```bash
docker run -d debian sleep 200
```
- Affichez la liste des conteneur tournants
```
docker ps
```
- Stopper le conteneur.
```
docker stop <conteneur>
```
- Trouvez comment vous débarrasser d'un conteneur récalcitrant.
```
docker kill <conteneur>
```
- Affichez la liste des conteneurs tournants et arrêtés.
```
docker ps -a
```
- Tentez de lancer deux conteneurs avec le nom `debian_container`
```
docker run debian -d --name debian_container sleep 500
docker run debian -d --name debian_container sleep 500
=> name already used
```

- Créez un conteneur avec le nom `debian2`
```
docker run debian -d --name debian2 sleep 500
```

## Chercher sur Docker Hub

- Visitez [hub.docker.com](https://hub.docker.com)
- Cherchez l'image de wordpress
  - Lisons ensemble la documentation
  - téléchargez la dernière version (`pull`).
```
docker pull wordpress
```
- Lancez wordpress. Notez que lorsque l'image est déjà téléchargée le lancement d'un conteneur est quasi instantané.
```
docker run wordpress
```
- Ouvrez le port correctement (redirection 8080 de l'hôte vers 80 du conteneur par exemple ) pour pouvoir visiter votre site wordpress en local.
```
docker run -d --name wp --port 8080:80 wordpress
```
  - Pour ouvrir le port a postériori sur un conteneur existant utilisez `docker commit` comme indiqué [ici](https://stackoverflow.com/users/671479/fujimoto-youichi) (en fait c'est impossible il faut crez un snapshot de notre conteneur et en démarrer un nouveau).

### MYSQL et les variables d'environnement

Depuis Ubuntu:

- Chercher le conteneur MySQL version 5.7.
```
le nom du conteneur est mysql:5.7
```
- Lancez le.
```
docker run --name mysql -d mysql:5.7
```
- En lisant la documentation du conteneur mysql, trouvez comment utiliser une variable d'environnement pour préciser que le mot de passe peut être vide.
```
docker run --name mysql2 -e MYSQL_ALLOW_EMPTY_PASSWORD=yes -d mysql:5.7
```

- Mappez le port mysql (3306) sur le port 6666 de l'hôte (`-p`).
```bash
docker run --name mysql3 -e MYSQL_ALLOW_EMPTY_PASSWORD=yes -p 6666:3306 -d mysql:5.7
```

- Installez mariadb-client sur ubuntu pour vous connectez vous à votre conteneur en ligne de commande.

```bash
sudo apt install mariadb-client
mysql --user=root --host=127.0.0.1 --port=6666
``` 

- Lancez la commande `docker ps -aq -f status=exited`. Que fait elle ?
```
docker ps -aq -f status=exited
=> affiche seulement les ids des conteneurs arrêtés
```

- Combinez cette commande avec `docker rm` pour supprimer tous les conteneurs arrêtés (`$()` ?)
```
docker rm $(docker ps -aq -f status=exited)
```

- Listez les images
```
docker image ls
```

- supprimez une image
```
docker image rm <image_num_or_name>
```
