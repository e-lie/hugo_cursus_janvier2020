# TODO:

Docker : (Hadrien)

- tp1 existant à améliorer (pas debian sleep) et à la fin portainer
- TP2 : partir d'un dockerfile du projet knowledge/search d'hadrien qui utiliserait mysql (flask v1) : améliorer TP 2 d'Elie existant et changer l'app, et gestion volumes et réseau en CLI pou rlancer mysql
- TP3 : docker-compose avec flask v1 + mysql, puis la passer en elk (flask v2)
- TP4 : Swarm vite fait (app web qui loadbalance où tu sais quel nœud t'a servi ou voting app) et intro à K8s en montrant les limites de swarm
-

# Slides

- Dire que images OCI que c'est un gzipped filesystem basically + metadata
- Ajouter dockercoins en td swarm + imgur.schmilblick

# TD général

- ajouter un nginx dans le gatsby-wave pour docker-compose (avec mysql et avec elk), et faire du HTTP auth pour le elasticsearch
- faire d'abord flask + mysql en CLI / Portainer puis en docker-compose
- leur dire de faire du portainer s'ils galèrent
- Des TD plus funs : funkwhale ? faire du docker machine et du basic swarm ? faire ressortir les intérêts du truc. Simuler une montée en charge en trouvant une manière de monitorer ?
  ~~- Pad CodiMD monté en Docker pour toute la classe ? Autres services ?~~

- faire un test de NFS ? ou autre test de volume partagé genre cluster Elasticsearch?

Ajout TD/démo Swarm avec voteapp et/ou dockercoins pour la démo des docker machine

## Idées TD

docker-compose secrets + cache : https://github.com/docker-training/suggested-solutions/blob/master/dops/content-cache.yaml

https://github.com/docker-training/webapp

https://github.com/docker/labs/tree/master/Docker-Orchestration
https://container.training/
basic webapp : https://github.com/docker/labs/blob/master/beginner/chapters/webapps.md

docker swarm example : voting app https://github.com/dockersamples/example-voting-app https://github.com/docker/labs/blob/master/beginner/chapters/votingapp.md https://github.com/dockersamples/global-2018-hol/blob/master/beginner-linux/part-three.md https://github.com/docker-training/docker-paas

app de paiement avec secrets : https://github.com/dockersamples/atsea-sample-shop-app

Faire un gros docker swarm avec toute la classe ? https://github.com/dockersamples/docker-swarm-visualizer

https://github.com/docker-training/healthcheck

app simple à la con : https://github.com/docker-training/namer

- c'est quand même utile de faire le TD packager une app

https://docs.docker.com/compose/rails/

faire lancer wordpress avec variables d'env. prépopées pour pas avoir à install

Faire les démos compliquées comme des sortes de TP assistés et guidés.

## TD existants détails/corrections

- Verifier comportement redis avec un volume read-only (cache in-memory?): https://hub.docker.com/_/redis/ https://stackoverflow.com/questions/27681402/how-to-disable-redis-rdb-and-aof https://redis.io/commands/readonly
- Clarifier quand boot.sh est dans app et qu'il se fait écraser par l'instruction volume du dockerfile
- Clarifier entrypoint et CMD
- redis-server --appendonly yes ou REDIS_REPLICATION_MODE=slave ?
- Problèmes de place sur le disque de la VM par défaut (prendre image python3 plus légère ? fournir VM avec plusieurs disques ?)
- erreurs python lors du build de microblog v0.18 (partie facultative tp4) : alpine ne possède pas les deps requises, réadapter avec ptyhon:3 qui marche
- Utiliser un cache pour pip et refaire tp4 pour ne pas jouer sur les permissions. Ajouter un autre exemple sympa. Abandonner uwsgi ?

# Sécurité

La sécurité de Docker c'est aussi celle de la chaîne de dépendance des packages npm etc. : on fait confiance à trop de petites briques

## Sécurité des conteneurs

    - alpine par exemple c'est uclibc donc un glibc recodé par un seul mec : y a des erreurs de compilation sur par exemple compilation d'une JVAPP java et on sait pas pourquoi : du coup l'argument de dire "c'est le même binaire de A à Z", à relativiser car alpine a pas du tout les mêmes binaires par exemplee t donc plus fragile

- demo de pb d'isolation entre deux process (version dependency) quand pas containers / ou entre process et OS pour introduire chroot, même si ; To be clear, this is NOT a vulnerability. The **root user is supposed to be able to change the root directory for the current process and for child processes**. Chroot only jails non-root processes. Wikipedia clearly summarises the limitations of chroot." Wikipédia : "On most systems, chroot contexts do not stack properly and chrooted programs with sufficient privileges may perform a second chroot to break out. To mitigate the risk of this security weakness, chrooted programs should relinquish root privileges as soon as practical after chrooting, or other mechanisms – such as FreeBSD jails – should be used instead. "
  > En gros chroot fait que changer le root, si on peut rechroot on peut rechroot. Aussi, pb. d'isolation network et IPC. si privilégié pour le faire (du coup tempérer le "filesystem-based" d'Unix)
  > http://pentestmonkey.net/blog/chroot-breakout-perl

