---
title: 'TP1 - réseaux et volumes'
draft: true
---

<!--
#TODO
# change network name to moby-network and add a schematics for clarity
# Add explanation on redis functionning (in RAM db => create a dump.rdb file only used when restarted)
# Redis need to restart to update from file stored in volume.
# Add points to recreate moby and redis at the start of second part.
-->

 
## Docker networking

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

{{% expand "Réponse  :" %}}
```
docker exec moby-counter cat ping -c3 redis

=> le conteneur connait le nom de domaine associée à l'adresse ip du conteneur redis
```
{{% /expand %}}


- De même, affichez le contenu des fichiers `/etc/hosts` et `/etc/resolv.conf`. Nous constatons que docker a automatiquement configuré le nom d'hôte de la machine avec l'identifiant du conteneur.

{{% expand "Réponse  :" %}}
```
docker exec moby-counter cat /etc/hosts

127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
172.19.0.3      17a03db75433 => id du conteneur moby-counter avec son ip

docker exec moby-counter cat /etc/resolv.conf

search numericable.fr
nameserver 127.0.0.11  => l'ip du server DNS (nameserver) docker est automatiquement configurée
options ndots:0
```
{{% /expand %}}


- Pour le vérifier interrogeons le serveur DNS de notre réseau `moby-counter` en lançant la commande `nslookup redis 127.0.0.11` toujours grâce à `docker exec`.

{{% expand "Réponse  :" %}}
```
docker exec moby-counter nslookup redis 127.0.0.11

Server:    127.0.0.11
Address 1: 127.0.0.11

Name:      redis
Address 1: 172.19.0.2 redis.moby-counter => on récupère bien l'ip de redis
```
{{% /expand %}}


- Créez un deuxième réseau `moby-counter2` et une deuxième instance de l'application dans ce réseau : `docker run -itd --name moby-counter2 --network moby-counter2 -p 9090:80 russmckendrick/moby-counter`


- Lors que vous pingez `redis` depuis cette nouvelle instance de l'application, quelle ip obtenez vous ?

{{% expand "Réponse  :" %}}
```
docker exec moby-counter2 ping -c 3 redis
                                                                            :(
ping: bad address 'redis'
```

 => redis n'est pas joignable
{{% /expand %}}


- Récupérez comme auparavant l'adresse ip du nameserver local pour `moby-counter2`.

- Puis lancez `nslookup redis <nameserver_ip>` pour tester la résolution de DNS. Comparer l'adresse ip avec les adresses habituelles Docker.

{{% expand "Réponse  :" %}}
```bash
docker exec moby-counter2 nslookup redis 127.0.0.11

Server:    127.0.0.11
Address 1: 127.0.0.11

nslookup: can't resolve 'redis': Name does not resolve
```
{{% /expand %}}


Bie don que vous ne puissiez pas avoir deux conteneurs avec les mêmes noms, comme nous l'avons déjà découvert, notre deuxième réseau fonctionne complètement isolé de notre premier réseau, ce qui signifie que nous pouvons toujours utiliser lemaine `redis` ; pour ce faire, nous devons ajouter le drapeau `--network-alias` :

- Créons un deuxième redis avec le même domaine: `docker container run -d --name redis2 --network moby-counter2 --network-alias redis redis:alpine`

- Relancez la résolution précédente avec `nslookup`.

{{% expand "Réponse  :" %}}
```bash
docker exec moby-counter2 nslookup redis 127.0.0.11

Server:    127.0.0.11
Address 1: 127.0.0.11

Name:      redis
Address 1: 172.20.0.3 redis2.moby-counter2 => maintenant que nous avons ajouté le network alias redis  pointe bien vers le conteneur redis2
```
{{% /expand %}}


- Vous pouvez retrouver la configuration du réseau et les conteneurs qui lui sont relié avec `docker network inspect moby-counter`.
  Notez la section IPAM.

{{% expand "Réponse  :" %}}
```json
[
    {
        "Name": "moby-counter",
        "Id": "5be26b00aed7f4b20efb042c520ac917e4cb6f5847692ee37d614c94552b7a67",
        "Created": "2019-07-31T00:01:09.998095312+02:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.19.0.0/16",
                    "Gateway": "172.19.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "17a03db754331f89116ccf4e3b216b2a5600014bba8c789c9e9394198f9f1562": {
                "Name": "moby-counter",
                "EndpointID": "526be451323f5ecfb0a6a0a937b655bce148cca14db0b79f24c8483d4f95bce6",
                "MacAddress": "02:42:ac:13:00:03",
                "IPv4Address": "172.19.0.3/16",
                "IPv6Address": ""
            },
            "5ee770c028639adc566c77bf0de322487752d6b21be24166b48de16f2fec891f": {
                "Name": "redis",
                "EndpointID": "b730d6a281dcddbcbdcf29b00287b8d64f97d5c94296f275cbd794b9f6a93373",
                "MacAddress": "02:42:ac:13:00:02",
                "IPv4Address": "172.19.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]
```
{{% /expand %}}


