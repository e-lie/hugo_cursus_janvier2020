---
title: Préparation de la VM de travail
weight: 2
draft: yes
---


Cette formation est basée sur une machine de travail **Ubuntu Bionic (18.04)** (ou xubuntu,kubuntu)

## Formation à distance - accéder à une machine distante grâce à VNC


### Instructions Windows

- Installer mobaxterm
- Se connecter avec mobaxterm :
  - session VNC:
    - Hôte `127.0.0.1:5901`
    - Dans `network settings` utiliser `proxy SSH` avec `stagiaire@<votrenom>.dopl.uk`
    - La connection nécessite de rentrer deux fois le mdp `devops101`

### Instruction MacOS

- Lancez dans un terminal `ssh -L 5901:localhost:5901 stagiaire@<votrenom>.dopl.uk &`. Le mot de passe ssh est `devops101`
- Garder le terminal ouvert puis lancez `Partage d'écran.app` et connection sur `localhost:5901` avec le mdp `devops101` également.

### Instruction Linux

- Installez `Vinagre`
- Se connecter avec vinagre:
  - session VNC:
    - Hôte `127.0.0.1:5901`
    - Dans utiliser l'option `tunnel SSH` avec `stagiaire@<votrenom>.dopl.uk`
    - La connection nécessite de rentrer deux fois le mdp `devops101`


### Une fois sur la session linux distante

 - Conseil: mettez VNC en plein écran (nous ferons tout sur la machine distante)
 - Ouvrez le raccourci "machine formateur" sur le bureau pour suivre le déroulement en mode démonstration des cours et TPs (VNCeption).

## Formation présentiel - importer une machine Linux virtualbox

- Récupérez  une machine virtualbox ubuntu ou xubuntu (18.04): sur un disque externe, dans le partage réseau (au cas ou elle n'est pas fournie par exemple pour une formation à distance vous pouvez en récupérer sur le net par exemple [ici](https://www.osboxes.org/xubuntu/) ou en [installant depuis l'image ISO officielle](https://www.numetopia.fr/comment-installer-ubuntu-dans-virtualbox/))
  
- Configurez la avec 4Go de RAM minimum (8Go si possible) et et 2 à 4 processeurs.
  
- Démarrez la machine
  
- Faites les mises à jours (`sudo apt update` et `sudo apt upgrade`)

### Installer quelques logiciels (dans le cas d'une machine vierge)

- Installez VSCode avec le gestionnaire de paquet snap : `snap install code --classic`
  
- En ligne de commande (`apt`) installez `lxd`, `git`, `htop`, `ncdu`



