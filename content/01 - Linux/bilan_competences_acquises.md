---
title: "Bilan compétences Linux"
weight: 120
# pre: "<i class='fab fa-git'></i> - "
draft: false
---

### Utilisation du terminal

- Savoir taper une commande, identifier les morceaux importants : nom de commande, options, arguments
- Savoir identifier dans quel répertoire on se trouve actuellement, avec quel user, et sur quelle machine
- Savoir utiliser l'auto-complétion et l'historique
- Savoir obtenir de l'aide sur une commande
- Savoir relire attentivement sa commande pour vérifier les typos
- Savoir lire attentivement ce que la commande renvoie pour valider qu'elle a fonctionné ... ou bien qu'il y a une erreur à debugger

### Fichiers

- Comprendre la notion de chemin relatif et de chemin absolu pour désigner un fichier
- Où sont stockés les fichiers de configuration
- Où sont stockés les programmes
- Où sont stockés les logs
- Où sont les répertoires utilisateurs
- Savoir afficher ou éditer un fichier depuis la ligne de commande

### Users

- Savoir lister les users et groupes
- Savoir créer un user
- Savoir ajouter un user dans un groupe et listers les groupes dans lequel un user est
- Savoir lire les permissions r/w/x d'un fichier, et les modifier
- Savoir identifier le propriétaire + groupe propriétaire d'un fichier, et le modifier
- Savoir changer son mot de passe
- Savoir executer des taches d'administration avec sudo
- Savoir changer de compte utilisateur avec su

### Processus

- Voir les processus qui tournent actuellement
- Savoir identifier le proprietaire d'un process et son PID
- Comprendre les relations de parentés entre process
- Savoir tuer un process
- Comprendre qu'il existe un process "originel" nommé "init"

### Installation de Linux

- Avoir compris la procédure d'installation de linux : 
    - téléchargement d'une ISO, 
    - boot sur l'ISO, 
    - configuration des partitions et points de montage
- Comprendre la notion de point de montage d'une partition
- Savoir monter manuellement un périphérique de stockage externe

### Gestionnaire de paquet

- Savoir installer un paquet
- Savoir mettre à jour le système
- Savoir ce qu'est un dépot de logiciel
- Savoir ajouter un dépot de logiciel (et clef de signature associée)

### Réseau (IP)

- Comprendre que l'acheminement des paquets sur le réseau se fait par des routeurs qui discutent entre eux pour optimiser les trajets
- Comprendre la notion de réseau local
- Savoir qu'il y a l'IPv4 ... mais aussi l'IPv6 !
- Savoir identifier son IPv4 locale
- Savoir identifier son IPv4 globale
- Savoir pinger une autre machine

### Réseau (TCP)

- Comprendre que TCP est une couche réseau qui permet d'introduire de la fiabilité dans les communications à l'aide d'accusé de réceptions
- Comprendre qu'il est nécessaire d'introduire la notion de port (en plus de l'IP) pour spécifier entre quelles programmes se fait une communication TCP
- Savoir lister les process qui écoutent sur un port
- Savoir ce qu'est un firewall et de quoi il protège

### Réseau (DNS)

- Savoir ce qu'est un nom de domaine
- Savoir résoudre un nom de domaine
- Savoir ce qu'est un résolveur DNS, et où il est configuré sur le système

### Réseau (web)

- Comprendre ce qu'il se passe au niveau réseau lorsqu'on visite une page web (résolution DNS, établissement d'une communication TCP, envoi d'une requête GET /)
- Savoir télécharger des pages web ou fichiers sur le web avec wget ou curl

### Notions de sécurité

- Connaître les bonnes pratiques de base : tenir son serveur raisonnablement à jour, utiliser des mots (ou phrases) de passes raisonnablement forts
- Comprendre pourquoi il ne faut pas faire `chmod 777`, ou `chmod +r`, notamment sur des fichiers contenant des informations privées (données personnelles) ou critique (mot de passe de base de donnée)
- Comprendre qu'un serveur sur internet est sujet à des attaques automatiques, et qu'il est possible de mettre en place des contre-mesures
- Comprendre le principe de la cryptographie asymétrique : notion de clef publique, clef privée
- (idéalement : comprendre la notion d'authenticité et de signature cryptographique)

### Infrastructure

- Savoir qu'il est possible d'acheter des serveurs (VPS) en ligne (infrastructure as a service)

### SSH

- Savoir se connecter à une machine SSH, avec password ou clef
- Savoir générer une clef publique / privée, et comment donner sa clef publique à un collègue
- (idéalement : avoir compris l'intérêt d'utiliser une clef plutôt qu'un mot de passe)
- Savoir jongler mentalement entre plusiers terminaux, possiblement connectés sur des machines différentes

### Services

- Savoir ce qu'est un service (au sens de l'administration système)
- Savoir lancer / arrêter / redémarrer un service
- Savoir afficher l'état d'un service
- Savoir trouver et lire les logs d'un service
- Comprendre que le déploiement d'une application implique généralement d'installer et configurer un écosystème de services qui travaillent ensemble : serveur web, "l'app", et le serveur de base de donnée

### Commandes "avancées"

- Comprendre la notion de code de retour, d'entrée standard, sortie standard et erreur standard
- Savoir rediriger les sorties des commandes
- Savoir enchainer des commandes (;, &&, ||, |)
- Savoir utiliser grep, awk, cut, sort, uniq, wc, ... pour filtrer ou faire des calculs sur les sorties des commandes

### Scripting bash

- Savoir modifier et afficher une variable
- Avoir compris le rôle du fichier `~/.bashrc`
- Avoir compris comment le shell sais où trouver les programmes correspondants aux commandes tapées (variable `PATH`)
- Savoir écrire un script et le lancer
- Savoir utiliser les arguments fourni dans un script
- Savoir mettre la sortie d'une commande dans une variable
- Savoir comment écrire un bloc de condition
- Savoir écrire des fonctions, des boucles
- Savoir ajouter une tâche planifiée sur le système (cron)
- Savoir ne pas partir en courant à la vue d'une regex
