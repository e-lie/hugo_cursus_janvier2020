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

## Ajouts pendant humancoders

- parler de permissions et de sudo
- planifier vitesse rapide si les gens savent de vitesse plus lente
- parler de CI gitlab ? de git ? de gitpod ?
- REFAIRE TP SUR REDIS ET VOLUMES AVEC AUTRE CHOSE + plus explicite pour le réseau
  => mon search cards pas CRUD, plutot le flask machin non ?
- expliquer comment on stocke les images et les conteneurs et les volumes cocnrètement
- s'étendre plus longuement sur la partie comment marchent les hashes et l'overlayfs à coup de copiers collers de linux
- ouvrir sur k8s ou elk ou gitlab CI selon leur intérêt.
  Ou simplement sur des trucs tordus d'app stateful (snapshots jsp)
- partie sur git qd même ? important car infra as code, ouverture sur CI ? laisser les avancés plancher sur une CI gitlab ? vu qu'ils sont un peu ce profil
- QCM ARGH
- faire faire du git à ceux qui sont trop en avance ?
- podman / buildah ?
- upload sur un registry

- Au début demo avec scaleway hop je mets un docker, hop un codimd et un nginx et boum.
- Souligner que'intérêt est de déployer facilement.
- Pour rappels linux, explorer justement bien le réseau avec le ssh dans scaleway, et les k ter face spour comprendre les interfaces docker. Pour comprendre, les volumes parler des permissions pour comprendre le chroot.
- Pour comprendre les images prendre l'extrait des cours Linux qui parlent de tar.gz et décortiquer une image pour comprendre eque c'est qu'un rootfs gzipé, avec du overlayfs.
- Pour le to volume et réseau faire l'app flask avec le mysql. Si marche pas garder redis mais dire qu'il y a du cache et que ça soulingen l'importance et les limite du stateless vs stateful. Fsite fzire un script pour migrer du stateful redis Moby counter?
- Leur demander de générer une clé ssh et me l'envoyer la clé publique par mail ou alors le servir avec nginx et me donner leur IP.
- Leur donner 6 vm scaleway, leur faire foutre portainer dessus (commande ssh de la socket ? ou socat ?) puis protéger avec nginx (ou traefik) avec auth http. Ajouter swarm et hop c'est redondant. Autre vm avec swarm et pouf c'est resilient la.
- Du coup faire faire déployer d'abord la flask app puis elk et flask avec elk puis WordPress, nextcloud, codimd, MediaWiki ou dokuwiki.
  En gros a la fin ils on un ynh cheap et avec ci pour les plus avances.
- Faire faire une ci gitlab pour les avancés pour qu'ils comprennent la puissance d'avoir packagé docker. Du coup faire git et framagit forcément, plutôt en jour 1 même.
  piquer des trucs du tuto de fergus ?
- dire que la ref docker est cool à checker car elle change tout le temps
- dire que docker compose wordpress tout fait là https://hub.docker.com/_/wordpress ou là https://docs.docker.com/compose/wordpress/
- same with nextcloud https://hub.docker.com/_/nextcloud or here https://github.com/nextcloud/docker

- ajout d'exemple du build multi image

- remplacer dans tp3 par un truc avec microblog et un volume sur sqlite file (app/app.db ou app.db) puis mysql puis docker-compose avec elasticsearch (en insistant sur le fait que c'est intenable à la main et là l'utilité de dockercompose)
- ajouter les "expand"

- tester redis et dire pour le cache rdb
- !!! moby counter contacts redis as client JS not server !!!
- +WordPress config a la main et extraire fichier d econf puis avec vars env en tp3 volume et réseau ?
- Virzr optio link docker
- chapitre cours containers Windows

TESTS connexions :

- un proxy HTTP c'est censuré aussi ?
- Et wsl en fait ?
- brng your own amchine + tethering
- guacamole VNC ?
- demander ouverture du SSH ou de VNC pour la salle

- tp2 modifier pour pas boot.sh la premiere fois
- tp4 links désuets ? et quid du depends_on ?
- TP6 trouver meilleur exemple de stack !!!
- TESTER example-voting-app
- sur le réseau rajouter une slide sur les drivers réseau et les possibilités des drivers réseau (plutôt K8s quand avancé) : VXLAN ou encapsulation IPIP (IP in IP), Cilium avec eBPF
- rajouter dans slides + TP sur dockerfile des explications et exemples et manips sur VOLUME et sur EXPOSE.
- Inverser TP Dockerfile et TP Volume + réseau
- Virer ou renforcer slide qui parlait de microservices.
- Ajouter un memo de ce qui est déprécié ou non entre docker compose v2 et v3. Check pour depends_on : Version 3 no longer supports the condition form of depends_on.
- donner texto les commandes pour fix le docker compose de voting_app ou commit sur mon repo et changer les refs

+WordPress config a la main et extraire fichier d econf puis avec vars env en tp3 volume et réseau ?
Virzr optio link docker
slides sur containers Windows

- acheter livrets containers julia et verif droits images utilisées / refaire schémas

- stateful ajout slides : ex. avec pstgres xl et les autres et ELK, extraits des slides stateful
- tp d'ouverture : tester k8s avec minikube ou k3s et l'app vote example
- Dans docker compose donner exemple de traefik
- Dans docker compose donner exemple de filebeat
- Dans docker compose donner ajoute rrole de label da's les slides.
- Rajouter facultatif traefik et changer ordre des tp

- corriger tp 4 Pas env env prod mais env context prod, unifier gunicorn et uwsgi
- rajout explications slide exposre un port qu'est-ce que c'est, syntaxe réseau et volume à gauche l'hote a droite le container
- "Some of the major differences between v2 and v3 is that v3 does not support using the volumes_from and extends properties. A whole bunch of the CPU and memory properties have also been moved to a deploy property in v3 instead.
  It’s also worth mentioning if you happen to be using Docker Swarm, you’ll need to use v3+."
- dans docker swarm on peut pas build, (logique : dans k8s non plus)

- Laïus sur infrastructures en conclusion et docker permet pas de simplifier mais des outils pour gérer la complexité de l'informatique quand elle March e pas avec illustration xkcd.

- Bien souligner awesome docker dans les ressources avec gif dockercraft.

- Faire fin moins en queue de poisson pour slide orchestration ou conclusion.

- Revoir comment on fait des variables réutilisables dans le même docker file / dockercompose.

- Expliquer la place des secrets docker Swarm.

- swarm : Trouver d'autres exemples que restart on failure.

- Remettre la diff avec docker ce et ee.

- Updater a v3 et network le docker compose exemple du cours.

- Écrire les commandes possibles de docker swarm dans les slides
