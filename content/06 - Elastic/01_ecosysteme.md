---
title: "1 - Découverte de l'écosystème Elastic"
draft: false
weight: 3010
---

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

# La suite Elastic

La suite Elastic, historiquement appelée "ELK", est une combinaison de plusieurs produits de la société Elastic, qui développe des logiciels :

- de base de données distribuée (Elasticsearch)
- de dashboard / d'interface graphique pour explorer des données (Kibana)
- de logging / monitoring (Logstash et Beats)

## Elasticsearch

Elasticsearch est à la fois :

- une base de données distribuée (plusieurs instances de la base de données sont connectées entre elles de manière à assurer de la **redondance** si un des nœuds en vient à avoir des problèmes)
- un moteur de recherche puissant, basé sur un autre logiciel appelé Apache Lucene

Elle fait partie des bases de données de type NoSQL :

<!-- FIXME: NoSQL -->
<!-- FIXME: vocab nœuds et cluster -->
<!-- FIXME: Lucene et KQL -->

## Kibana

Kibana est un outil très complet de visualisation et d'administration des données dans une base de données Elasticsearch.
Elle est toujours connectée à un cluster (un ou plusieurs nœuds) Elasticsearch.

<!-- FIXME: différentes parties Kibana -->
<!-- FIXME: KQL -->

## Logstash

Logstash est un couteau suisse puissant de récupération, de transformation et d'envoi de logs.
Contrairement à Kibana et Elasticsearch, Logstash peut être utilisé de façon **indépendante** à Elasticsearch ou à Kibana.

<!-- FIXME: Exemple filtres, principe des inputs/outputs -->

## Beats

Beats est un ajout récent à la suite Elastic. C'est un programme designé pour être extrêmement léger et n'avoir qu'une seule mission : envoyer des logs à un autre programme qui s'assurera du traitement de ceux-ci.

Il est un peu difficile de comprendre la différence fondamentale entre **Beats** et **Logstash** au début, on peut retenir :

- que **Beats** a beaucoup moins de fonctionnalités que Logstash,
- et qu'il n'a que quelques missions simples à remplir, là où Logstash est un outil très complet pour récupérer, transformer et renvoyer des logs.

<!-- FIXME: Filebeat -->

## Elastic APM

APM est le petit dernier d'Elastic.

<!-- Monitoring ? -->

## L'écosystème Elastic

<!-- FIXME: ecosysteme, open core premium pas premium, truc de security truc, ElastAlert -->

## <!-- FIXME: lien vers site de stacks monitoring pour dire que c compliké -->

![](../../images/elastic/elastic_stack.png)

---

---

title: ELK cours 1
theme: moon
separator: "\n>>>\n"
verticalSeparator: "\n---\n"
revealOptions:
transition: 'none'

---

# Elastic stack et le logging (partie 1)

_Apprendre à nager dans un océan d'évènements et de texte!_

---

# Plan de la formation

- _Intro)_
- _I)_ **Les évènement d'un système et le logging**
- _II)_ **Elasticsearch, stocker une masse de données texte**
- _III)_ **Installer un cluster virtuel Elasticsearch et Kibana**
- _IV)_ **Elasticsearch et Kibana pour la recherche et l'analyse**

- Ouvrage recommandé : _PacktPub - Learning Elastic Stack 6_

---

# A propos de moi

- Développeur Python et BDD (opérateur télécom Sewan)
- Doctorant en philosophie politique et de la technique:
- Formateur: Python, Ansible, Elasticsearch

---

## Racontez moi les épisodes précédents

Ça va globalement ce cursus super dense ?

- Linux ? on va reparler de **tail**, **grep** et des **logs**
- HTTP ? on va utiliser une **API REST** (basé sur **HTTP**)
- JSON ? on va utiliser des documents formatés en **JSON**
- Les **bases de données** ?
- **Machines virtuelles** ? on va parler un peu à la fin de cluster dans le cloud
  (c'est compliqué un cluster elasticsearch mais on va surtout voir ça théoriquement)
- Nagios ?

---

## Rappel

- L'informatique c'est _complexe_ surtout lorsqu'on est _pas familier_ avec l'environnement. Ça prend quelques temps pour être vraiment à l'aise.
- Elasticsearch et sa stack c'est particulièrement compliqué ! J'ai essayé d'évité les détails inutiles et je serais pas trop méchant dans le QCM final.
- Je peux oublier de préciser certaines choses donc arrêtez moi si ce que je dis n'est pas clair.

---

## aspects pratiques

- Les supports sont dispo au fur et à mesure sur https://ptych.net/documents
- On aura juste besoin de ssh et un navigateur.
  - vous avez déjà une machine linux ?
  - MobaXTerm pour le ssh

> > >

## Intro) La stack ELK : Chercher et analyser les logs de façon centralisée

---

## ELK : Elasticsearch, Logstash, Kibana

- _Elasticsearch_ : une base de données pour stocker des **grandes quantités** de **documents texte**
  et **chercher** dedans.

- _Logstash_ : Un **collecteur de logs** et autre données pour remplir Elasticsearch.

- _Kibana_ : Une **interface web** pratique pour **chercher** et **analyser** les données stockées.

![](../../images/elastic/elk_schema_01.png) <!-- .element: class="mediumimg" -->

---

## Pourquoi ELK ? Pourquoi c'est dans le cursus

- Gérer une **GRANDE quantité** de logs sur une infrastructure (entre 5 et des centaines de machines)
- Les **explorer** efficacement : un problème difficile vu la quantité (on y reviendra)
- Un brique important pour avoir des applications distribuées avec un déploiement automatisé #Devops

---

## En résumé

- On va voir trois choses durant ces deux jours qui peuvent résumer l'intérêt d'ELK:
  - **Les logs** : pourquoi? comment ? _( quelle est la motivation de ELK )_
  - Découvrir **elasticsearch** et comment **chercher** dans du texte ? _( la partie principale )_
  - Qu'est-ce qu'une **infra distribuée** moderne ? pourquoi ELK c'est du devops ? _( fin )_

---

## Ce qu'on va pas faire

- Installer la stack ELK à la main, et en particulier :
  - Configurer Logstash pour pomper des logs sur une vraie infrastructure
  - Aborder la sécurité de ELK parce que c'est compliqué (mais c'est important pour faire de vraies installations)

