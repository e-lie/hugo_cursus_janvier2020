---
title: "1 - Découverte de l'écosystème Elastic"
draft: false
weight: 3010
---

## _Apprendre à nager dans un océan d'évènements et de texte!_

<!--
# Elastic stack et le logging
*Apprendre à nager dans un océan d'évènements et de texte!*

---

# Plan de la formation

Intro)
I) Les évènement d'un système et le logging
II) Elasticsearch, stocker une masse de données texte
III) Elasticsearch et Kibana pour la recherche et l'analyse
IV) Architecture d'un cluster ELK
Conclusion) Elk et devops -->

---

<!--
# Plan de la formation

- _Intro)_
- _I)_ **Les évènement d'un système et le logging**
- _II)_ **Elasticsearch, stocker une masse de données texte**
- _III)_ **Installer un cluster virtuel Elasticsearch et Kibana**
- _IV)_ **Elasticsearch et Kibana pour la recherche et l'analyse** -->

- Ouvrage recommandé : _PacktPub - Learning Elastic Stack 6_

---

## Dans les épisodes précédents

- Linux ? on va reparler de **tail**, **grep** et des **logs**
- HTTP ? on va utiliser une **API REST** (basé sur **HTTP**)
- JSON ? on va utiliser des documents formatés en **JSON**
- Les **bases de données** ?
- **Machines virtuelles** ? on va parler un peu à la fin de cluster dans le cloud
(c'est compliqué un cluster elasticsearch mais on va surtout voir ça théoriquement)
<!-- - Nagios ? -->

---

## Rappel

- L'informatique c'est _complexe_ surtout lorsqu'on est _pas familier_ avec l'environnement. Ça prend quelques temps pour être vraiment à l'aise.
- Elasticsearch et sa stack c'est particulièrement compliqué ! J'ai essayé d'évité les détails inutiles.
- Je peux oublier de préciser certaines choses donc arrêtez moi si ce que je dis n'est pas clair.

---

<!--
## aspects pratiques

- Les supports sont dispo au fur et à mesure sur https://ptych.net/documents
- On aura juste besoin de ssh et un navigateur.
  - vous avez déjà une machine linux ?
  - MobaXTerm pour le ssh -->

## Intro) La stack ELK : Chercher et analyser les logs de façon centralisée

---

## ELK : Elasticsearch, Logstash, Kibana

- _Elasticsearch_ : une base de données pour stocker des **grandes quantités** de **documents texte**
  et **chercher** dedans.

- _Logstash_ : Un **collecteur de logs** et autre données pour remplir Elasticsearch.

- _Kibana_ : Une **interface web** pratique pour **chercher** et **analyser** les données stockées.

<!-- ![](../../images/elastic/elk_schema_01.png) .element: class="mediumimg" -->

![](../../images/elastic/elastic_stack.png)

# La suite Elastic

La suite Elastic, historiquement appelée "ELK", est une combinaison de plusieurs produits de la société Elastic, qui développe des logiciels :

- de base de données distribuée (Elasticsearch)
- de dashboard / d'interface graphique pour explorer des données (Kibana)
- de logging / monitoring (Logstash et Beats)

## Elastic APM

APM est le petit dernier d'Elastic, axé sur le monitoring et le traçage des performances des applications.

<!-- Monitoring ? -->

## L'écosystème Elastic

La société Elastic évolue assez vite et change souvent ses produits. Elle a un business model _open core_ : les fonctionnalités de base sont gratuites et open source, d'autres nécessitent un abonnement (assez cher) appelé X-Pack. Ce dernier est suggéré assez agressivement dans l'interface Kibana.

Le cœur des produits Elastic est composé de Elasticsearch, Kibana (les dashboards et le mode Discover), de Logstash et de Filebeat.

La sécurité est gérée à part, l'alerting aussi. Cela a conduit d'autres personnes à proposer des remplacements open source pour les fonctionnalités payantes : ElastAlert, OpenSearch (anciennement OpenDistro for Elasticsearch) qui est un fork de la suite Elastic, sponsorisé par Amazon.

## <!-- FIXME: lien vers site de stacks monitoring pour dire que c compliké -->

---

### Pourquoi ELK ? Pourquoi c'est dans le cursus

- Gérer une **GRANDE quantité** de logs sur une infrastructure (entre 5 et des centaines de machines)
- Les **explorer** efficacement : un problème difficile vu la quantité (on y reviendra)
- Un brique important pour avoir des applications distribuées avec un déploiement automatisé #Devops

---

### En résumé

- On va voir trois choses durant ces deux jours qui peuvent résumer l'intérêt d'ELK:
  - **Les logs** : pourquoi? comment ? _( quelle est la motivation de ELK )_
  - Découvrir **elasticsearch** et comment **chercher** dans du texte ? _( la partie principale )_
  - Qu'est-ce qu'une **infra distribuée** moderne ? pourquoi ELK c'est du devops ? _( fin )_

---

## Ce qu'on ne va pas faire

- Voir en détail l'installation d'une stack ELK à la main
- Configurer Logstash ou Elastic APM pour pomper des logs d'une vraie infrastructure
- Aborder la sécurité de ELK parce que c'est compliqué (mais c'est important pour faire de vraies installations)

