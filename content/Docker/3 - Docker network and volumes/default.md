---
title: 'Docker 3 - réseaux et volumes'
visible: true
---

# Docker networking

Pour expérimenter avec le réseau, nous allons lancer une petite application nodejs d'exemple (moby-counter) qui fonctionne avec une queue redis (comme une base de données mais pour stocker des paires clé/valeur simple).

Récupérons les images depuis Docker Hub:

- `docker image pull redis:alpine`
- `docker image pull russmckendrick/moby-counter`

Pour connecter les deux applications créons un réseau manuellement:

- `docker network create moby-counter`

Puis lancons les deux applications en utilisant notre réseau
- `docker container run -d --name redis --network <réseau> redis:alpine`
- `docker container run -d --name moby-counter --network <réseau> -p <mapping_web> russmckendrick/moby-counter`

- Visitez la page de notre application. Qu'en pensez vous ? Moby est le nom de la mascotte Docker :). Faites un motif reconnaissable en cliquant.

Comment notre application se connecte-t-elle au conteneur redis ? Elle utilise ces instructions JS dans son fichier `server.js`:
```javascript
var port = opts.redis_port || process.env.USE_REDIS_PORT || 6379
var host = opts.redis_host || process.env.USE_REDIS_HOST || 'redis'
```
En résumé par défaut, notre application se connecte sur l'hôte `redis` avec le port `6379`

Explorons un peu notre réseau Docker.

- Executez (`docker exec`) la commande `ping -c 3 redis` sur notre conteneur applicatif. Quelle est l'adresse ip affichée ?

- De même, affichez le contenu des fichiers `/etc/hosts` et `/etc/resolv.conf`. Nous constatons que docker a automatiquement configuré le nom d'hôte de la machine avec l'identifiant du conteneur.

- Interrogeons le serveur DNS de notre réseau `moby-counter` en lançant la commande `nslookup redis 127.0.0.11` toujours grâce à `docker exec`.

- Créez un deuxième réseau `moby-counter2` et une deuxième instance de l'application dans ce réseau : `docker run -itd --name moby-counter2 --network moby-counter2 -p 9090:80 russmckendrick/moby-counter`

- Lors que vous pingez `redis` depuis cette nouvelle instance de l'application, quelle ip obtenez vous ?
  
- Récupérez comme auparavant l'adresse ip du nameserver local pour `moby-counter2`.
- Puis lancez `nslookup redis <nameserver_ip>` pour tester la résolution de DNS. Comparer l'adresse ip avec les adresses habituelles Docker.

Bien que vous ne puissiez pas avoir deux conteneurs avec les mêmes noms, comme nous l'avons déjà découvert, notre deuxième réseau fonctionne complètement isolé de notre premier réseau, ce qui signifie que nous pouvons toujours utiliser le domaine `redis` ; pour ce faire, nous devons ajouter le drapeau `--network-alias` :

- Créons un deuxième redis avec le même domaine: `docker container run -d --name redis2 --network moby-counter2 --network-alias redis redis:alpine`

- Relancez la résolution précédente avec `nslookup`.

- Vous pouvez retrouver la configuration du réseau et les conteneurs qui lui sont relié avec `docker network inspect moby-counter`.
  Notez la section IPAM.

- Arrêtons nos conteneurs `docker container stop moby-counter2 redis2`.

- Pour faire rapidement le ménage des conteneurs arrêtés lancez `docker container prune`.

- De même `docker network prune` permet de faire le ménage des réseaux qui ne sont plus utilisés par aucun conteneur.

## Docker Volumes

- Lancez `docker volume prune` pour faire le ménage de volume éventuellement créé dans les TPs précédent (pour ne pas interférer avec ce exercice).
- Arrêtez et supprimez le conteneur redis (n°1).
- Visitez votre application dans le navigateur. Elle est maintenant déconnectée de son backend.
- Recréez le conteneur `redis` dans le réseau `moby-counter`
- Rechargez la page, plusieurs fois. Que s'est-il passé ?
- Supprimez à nouveau le conteneur redis.