---

## La "hype" elasticsearch

- Indipensable à de plus en plus d'entreprises qui grossissent : pour augmenter le _contrôle sur les infrastructures_.
- Un outil très _versatile_ et bien fait qui permet de faire de jolis _dashboard d'analyse_ et c'est à la mode d'avoir des jolis dashboards
- Utile pour faire des _big data_ : c'est un peu le moteur de l'informatique actuelles. Tous les nouveaus services fonctionnent grace au traitement de données.

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
  - ![](../../images/elastic/logmustach.jpg) <!-- .element: class="tinyimg" -->

---

## Comprendre ce qui se trame

- Prendre connaissance et analyser les **évènements** d'un système d'un point de vue **opérationnel**.
- Les évènements en informatique sont **invisibles** et presque **instantanés**.
- Les journaux sont la façon la plus simple de contrôler ce qui se passe
  - Des fichiers textes simple avec **une ligne** par **évènement**

---

## Objectifs 1: monitoring

- **Suivre** et **anticiper** le fonctionnement d'un système:
  - **suivre** (et réparer) = zut j'ai une erreur : le service nginx à crashé sur mon infra
  - **anticiper** : le disque dur de cette machine sera bientôt plein il faut que je le change /le vide.
  - **enquêter** : par exemple sur les erreurs rares d'une application

---

## Monitoring comme Nagios ?

- Donc avec la stack elastic on peut faire du monitoring.
- Nagios permet un peu la même chose mais à un niveau plus bas:
  - _Nagios_ monitoring plutôt _infrastructure_ (réseau, état des OS et système de fichiers)
  - _ELK_ monitoring plutôt _application_ (est-ce que mon application a des bugs ? répond-t-elle correctement aux requêtes ?)
- Aussi ELK est plus **flexible** et permet l'**analyse statistique**.

---

## Objectif 2: Conserver les traces de ces évènements pour analyse

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

> > >

## I.1) Exercice

Objectif:

- analyser des logs pour retrouver une information
- être attentif au format des logs

Sur le serveur ptych.net le titre d'une page web a été changé. On veut savoir qui des trois administrateurs Alice, Bob ou Jack a fait cette modification.

1. connectez vous en ssh :
   `ssh -p 12222 enqueteur@ptych.net`
   passwd: `enqueteur`

- en utilisant cat et grep par exemple:
  - Pour savoir qui s'est connecté consultez le fichier /var/log/auth.log
  - Pour connaître le titre du site au fil du temps consultez le fichier /var/log/nginx/access.log
- utilisez `| grep MyWebSite` pour savoir quand le titre a été modifié
- utilisez `| grep` et l'heure pour savoir qui s'est connecté à cette heure ci

---

# Bilan

- Explorer les logs "a la main" c'est pas toujours très pratique.
- Chercher des évènements datés en filtrant (+logrotate) pas très adapté.
- Résoudre un problème nécessite de les interpréter.
- Pour cela on doit chercher et croiser des informations diverses avec un but.

> > >

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

- Les **Beats** pour lire les données depuis plusieurs machines

  - **FileBeat** : lire des fichiers de log pour les envoyer à **Logstash**
  - **MetricBeat** : récupérer des donneés d'usage, du CPU, de la mémoire, du nombre de process NGINX
  - etc

- Logstash : récupère les log pour les traiter avant de les envoyer dans Elasticsearch
  - formatter des logs
  - transformer les données avantde les mettres dans

---

### Quelques forces d'Elasticsearch et ELK

- **Facile à agrandir**: (_elastic_) c'est une application **automatiquement distribuée**.
  - Ajout d'un nouveau noeud, réindexation et hop.
- **Presque en temps réels** : Les évènements sont disponibles pour la recherche presque instantanément
- **Recherche très rapide** : sur des gros volumes