(parenthèse systemd : docker daemon et systemd en cocurrence pour être tous les deux des process d'init : pas possible de lancer un conteneur depuis systemd) (2e parenthèse : pid 1)

-
- comprendre l'isolation container : concrètement quand on fait tourner des containers de gens différents dans le même docker c'est "cmme de l'hébergement mutuel php sur la même machine avec apache qui segmente" (effectivement en interne ça l'est)

- différence en sécurité des VM c'est qu'on s'appuie pour les VM sur un sandboxing au niveau matériel (failles dans IOMMU/VT-X/instrctions x84) (si l'on oublie qu'un soft comme virtualbox a une surface d'attaque plus grade, par exemple exploit sur driver carte réseau) et dans l'autre faille de kernel
- Exemple avec option profil seccomp

# Ajouts slides

- systemd-nspawn : autre bundle type rkt ou lxc
- Concept d'immutabilité
- ajouter l'explication de la commande "HEALTHCHECK"
  https://docs.docker.com/engine/reference/commandline/config/
  https://docs.docker.com/engine/context/working-with-contexts/
  docker lxc driver
- multistage build : https://docs.docker.com/develop/develop-images/multistage-build/

## Stateful

- le stateless c'est bien beau mais avec une bdd ça se gère pas magiquement du tout

distinction satteful/stateless
https://container.training/swarm-selfpaced.yml.html#450

## Volumes répliqués

- pb des volumes partagés / répliqués
  1. solution 1 : applicative
     - réplication / sharding / clustering : difficiel à configurer
  2. volume driver : infinit racheté par docker, flocker, convoy, visent à intégrer une techno de réplication : NFS/DRDB/EBS...
     - c'est un moyen, pas une solution : le NFS est à configurer par nous

## Secrets

## Monitoring

- question du monitoirng et d'un container UP mais zombie, ne fonctionne pas. Du coup logs. A centraliser pas à la main. Petit TP logs?
  - /var/lib/docker/containers/<id>/<id>-json.log
    pareil, solution 1: apllicatif, ELK
    solution 2: logging driver, le default c'est json-file, sinon ça peut etre syslog, gelf, fluentd.
    solution 3: des agents : des conteneurs auxquels on donne accès à la socket du host (docker.sock). Le fonctionenement par agent est très puissant. Ex de stack: cAdvisor/influxDB/Grafana.

Docker logs monitoring :

- Dockbeat
- https://logz.io/blog/docker-logging/ : ELK avec Filebeat et docker collector, ou bien custom logging driver for docker avec syslog

* https://docs.docker.com/swarm/plan-for-production/
* Swarm TLS

## Network

Encrypted overlay network: https://github.com/dockersamples/global-2018-hol/tree/master/security/networking

# Pédagogie

Tu crées des boîtes pour que les sysadmins aient "que" à maintenir une infra Swarm ou Kubernetes et que le travail des devs soit le travaild es devs

Remettre intro sur le DevOps : le concept 'd'infra as code, et le concept cattle vs. pet ou autre métaphore

- métaphore du garage utile pour telle véhicule et qu'on aimerait chagner de garage à chaque fois
- pet vs. cattle : du bétail par rapport à un chat qu'on amène au véto etc. Le conteneur amène cette logique
  (- approfondir à l'oral la logique de volume from ? : un conteneur vide, pointeur vers les volumes, du coup il suffit de faire volume from pr un nv conteneur)

Séparer bien les concepts et parties de cours en disant Concept A : Les conteneurs, Concept B : L'infra as code, Concept C : Les dockerfile et les layers

Faire des briques physiques avec des fils et des boites transparentes ou set tout fait

Des schémas explicites

Plus de temps sur les concepts et analogies : garage avec outils spécifiques pour tel type de voiture et ne se faire voler qu'un garage à la fois

problem-centered rather than content-oriented.

- docker in docker on ne fait que lui donner accès à la socket du host : c'est en fait du "sidecar"

Les faire participer au planning de formation dans la limite du possible

# TODO

- add healthcheck app: https://github.com/docker-training/healthcheck

- Réviser réseau NAT de Docker
- test rootless docker : https://docs.docker.com/engine/security/rootless/#prerequiresites
- REFAIRE TOUT LE QUIZ

- Distroless pour **sécurité** : https://github.com/GoogleContainerTools/distroless

<!-- # Module Docker avancé

## Ouverture sur besoin d'un Kubernetes en explorant DB répliquée PostgreSQL

- http://code4projects.altervista.org/install-postgresql-cluster-docker/
- https://severalnines.com/database-blog/how-deploy-postgresql-docker-container-using-clustercontrol
- https://medium.com/@2hamed/replicating-postgres-inside-docker-the-how-to-3244dc2305be
- https://access.crunchydata.com/documentation/crunchy-postgres-containers/4.2.0/examples/backup-restoration/pgbasebackup/
- https://access.crunchydata.com/documentation/crunchy-postgres-containers/4.2.0/examples/backup-restoration/pgbackrest/
- https://access.crunchydata.com/documentation/crunchy-postgres-containers/4.2.0/examples/postgresql/statefulset-cluster/
- https://github.com/paunin/PostDock#postgres
- https://info.crunchydata.com/blog/an-easy-recipe-for-creating-a-postgresql-cluster-with-docker-swarm

## Ceph

# Autres

Bouquin Learn openshift très cool pour intro tassée -->