---

## La "hype" Elasticsearch

- Indipensable à de plus en plus d'entreprises qui grossissent : pour augmenter le _contrôle sur les infrastructures_.
- Un outil très _versatile_ et bien fait qui permet de faire de jolis _dashboards d'analyse_ et les gens adorent avoir des jolis dashboards
- Utile pour faire des _big data_ : c'est un peu le moteur de l'informatique actuelle. Tous les nouveaux services fonctionnent grace au traitement de données.

---

## Dashboards

![](../../images/elastic/dashboards.png)

> > >

## I) Les évènements d'un système et le logging

### I.1) Rappel - pourquoi des Logs ?

---

## Logs ?

- Ça veut dire _journaux (système)_ et _bûches_
- Icone originale de Logstash:
  ![](../../images/elastic/logmustach.jpg)

---

## Comprendre ce qui se trame

- Prendre connaissance et analyser les **évènements** d'un système d'un point de vue **opérationnel**.
- Les évènements en informatique sont **invisibles** et presque **instantanés**.
- Les journaux sont la façon la plus simple de contrôler ce qui se passe
  - Des fichiers textes simple avec **une ligne** par **évènement**

---

## Objectif 1: monitoring

- **Suivre** et **anticiper** le fonctionnement d'un système:
  - **suivre** (et réparer) = zut j'ai une erreur : le service nginx a crashé sur mon infra
  - **anticiper** : le disque dur de cette machine sera bientôt plein il faut que je le change / le vide.
  - **enquêter** : par exemple sur les erreurs rares d'une application

<!--
## Monitoring ? (comme Nagios, Zabbix, etc. ?)

- Donc avec la stack elastic on peut faire du monitoring.
- Nagios permet un peu la même chose mais à un niveau plus bas:
  - _Nagios_ : monitoring plutôt _infrastructure_ (réseau, état des OS et système de fichiers)
  - _ELK_ monitoring plutôt _application_ (est-ce que mon application a des bugs ? répond-t-elle correctement aux requêtes ?)
- Aussi ELK est plus **flexible** et permet l'**analyse statistique**. -->

---

## Objectif 2 : conserver les traces de ces évènements pour analyse

- **Archiver** pour **analyser** sur la **longue durée** (6 mois à 1 an ?) avec des **graphiques**.

- Exemples : ces derniers mois est-ce que l'application a correctement répondu aux requêtes de mes utilisateurs ?
  - Compter le nombre de _timeout_ (application est trop lente ?)
  - Compter le nombre de _requêtes_ pour savoir quand sont les pics d'usage dans la journée
  - Connaître la provenance des _requêtes_ et le délai de réponse pour savoir
    - si les serveurs sont correctement disposés _géographiquement_.
    - si les requêtes sont "_routées_" (redirigées) vers le bon serveur.

---

## Infra distribuée

![](../../images/elastic/distrib_app_monitoring.gif)

---

## Exemples de fichier de logs

- chaque application peut avoir un fichier de log
- ou alors on peut les rassembler dans un le même fichier
- les logs sont dans `/var/log`

Vous en connaissez ?

---

## Exemples de fichier de logs

