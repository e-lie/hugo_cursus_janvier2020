---
title: Préparation de la VM de travail
weight: 2
draft: no
---


Cette formation est basée sur une machine de travail **Ubuntu Bionic (18.04)** (ou xubuntu,kubuntu)

## Formation à distance - accéder à une machine distante grâce à VNC


### Sur windows et linux

- Installez tigerVNC et lancez `vncviewer` ou `tigervnc`

### Sur MacOS

- Lancez simplement l'utilitaire "Partage d'écran"

### Se connecter

La formation se déroule en accédant simultanément à 2 machines distantes :

- La machine du formateur, ou sont réalisées les démonstrations et la présentation du cours
- Votre machine stagiaire individuelle.

1. récupérez l'adresse ip ou le non de domaine de ces deux machines fournis pour la formation (la connexion s'effectue sur le port 5901 par exemple `formateur.dopl.uk:5901`)
2. connectez vous à la machine formateur en mode "passif" ou "viewer" à l'aide du mot de passe fournis
3. connectez vous à la machine stagiaire en mode controle (normal) à l'aide du mot de passe adéquat
4. Indiquez au formateur que vous avez bien accès aux deux machines.


## Formation présentiel - importer une machine Linux virtualbox

- Récupérez  une machine virtualbox ubuntu ou xubuntu (18.04): sur un disque externe, dans le partage réseau (au cas ou elle n'est pas fournie par exemple pour une formation à distance vous pouvez en récupérer sur le net par exemple [ici](https://www.osboxes.org/xubuntu/) ou en [installant depuis l'image ISO officielle](https://www.numetopia.fr/comment-installer-ubuntu-dans-virtualbox/))
  
- Configurez la avec 8Go de RAM et 3 processeurs
  
- Démarrez la machine
  
- Faites le mises à jours (`sudo apt update` et `sudo apt upgrade`)

## Installer quelques logiciels

- Installez VSCode avec le gestionnaire de paquet snap : `snap install code --classic`
  
- En ligne de commande (`apt`) installez `git`, `htop`, `ncdu`

## Explorer Ubuntu Bionic (18.04) : Démo

## Explorer l'éditeur VSCode : Démo