Avons nous vraiment perdu les données de notre conteneur ? le Dockerfile pour l'image officielle Redis ressemble à ça:
```Dockerfile
FROM alpine:3.5

RUN addgroup -S redis && adduser -S -G redis redis
RUN apk add --no-cache 'su-exec>=0.2'`docker container prune`
`docker network prune`
`docker volume prune`
ENV REDIS_VERSION 3.0.7
ENV REDIS_DOWNLOAD_URL http://download.redis.io/releases/redis-3.0.7.tar.gz
ENV REDIS_DOWNLOAD_SHA e56b4b7e033ae8dbf311f9191cf6fdf3ae974d1c
RUN set -x \
    && apk add --no-cache --virtual .build-deps \
        gcc \
        linux-headers \
        make \
        musl-dev \
        tar \
    && wget -O redis.tar.gz "$REDIS_DOWNLOAD_URL" \
    && echo "$REDIS_DOWNLOAD_SHA *redis.tar.gz" | sha1sum -c - \
    && mkdir -p /usr/src/redis \
    && tar -xzf redis.tar.gz -C /usr/src/redis --strip-components=1 \
    && rm redis.tar.gz \
    && make -C /usr/src/redis \
    && make -C /usr/src/redis install \
    && rm -r /usr/src/redis \
    && apk del .build-deps

RUN mkdir /data && chown redis:redis /data
VOLUME /data
WORKDIR /data
COPY docker-entrypoint.sh /usr/local/bin/
RUN ln -s usr/local/bin/docker-entrypoint.sh /entrypoint.sh # backwards compat
ENTRYPOINT ["docker-entrypoint.sh"]
EXPOSE 6379
CMD [ "redis-server" ]
```

Notez, vers la fin du fichier, il y a deux instructions VOLUME et WORKDIR; Cela signifie que lorque notre conteneur a été lancé, un volume "caché" a effectivement été créé par Docker.

- Pour le confimer listez les volumes et inspectez les pour trouver l'identifiant d'un précédent volume.

- Créez un nouveau conteneur redis en rattachant l'un des précédents volumes redi.
`docker container run -d --name redis -v <volume_id>:/data --network moby-counter redis:alpine`

- Visitez la page de l'application. Normalement un motif de moby d'une précédente session devrait s'afficher (après un délai)

- Affichez le contenu du volume avec la commande : `docker container exec redis ls -lhat /data`

Finalement nous allons écraser ce volume anonyme par le notre.
  
 A l'heure actuelle la bonne façon de créer des volumes consiste à les créer manuellement (volume nommés). `docker volume create redis_data`.

- Créez un nouveau conteneur attaché à ce volume nommé : `docker container run -d --name redis -v redis_data:/data --network moby-counter redis:alpine`

**Bind Mounting**
Lorsqu'un répertoire hôte spécifique est utilisé dans un volume (la syntaxe `-v HOST_DIR:CONTAINER_DIR`), elle est souvent appelée **bind mounting**.
C'est quelque peu trompeur, car tous les volumes sont techniquement "bind mounted". La différence, c'est que le point de montage est explicite plutôt que caché dans un répertoire appartenant à Docker.

`docker volume inspect redis_data`

- Créez un réseau `moby-counter2` et ajoutez un deuxième conteneur Redis:
  - situé à l'intérieur du nouveau réseau (`moby-counter2`) comme à la partie précédent.
  - partageant le volume de données du premier (Cf cours)
  - monté en read-only (`:ro` après le paramètre de la question précédente)

Le read only est nécessaire pour que les deux redis n'écrivent pas de façon contradictoire dans la base de valeurs.

- Ajoutez une deuxième instance de l'application dans le deuxième réseau connectée à ce nouveau redis.

- Visitez la deuxième application: vous devriez voir également le motif de moby apparaître.

`docker container stop redis moby-counter`

- Pour nettoyer tout ce travail, arrêtez les les deux redis et les deux moby-counter.
- Lancez trois `prune` pour les conteneurs d'abord puis les réseaux et les volumes.
- Comme les réseau et volumes n'étaient plus attachés à des conteneurs en fonctionnement ils ont étés supprimés. Généralement, il faut faire beaucoup plus attention au prune de volumes (données à perdre) que de conteneur(rien à perdre car immutable et dans le registry).