- _auth.log_ : connexion des utilisateurs au système
- _httpd.log_ : connexion au serveur web apache
- _mail.log_ : (aussi bien envoi que réception donc plusieurs applications)
- _nginx/access.log_ : connexion au serveur web nginx

## Exemple d'investigation

Objectif:

- analyser des logs pour retrouver une information
- être attentif-ve au format des logs

Sur le serveur exemple.net, une page web a été supprimée. On veut savoir qui des trois administrateurs Alice, Bob ou Jack a fait cette modification.

<!-- 1. connectez vous en ssh :
   `ssh -p 12222 enqueteur@ptych.net`
   passwd: `enqueteur` -->

1. On se connecte en SSH
2. en utilisant `cat` et `grep` par exemple :

- Pour connaître le titre du site au fil du temps consultez le fichier `/var/log/nginx/access.log`
- on utilise `| grep 403` et `| grep 200` pour savoir quand la page a disparu (en cherchant les codes d'erreur HTTP)
- Pour savoir qui s'est connecté on consulte le fichier `/var/log/auth.log`
- on utilise `| grep` et l'heure pour savoir qui s'est connecté à cette heure ci

{{% expand "`/var/log/nginx/access.log` :" %}}

```log
root@exemple:/home/alice# cat /var/log/nginx/access.log
127.0.0.1 - - [19/Aug/2021:21:53:28 +0000] "GET / HTTP/1.1" 200 612 "-" "curl/7.58.0"
127.0.0.1 - - [19/Aug/2021:21:53:40 +0000] "GET / HTTP/1.1" 200 616 "-" "curl/7.58.0"
127.0.0.1 - - [19/Aug/2021:22:09:16 +0000] "GET / HTTP/1.1" 403 178 "-" "curl/7.58.0"
127.0.0.1 - - [19/Aug/2021:22:09:20 +0000] "GET / HTTP/1.1" 403 178 "-" "curl/7.58.0"
127.0.0.1 - - [19/Aug/2021:22:09:21 +0000] "GET / HTTP/1.1" 403 178 "-" "curl/7.58.0"
```

{{% /expand %}}

{{% expand "`/var/log/auth.log` :" %}}

```log
root@exemple:/home/alice# cat /var/log/auth.log
Aug 19 21:31:13 ubuntu-bionic sudo:  vagrant : TTY=pts/0 ; PWD=/home/vagrant ; USER=root ; COMMAND=/bin/sh -c echo BECOME-SUCCESS-qzjtxhuddaxgkqyivvmjsgpcijwmywth ; /usr/bin/python3 /home/vagrant/.ansible/tmp/ansible-tmp-1629408672.571441-2529-99170342043575/AnsiballZ_systemd.py
Aug 19 21:31:13 ubuntu-bionic sudo: pam_unix(sudo:session): session opened for user root by vagrant(uid=0)
Aug 19 21:31:15 ubuntu-bionic sudo: pam_unix(sudo:session): session closed for user root
Aug 19 21:32:15 ubuntu-bionic sshd[2176]: Received disconnect from 192.168.2.1 port 49608:11: disconnected by user
Aug 19 21:32:15 ubuntu-bionic sshd[2176]: Disconnected from user vagrant 192.168.2.1 port 49608
Aug 19 21:32:15 ubuntu-bionic sshd[2099]: pam_unix(sshd:session): session closed for user vagrant
Aug 19 21:32:15 ubuntu-bionic systemd-logind[847]: Removed session 5.
Aug 19 21:52:19 ubuntu-bionic sshd[5610]: Accepted publickey for bob from 10.0.2.2 port 49854 ssh2: RSA SHA256:1M4RzhMyWuFS/86uPY/ce2prh/dVTHW7iD2RhpquOZA
Aug 19 21:52:19 ubuntu-bionic sshd[5610]: pam_unix(sshd:session): session opened for user bob by (uid=0)
Aug 19 21:52:19 ubuntu-bionic systemd: pam_unix(systemd-user:session): session opened for user bob by (uid=0)
Aug 19 21:52:19 ubuntu-bionic systemd-logind[847]: New session 7 of user bob.
Aug 19 21:52:25 ubuntu-bionic sudo:  bob : TTY=pts/0 ; PWD=/home/bob ; USER=root ; COMMAND=/bin/su
Aug 19 21:52:25 ubuntu-bionic sudo: pam_unix(sudo:session): session opened for user root by bob(uid=0)
Aug 19 21:52:25 ubuntu-bionic su[5708]: Successful su for root by root
Aug 19 21:52:25 ubuntu-bionic su[5708]: + /dev/pts/0 root:root
Aug 19 21:52:25 ubuntu-bionic su[5708]: pam_unix(su:session): session opened for user root by bob(uid=0)
Aug 19 21:52:25 ubuntu-bionic su[5708]: pam_systemd(su:session): Cannot create session: Already running in a session
Aug 19 22:12:45 ubuntu-bionic su[5708]: pam_unix(su:session): session closed for user root
Aug 19 22:12:45 ubuntu-bionic sudo: pam_unix(sudo:session): session closed for user root
Aug 19 22:12:48 ubuntu-bionic sshd[5691]: Received disconnect from 10.0.2.2 port 49854:11: disconnected by user
Aug 19 22:12:48 ubuntu-bionic sshd[5691]: Disconnected from user bob 10.0.2.2 port 49854
Aug 19 22:12:48 ubuntu-bionic sshd[5610]: pam_unix(sshd:session): session closed for user bob
Aug 19 22:12:48 ubuntu-bionic systemd-logind[847]: Removed session 7.
```

<!--
root@kibana-node:/home/vagrant# cat /var/log/nginx/access.log
127.0.0.1 - - [19/Aug/2021:21:53:28 +0000] "GET / HTTP/1.1" 200 612 "-" "curl/7.58.0"
127.0.0.1 - - [19/Aug/2021:21:53:40 +0000] "GET / HTTP/1.1" 200 616 "-" "curl/7.58.0"
 -->

---

# Bilan

- Explorer les logs "à la main" c'est pas toujours très pratique.
- Chercher des évènements datés en filtrant n'est pas très adapté.
- Résoudre un problème nécessite de les interpréter.
- Pour cela on doit chercher et croiser des informations diverses avec un but.

## I.2) Le problème avec les logs d'une infrastructure

---

## Décentralisé

- Une infrastructure c'est beaucoup de machines: les logs sont décentralisés à plein d'endroits.

  - Au delà de 3 machines pas question de se logguer sur chacune pour enquêter.

- On veut un endroit centralisé pour tout ranger.

---

## La quantité

- Des millions et des millions de ligne de journaux, ce qui représente potentiellement des _teraoctets_ de données.
- Cette quantité faramineuse de données texte il faut pouvoir:
  - la **stocker** et la **classer**, l'uniformiser (les logs ont pleins de format différents)
  - **chercher** dedans par date efficacement.
  - **croiser les données**

---

## La stack ELK

![](../../images/elastic/elk_schema_01.png)

---

## La stack ELK

- Les **Beats** pour lire les données depuis plusieurs machines. Les principales sont :

  - **FileBeat** : lire des fichiers de log pour les envoyer à **Logstash** ou directement à **Elasticsearch**
  - **MetricBeat** : récupérer des données d'usage, du CPU, de la mémoire, du nombre de process NGINX
  <!-- - etc -->

- Logstash : récupère les log pour les traiter avant de les envoyer dans Elasticsearch
  - formater des logs
  - transformer les données avant de les mettre dans Elasticsearch

---

### Quelques forces d'Elasticsearch et ELK

- **Facile à agrandir**: (_elastic_) c'est une application **automatiquement distribuée**.
  - Ajout d'un nouveau noeud, réindexation et hop.
- **Presque en temps réel** : Les évènements sont disponibles pour la recherche presque instantanément
- **Recherche très rapide** : sur des gros volumes

## Exercice I.2)

Calculons la quantité de log que produisent 12 instances d'une application pendant un mois
Chaque instance = Un serveur web, une application python + une base de données pour toutes les instances

- Chercher la taille d'une ligne de log ?
- Combien pèse un caractère ?
- Comment mesurer la quantité de lignes produites par une application ?
  - on va retenir 200 lignes par minute en moyenne pour le serveur web
  - 120 pour l'application python
  - 60 pour la DB -> c'est très variable
- Faire le calcul
- Conclusions...

---

<!--
 FIXME: syntaxe de l'API et de JSON rapidement et schémas !!!
 -->
