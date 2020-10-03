---
title: TP - Volumes et réseaux
---

Pour expérimenter avec le réseau, nous allons lancer une petite application nodejs d'exemple (moby-counter) qui fonctionne avec une queue redis (comme une base de données mais pour stocker des paires clé/valeur simples).

Récupérons les images depuis Docker Hub:

- `docker image pull redis:alpine`
- `docker image pull russmckendrick/moby-counter`

Pour connecter les deux applications créons un réseau manuellement:

- `docker network create moby-network`

Puis lancons les deux applications en utilisant notre réseau

- `docker run -d --name redis --network <réseau> redis:alpine`
- `docker run -d --name moby-counter --network <réseau> -p 80:80 russmckendrick/moby-counter`

- Visitez la page de notre application. Qu'en pensez vous ? Moby est le nom de la mascotte Docker :). Faites un motif reconnaissable en cliquant.

Comment notre application se connecte-t-elle au conteneur redis ? Elle utilise ces instructions JS dans son fichier `server.js`:

```javascript
var port = opts.redis_port || process.env.USE_REDIS_PORT || 6379;
var host = opts.redis_host || process.env.USE_REDIS_HOST || "redis";
```

En résumé par défaut, notre application se connecte sur l'hôte `redis` avec le port `6379`

Explorons un peu notre réseau Docker.

- Exécutez (`docker exec`) la commande `ping -c 3 redis` sur notre conteneur applicatif (`moby-counter` donc). Quelle est l'adresse IP affichée ?

- De même, affichez le contenu des fichiers `/etc/hosts`. Nous constatons que Docker a automatiquement configuré l'IP externe **du conteneur dans lequel on est** avec l'identifiant du conteneur. De même, affichez `/etc/resolv.conf` : le résolveur DNS a été configuré par Docker.

- Pour vérifier que l'on peut bien connaître l'IP de `redis`, interrogeons le serveur DNS de notre réseau `moby-network` en lançant la commande `nslookup redis 127.0.0.11` toujours grâce à `docker exec`.

- Créez un deuxième réseau `moby-network2`
- Créez une deuxième instance de l'application dans ce réseau : `docker run -itd --name moby-counter2 --network moby-network2 -p 9090:80 russmckendrick/moby-counter`

Vous ne pouvez pas avoir deux conteneurs avec les mêmes noms, comme nous l'avons déjà découvert. Par contre notre deuxième réseau fonctionne complètement isolé de notre premier réseau, ce qui signifie que nous pouvons toujours utiliser le nom de domaine `redis` ; pour ce faire, nous devons spécifier l'option `--network-alias` :

- Créons un deuxième redis avec le même domaine: `docker run -d --name redis2 --network moby-network2 --network-alias redis redis:alpine`

- Lors que vous pingez `redis` depuis cette nouvelle instance de l'application, quelle IP obtenez-vous ?

- Récupérez comme auparavant l'adresse IP du nameserver local pour `moby-counter2`.

- Puis lancez `nslookup redis <nameserver_ip>` pour tester la résolution de DNS.

- Vous pouvez retrouver la configuration du réseau et les conteneurs qui lui sont reliés avec `docker network inspect moby-network`.
  Notez la section IPAM.

- Arrêtons nos conteneurs `docker stop moby-counter2 redis2`.

- Pour faire rapidement le ménage des conteneurs arrêtés lancez `docker container prune`.

- De même `docker network prune` permet de faire le ménage des réseaux qui ne sont plus utilisés par aucun conteneur.

## Docker Volumes

- Arrêtez et supprimez le conteneur redis (n°1).
- Lancez `docker volume prune` pour faire le ménage des volumes éventuellement créés dans les TPs précédent (pour ne pas interférer avec ce exercice).
- Visitez votre application dans le navigateur. Elle est maintenant déconnectée de son backend.
- Recréez le conteneur `redis` dans le réseau `moby-network`
- Rechargez la page, plusieurs fois. Que s'est-il passé ?
- Faites de nouveau un motif reconnaissable dans l'application web

Le Dockerfile pour l'image officielle Redis ressemble à ça :

```Dockerfile
FROM alpine:3.5

RUN addgroup -S redis && adduser -S -G redis redis
RUN apk add --no-cache 'su-exec>=0.2'
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

Notez que, vers la fin du fichier, il y a deux instructions VOLUME et WORKDIR ; cela signifie que lorque notre conteneur a été lancé, un volume "caché" a effectivement été créé par Docker.

- Inspectez le conteneur redis pour trouver l'identifiant du volume caché.

- Supprimez le conteneur redis.

- Avons-nous vraiment perdu les données de notre conteneur ? Non ! Créez un nouveau conteneur redis en le rattachant au volume redis "caché" que vous avez retrouvé.
  `docker container run -d --name redis -v <volume_id>:/data --network moby-network redis:alpine`

- Visitez la page de l'application. Normalement un motif de moby d'une précédente session devrait s'afficher (après un délai)

- Affichez le contenu du volume avec la commande : `docker container exec redis ls -lhat /data`

Finalement nous allons écraser ce volume anonyme par le nôtre.

A l'heure actuelle la bonne façon de créer des volumes consiste à les créer manuellement (volumes nommés) : `docker volume create redis_data`.

- Supprimez l'ancien conteneur `redis` puis créez un nouveau conteneur attaché à ce volume nommé : `docker container run -d --name redis -v redis_data:/data --network moby-network redis:alpine`

**Bind mounting**
Lorsqu'un répertoire hôte spécifique est utilisé dans un volume (la syntaxe `-v HOST_DIR:CONTAINER_DIR`), elle est souvent appelée **bind mounting**.
C'est quelque peu trompeur, car tous les volumes sont techniquement "bind mounted". La différence, c'est que le point de montage est explicite plutôt que caché dans un répertoire appartenant à Docker.

- Lancez `docker volume inspect redis_data`.

- Créez un réseau `moby-network2` et ajoutez un deuxième conteneur Redis:
  - situé à l'intérieur du nouveau réseau (`moby-network2`) comme à la partie précédent.
  - partageant le volume de données du premier (cf. cours)
  - monté en read-only (`:ro` après le paramètre de la question précédente)
    <!-- - redis-server --appendonly yes ou REDIS_REPLICATION_MODE=slave nécessaire ? -->

Le read-only est nécessaire pour que les deux redis n'écrivent pas de façon contradictoire dans la base de valeurs.

- Ajoutez une deuxième instance de l'application dans le deuxième réseau connectée à ce nouveau redis.

- Visitez la deuxième application : vous devriez voir également le motif de moby apparaître. Mais dans cette application, vous ne pourrez pas rajouter de nouveaux motifs en cliquant à cause de l'option read-only.

`docker container stop redis moby-counter`

- Pour nettoyer tout ce travail, arrêtez les deux redis et les deux moby-counter.

- Lancez trois `prune` pour les conteneurs d'abord puis les réseaux et les volumes.

Comme les réseaux et volumes n'étaient plus attachés à des conteneurs en fonctionnement, ils ont été supprimés.

**_Généralement, il faut faire beaucoup plus attention au prune de volumes (données à perdre) qu'au `prune` de conteneurs (rien à perdre car immutable et en général dans le registry)._**