> > >

## Exercice I.2)

Calculons la quantité de log que produisent 12 instances d'une application pendant un mois
Chaque instance = Un serveur web, une application python + une base de données pour toutes les instances

- Chercher la taille d'un ligne de log ?
- Combien pèse un caractère ?
- Comment mesurer la quantité de lignes produites par une application ?
  - on va retenir 200 lignes par minute en moyenne pour le serveur web
  - 120 pour l'application python
  - 60 pour la DB -> c'est très variable
- Faire le calcul
- Conclusions...

---

---

title: Elastic Stack et le logging
theme: moon
separator: "\n>>>\n"
verticalSeparator: "\n---\n"
revealOptions:
transition: 'none'

---

# Plan

## 0) Petit bilan

0.1 Où en êtes-vous ? Ça va globalement ce cursus super dense ?

- Linux ? on va reparler de tail, grep et des logs
- HTTP ? on va utiliser une API REST (basé sur HTTP)
- JSON ? on va utiliser des documents formatés en JSON
- Machines virtuelles ? on va parler un peu à la fin de cluster dans le cloud
  (compliqué mais on va surtout voir ça théoriquement)

  0.2 Disclaimer

---

## Intro) La stack elastic : une base de données pour rechercher dans des logs

- gérer une GRANDE quantité de Logs : sur une infrastructure (entre 5 et 1000... machines)
- les explorer efficacement : un problème difficile vu la quantité
- En résumé : on va voir trois choses durant ces deux jours qui peuvent résumer ELk
  - les logs pourquoi? comment ?
  - Découvrir elasticsearch et comment chercher dans du texte ?
  - qu'est-ce qu'une infra distribuée moderne ? pourquoi ELK c'est du devops ?
- ce qu'on va pas faire : installer la stack ELK à la main
- La hype elasticsearch.

> > >

## I) Les évènements d'un système et le logging

### I.1) Rappel - pourquoi des Logs ?

- comprendre ce qui se trame, prendre connaissance et analyser les évènements d'un système
  -> suivre son comportement (anticiper -> pas que lorsque problèmes)
- afficher les erreurs et les risques potentiels (warning) -> voir les problèmes
- archiver pour consulter a posteriori : analyser sur la longue durée

### exercice:

- analyse de situation (qui a changé le titre ?) et d'usage des outils unix (révision)

---

### I.2) Le problème avec les logs d'une infrastructure

    - quantité

### exercice de calcul : 10 machines, 5 logiciels qui produisent entre 5 et 100 évènements par minute

    -> taille d'une ligne de log ? combien d'octets ?

    - Distribué

    - scalability (passage à l'échelle)
      -facilité à rajouter une machine

Solution : Elasticsearch + Logstash + Kibana
récupérer les logs distribués: Logstash

> > >

## II) Elasticsearch, une base de donnée pour la recherche de texte

### II.0) Kibana tête la première

### II.1) Structure de Elasticsearch

schémas !!!

syntaxe de l'API et de JSON rapidement

### CRUD exercices - Partie 1

### II.2) API REST ?

- HTTP, head & body
- REST : GET, POST, PUT, DELETE, HEAD endpoint

> > >

## III) Recherche avec Elasticsearch et Kibana

    - Deux types de recherche : exacte et fulltext

### Exercices

### II.2) Comparaison avec les base de données classiques

    - le schéma est facultatif et moins important
    - fait pour chercher plutôt que supporter le modèle des données.

Rappel kibana est une interface pour elastic (soit on attaque direct elastic soit on utilise les trucs pratiques de Kibana)

_utiliser les données d'aviation_
expliquer l'interface
expliquer les graphiques

idées d'exercices:

trouver les correspondances possible pour aller d'une ville A à une ville B entre telle et telle heure ?

## Exercice:

    requête pour analyser une erreur dans le code
    graphique sur le volume de connexion au cours de la journée
    corréler des évènements comme l'exemple du début
    ajouter des exemples de plus en plus compliqués

> > >

## III) Life inside a cluster

    - scale In = vertical , scale Out = horizontal
    - shard
    - dimensionner un cluster
    - haute disponibilité
      - endpoint switching déclarer plusieurs
      - fallback automatique

### Exercice:

    - calculer le nombre de noeuds pour que ça marche encore
    - répartir les noeuds sur une infrastructure
    # - configurer et constater qu'on a le bon nombre de noeuds
    - vérifier que quand il y en a un qui tombe ça marche toujours

> > >

## Conclusion) Elasticsearch et le Devops

Qu'est ce que le devops ?
Analyse et automatisation
Elk dans le devops !

> > >

# Logistique

Note: comment ça va se Passer ? #TODO

- feuilles d'exercices
- évaluation
- pauses
- installer la machine virt

# Découvrir Elastic stack et le logging d'infrastructure

_Apprendre à nager dans un océan d'évènements et de texte!_

> > >

# Logistique

Note: comment ça va se Passer ? #TODO

- feuilles d'exercices
- évaluation
- pauses
- installer la machine virtuelle
- TP

---

## Points en vracs
