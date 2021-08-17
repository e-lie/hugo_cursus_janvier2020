---
title: ELK cours 1
theme: moon
separator: "\n>>>\n"
verticalSeparator: "\n---\n"
revealOptions:
    transition: 'none'
---

# Elastic stack et le logging (partie 1)
*Apprendre à nager dans un océan d'évènements et de texte!*

---

# Plan de la formation

- *Intro)*
- *I)* **Les évènement d'un système et le logging**
- *II)* **Elasticsearch, stocker une masse de données texte**
- *III)* **Installer un cluster virtuel Elasticsearch et Kibana**
- *IV)* **Elasticsearch et Kibana pour la recherche et l'analyse**

- Ouvrage recommandé : *PacktPub - Learning Elastic Stack 6*

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

- L'informatique c'est *complexe* surtout lorsqu'on est *pas familier* avec l'environnement. Ça prend quelques temps pour être vraiment à l'aise.
- Elasticsearch et sa stack c'est particulièrement compliqué ! J'ai essayé d'évité les détails inutiles et je serais pas trop méchant dans le QCM final.
- Je peux oublier de préciser certaines choses donc arrêtez moi si ce que je dis n'est pas clair.

---

## aspects pratiques

- Les supports sont dispo au fur et à mesure sur https://ptych.net/documents
- On aura juste besoin de ssh et un navigateur.
    - vous avez déjà une machine linux ?
    - MobaXTerm pour le ssh

>>>



## Intro) La stack ELK : Chercher et analyser les logs de façon centralisée

---

## ELK : Elasticsearch, Logstash, Kibana

- *Elasticsearch* : une base de données pour stocker des **grandes quantités** de **documents texte**
et **chercher** dedans.

- *Logstash* : Un **collecteur de logs** et autre données pour remplir Elasticsearch.

- *Kibana* : Une **interface web** pratique pour **chercher** et **analyser** les données stockées.

![](img/elk_schema_01.png) <!-- .element: class="mediumimg" -->

---

## Pourquoi ELK ? Pourquoi c'est dans le cursus

- Gérer une **GRANDE quantité** de logs sur une infrastructure (entre 5 et des centaines de machines)
- Les **explorer** efficacement : un problème difficile vu la quantité (on y reviendra)
- Un brique important pour avoir des applications distribuées avec un déploiement automatisé #Devops

---

## En résumé
  
- On va voir trois choses durant ces deux jours qui peuvent résumer l'intérêt d'ELK:
    - **Les logs** : pourquoi? comment ? *( quelle est la motivation de ELK )*
    - Découvrir **elasticsearch** et comment **chercher** dans du texte ? *( la partie principale )*
    - Qu'est-ce qu'une **infra distribuée** moderne ? pourquoi ELK c'est du devops ? *( fin )*

---

## Ce qu'on va pas faire

- Installer la stack ELK à la main, et en particulier :
    - Configurer Logstash pour pomper des logs sur une vraie infrastructure
    - Aborder la sécurité de ELK parce que c'est compliqué (mais c'est important pour faire de vraies installations)

---

## La "hype" elasticsearch

 - Indipensable à de plus en plus d'entreprises qui grossissent : pour augmenter le *contrôle sur les infrastructures*.
 - Un outil très *versatile* et bien fait qui permet de faire de jolis *dashboard d'analyse* et c'est à la mode d'avoir des jolis dashboards
 - Utile pour faire des *big data* : c'est un peu le moteur de l'informatique actuelles. Tous les nouveaus services fonctionnent grace au traitement de données.

---

## Dashboards

![](img/dashboards.png)

>>>







## I) Les évènements d'un système et le logging

### I.1) Rappel - pourquoi des Logs ?

---

## Logs ?

- Ça veut dire *journaux (système)* et *bûches*
- Icone originale de Logstash: 
    - ![](img/logmustach.jpg) <!-- .element: class="tinyimg" -->


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
    - *Nagios* monitoring plutôt *infrastructure* (réseau, état des OS et système de fichiers)
    - *ELK* monitoring plutôt *application* (est-ce que mon application a des bugs ? répond-t-elle correctement aux requêtes ?)
- Aussi ELK est plus **flexible** et permet l'**analyse statistique**.

---

## Objectif 2: Conserver les traces de ces évènements pour analyse

- **Archiver** pour **analyser** sur la **longue durée** (6 mois à 1 an ?) avec des **graphiques**.

- Exemples : ces derniers mois est-ce que l'application a correctement répondu aux requêtes de mes utilisateurs ?
    - Compter le nombre de *timeout* (application est trop lente ?)
    - Compter le nombre de *requêtes* pour savoir quand sont les pics d'usage dans la journée
    - Connaître la provenance des *requêtes* et le délai de réponse pour savoir
       - si les serveurs sont correctement disposés *géographiquement*.
       - si les requêtes sont "*routées*" (redirigées) vers le bon serveur.

---

## Infra distribuée

![](img/distrib_app_monitoring.gif)

---

## Exemples de fichier de logs

- chaque application peut avoir un fichier de log
- ou alors on peut les rassembler dans un le même fichier
- les logs sont dans `/var/log`

Vous en connaissez ?

---

## Exemples de fichier de logs

- *auth.log* : connexion des utilisateurs au système
- *httpd.log* : connexion au serveur web apache
- *mail.log* : (aussi bien envoi que réception donc plusieurs applications)
- *nginx/access.log* : connexion au serveur web nginx

>>>

## I.1) Exercice

Objectif:
- analyser des logs pour retrouver une information
- être attentif au format des logs

Sur le serveur ptych.net le titre d'une page web a été changé. On veut savoir qui des trois administrateurs Alice, Bob ou Jack a fait cette modification.

1. connectez vous en ssh :
```ssh -p 12222 enqueteur@ptych.net```
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

>>>


## I.2) Le problème avec les logs d'une infrastructure

---

## Décentralisé

- Une infrastructure c'est beaucoup de machines: les logs sont décentralisés à plein d'endroits.
    - Au delà de 3 machines pas question de se logguer sur chacune pour enquêter.

- On veut un endroit centralisé pour tout ranger.

---

## La quantité

- Des millions et des millions de ligne de journaux, ce qui représente potentiellement des *teraoctets* de données. 
- Cette quantité faramineuse de données texte il faut pouvoir:
    - la **stocker** et la **classer**, l'uniformiser (les logs ont pleins de format différents)
    - **chercher** dedans  par date efficacement.
    - **croiser les données**

---

## La stack ELK

![](img/elk_schema_01.png)

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

 - **Facile à agrandir**: (*elastic*) c'est une application **automatiquement distribuée**.
    - Ajout d'un nouveau noeud, réindexation et hop.
 - **Presque en temps réels** : Les évènements sont disponibles pour la recherche presque instantanément
 - **Recherche très rapide** : sur des gros volumes

>>>

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


