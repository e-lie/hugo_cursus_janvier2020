---
title: Cours 2 - Volumes et réseaux 
draft: false
---


## Cycle de vie d'un conteneur

- Un conteneur a un cycle de vie très court: il doit pouvoir être créé et supprimé rapidement même en contexte de production.


Conséquences:

- On ne peux pas garder les données persistantes dans la boîte.
- On a besoin de méchanisme d'autoconfiguration en particuler réseau car les ip des différentes boîtes changent tout le temps.


Solutions:
- Des volumes (partagés ou non, distribués ou non) montés dans les conteneurs
- Des réseaux dynamiques par défaut automatiques (DHCP mais surtout DNS automatiques)
  


## Volumes

**Bind Mounting**
Lorsqu'un répertoire hôte spécifique est utilisé dans un volume (la syntaxe `-v HOST_DIR:CONTAINER_DIR`), elle est souvent appelée **bind mounting**.
C'est quelque peu trompeur, car tous les volumes sont techniquement "bind mounted". La différence, c'est que le point de montage est explicite plutôt que caché dans un répertoire appartenant à Docker (Cf TP).

Exemple:

```bash
docker run -it -v  /tmp/data:/data  ubuntu /bin/bash
cd /data/
touch testfile

```



### La sous commande `volume`

- `docker volume ls`
- `docker volume inspect`
- `docker volume prune`
- etc.



### Partager des données avec un volume

- Pour partager des données on peut monter le même volume dans plusieurs conteneurs.


- Pour lancer un conteneur avec les volumes d'un autre monté on peut utiliser `--volumes-from <container>`


- On peut aussi créer le volume à l'avance et l'attacher après coup à un conteneur.


- par défaut le driver de volume est `local` c'est à dire créer un dossier sur le disque de l'hôte


```bash
docker volume create --driver local \
    --opt type=btrfs \
    --opt device=/dev/sda2 \
    monVolume
```



### Plugins de volumes

On peut utiliser d'autres systèmes de stockage en installant de nouveau plugin driver de volume.

Exemples:

- BeeGFS (système de fichier distribué générique)
- Amazon EBS (vendor specific)
- etc.



### Permissions

- Un volume est créé en fonction des permissions du dossier préexistant (internet ou externe).
```Dockerfile
FROM debian
# add our user and group first to make sure their IDs get assigned consistently, regardless of other deps added later
RUN groupadd -r graphite && useradd -r -g graphite graphite
RUN mkdir -p /data/graphite && chown -R graphite:graphite /data/graphite
VOLUME /data/graphite
USER graphite
CMD ["echo", "Data container for graphite"]
```




## Réseau



### Gestion des ports réseaux (port mapping)

- Par défaut les conteneurs n'ouvrent pas de port même s'il sont déclarés avec `EXPOSE` dans le Dockerfile.

- Option `-p <port_host>:<port_guest>` de `docker run`.

- Instruction `port:` d'un compose file.



### Bridge et overlay

- Un réseau bridge est une façon de créer un pont entre deux cartes réseaux pour construire un réseau à partir de deux.

- Par défaut les réseaux docker fonctionnent en bridge (le réseau de chaque conteneur est bridgé à un réseau virtuel docker)

- Par défaut les adresses sont en 172.0.0.0/8, typiquement chaque hôte définit un CIDR DHCP 172.17.0.0/16.
  
- Un réseau overlay est un réseau virtuel privé déployé par dessus un réseau existant (typiquement public). Pour par exemple pour faire un cloud multi DC.




### Le réseau docker est très automatique

- Serveur DNS et DHCP intégré dans le "user-defined networks" (IPAM)

- Donne un nom de domaine autoamtique à chaque conteneur.

- Mais ne pas avoir peur d'aller voir comment on perçoit le réseau de l'intérieur (Cf TP). nécessaire pour bien contrôler le réseau.

- Ingress: un load balancer automatiquement connecté aux noeuds d'un swarm [doc overlay](https://docs.docker.com/network/overlay/)



### Lier des conteneurs

- On peut créer un lien entre des conteneurs
  - avec l'option `--link` de `docker run`
  - avec l'instruction `link:` dans un docker composer (Cf TP)
  - MAIS cette fonctionnalité est **dépréciée**

- Aujourd'hui il faut utiliser un réseau dédié créé par l'utilisateur ("user-defined bridge network") (Cf TP réseau)

### Partager des données entre conteneurs

- Pour partager des données il faut monter des volumes partagés.



### Plugins réseaux

- Les réseaux par défaut docker 
- Plusieurs autres solutions spécifiques de réseau sont disponibles pour des questions de performance et de sécurité
  - exemple: **Weave Net** pour un cluster docker swarm
  - fournit une autoconfiguration très simple
  - de la sécurité
  - un DNS qui permet de simuler de la découverte de service
  - Du multicast UDP
  - ...
  