- Arrêtons nos conteneurs `docker container stop moby-counter2 redis2`.

- Pour faire rapidement le ménage des conteneurs arrêtés lancez `docker container prune`.

- De même `docker network prune` permet de faire le ménage des réseaux qui ne sont plus utilisés par aucun conteneur.

## Docker Volumes

- Lancez `docker volume prune` pour faire le ménage de volume éventuellement créé dans les TPs précédent (pour ne pas interférer avec ce exercice).
- Lancez `docker volume ls` pour vérifier qu'aucun volume n'est créé sinon supprimez les avec `docker volume rm --force <id_volume>`
- Arrêtez et supprimez le conteneur redis (n°1).
- Visitez votre application dans le navigateur. Elle est maintenant déconnectée de son backend.
- Recréez le conteneur `redis` dans le réseau `moby-counter`
- Rechargez la page, plusieurs fois. Que s'est-il passé ?

{{% expand "Réponse  :" %}}
L'application refonctionne mais les données (les logo docker) on été effacées.
{{% /expand %}}


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

{{% expand "Réponse  :" %}}
```bash
docker volume ls
```
{{% /expand %}}


- Créez un nouveau conteneur redis en rattachant l'un des précédents volumes redis.
`docker container run -d --name redis -v <volume_id>:/data --network moby-counter redis:alpine`

- Visitez la page de l'application. Normalement un motif de moby d'une précédente session devrait s'afficher (après un délai)

- Affichez le contenu du volume avec la commande : `docker container exec redis ls -lhat /data`

{{% expand "Réponse  :" %}}
```bash
docker container exec redis ls -lhat /data

total 12K    
drwxr-xr-x    1 root     root        4.0K Jul 30 22:29 ..
drwxr-xr-x    2 redis    redis       4.0K Jul 30 22:28 .
-rw-r--r--    1 redis    redis        142 Jul 30 22:28 dump.rdb => fichier contenant les données redis
```
{{% /expand %}}



Finalement nous allons écraser ce volume anonyme par le notre.
  
 A l'heure actuelle la bonne façon de créer des volumes consiste à les créer manuellement (volume nommés). `docker volume create redis_data`.

- Créez un nouveau conteneur attaché à ce volume nommé : `docker container run -d --name redis -v redis_data:/data --network moby-counter redis:alpine`

**Bind Mounting**
Lorsqu'un répertoire hôte spécifique est utilisé dans un volume (la syntaxe `-v HOST_DIR:CONTAINER_DIR`), elle est souvent appelée **bind mounting**.
C'est quelque peu trompeur, car tous les volumes sont techniquement "bind mounted". La différence, c'est que le point de montage est explicite plutôt que caché dans un répertoire appartenant à Docker.

- Lancez `docker volume inspect redis_data`.

- Créez un réseau `moby-counter2` et ajoutez un deuxième conteneur Redis:
  - situé à l'intérieur du nouveau réseau (`moby-counter2`) comme à la partie précédent.
  - partageant le volume de données du premier (Cf cours)
  - monté en read-only (`:ro` après le paramètre de la question précédente)

Le read only est nécessaire pour que les deux redis n'écrivent pas de façon contradictoire dans la base de valeurs.
{{% expand "Réponse  :" %}}
```
docker network create moby-counter2
docker container run -d --name redis2 -v redis_data:/data:ro --network moby-counter2 --network-alias redis redis:alpine
```
{{% /expand %}}


- Ajoutez une deuxième instance de l'application dans le deuxième réseau connectée à ce nouveau redis.
  
{{% expand "Réponse  :" %}}
```
docker run -itd --name moby-counter2 --network moby-counter2 -p 9090:80 russmckendrick/moby-counter
```
{{% /expand %}}


- Visitez la deuxième application: vous devriez voir également le motif de moby apparaître.

{{% expand "Réponse  :" %}}
```
docker container stop redis moby-counter
```

Notez que `docker stop` et `docker container stop` sont équivalents
{{% /expand %}}

- Pour nettoyer tout ce travail, arrêtez les les deux redis et les deux moby-counter.

{{% expand "Réponse  :" %}}
```
docker stop redis redis2 moby-counter moby-counter2
```
{{% /expand %}}

- Lancez trois `prune` pour les conteneurs d'abord puis les réseaux et les volumes.

{{% expand "Réponse  :" %}}
```
docker container prune
docker network prune
docker volume prune
```
{{% /expand %}}


{{% notice info %}}
Comme les réseau et volumes n'étaient plus attachés à des conteneurs en fonctionnement ils ont étés supprimés. Généralement, il faut faire beaucoup **plus attention** au prune de **volumes (données à perdre)** que de **conteneur(rien à perdre car immutable et dans le registry)**.
{{% /notice %}}