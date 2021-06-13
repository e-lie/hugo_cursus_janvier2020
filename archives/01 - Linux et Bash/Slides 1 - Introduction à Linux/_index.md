---
title: "Slides 1 - Introduction à Linux"
weight: 1
outputs: ["reveal"]
---

# Introduction à Linux

<!-- _Become a Command Line padawan in one day!_ -->

---

# Hello, world!

<!--

# Plan de la formation

**Jour 1 ?**

- 0, 1 - Historique, introduction, rappels
- 2, 3 - Prise en main du terminal et de la ligne de commande
- 4 - Le système de fichier

**Jour 2 ?**

- 5, 6 - Utilisateurs, groupes et permissions
- 7 -  Les processus

**Jour 3 ?**

- 8 - Personnaliser son environnement
- 9 - Installer une distribution à partir d'une ISO
- 10 - Le gestionnaire de paquet et outils d'archives

---

# Plan de la formation

**Jour 4 ?**

- 11 - Notions de réseaux
- 12 - Notions de cryptographie et sécurité

**Jour 5 ?**

- 13 - Utiliser SSH pour administrer un serveur à distance
- 14 - Configurer et gérer des services : parefeu et fail2ban
- 15 - Configurer et gérer des services : serveur web
- 16 - Automatiser des tâches

---

# Evaluation ?
 -->

---

# Disclaimers

- L'informatique technique, c'est compliqué
  - ignorez les cryptonerds qui prétendent que c'est intuitif et trivial
- Soyez patients, méthodiques, attentifs !

**On est là pour apprendre :**

- Trompez-vous !
- Sortez des sentiers battus !
- Cassez des trucs !
- Interagissez, posez des questions !

---

# 0. Les origines de (GNU/)Linux

## (ou plus largement de l'informatique contemporaine)

---

## La préhistoire de l'informatique

- ~1940 : Ordinateurs électromécaniques, premiers ordinateurs programmables
- ~1950 : Transistors
- ~1960 : Circuits intégrés

...Expansion de l'informatique...

<!-- ---


## 1970 : PDP-7

![](../../images/pdp7.jpg) -->

---

## 1970 : UNIX

- Définition d'un 'standard' pour les OS
- Multi-utilisateur, multi-tâche
- Design modulaire, simple, élégant, efficace
- Adopté par les universités américaines
- Ouvert (évidemment)
- (Écrit en assembleur)

![](../../images/ritchie_thompson_kernighan.png)

---

## 1970 : UNIX

![](../../images/unixtree.png)

---

## 1975 : Le langage C

- D. Ritchie et K. Thompson définissent un nouveau langage : le C ;
- Le C rend portable les programmes ;
- Ils réécrivent une version d'UNIX en C, ce qui rend UNIX portable ;

![](../../images/ritchie_thompson.jpg)

<!--


## 1970~1985 : Les débuts d'Internet

- Définition des protocoles IP et TCP
  - Faire communiquer les machines entre elles
  - Distribué / décentralisé : peut survivre à des attaques nucléaires
- ARPANET ...

---


## 1970~1985 : Les débuts d'Internet

![](../../images/arpanet.png)

---


## 1970~1985 : Les débuts d'Internet

- Définition des protocoles IP et TCP
  - Faire communiquer les machines entre elles
  - Distribué / décentralisé : peut survivre à des attaques nucléaires
- ARPANET ...
- ... puis le "vrai" Internet
- Terminaux dans les grandes universités
- Appartition des newsgroup, ...

---


## 1980 : Culture hacker, logiciel libre

- Le logiciel devient un enjeu commercial avec des licences propriétaires
- L'informatique devient un enjeu politique
- La culture hacker se développe dans les universités
  - Partage des connaisances
  - Transparence, détournement techniques
  - Contre les autorités centrales et la bureaucratie
  - Un mouvement technique, artistique et politique

---


## 1980 : Culture hacker, logiciel libre

- R. Stallman fonde le mouvement du logiciel libre et la FSF <small>(Free Software Foundation)</small> 0. Liberté d'utiliser du programme
  1. Liberté d'étudier le fonctionnement du programme
  2. Liberté de modifier le programme
  3. Liberte de redistribuer les modificiations
- ... et le projet GNU : un ensemble de programmes libres

![](../../images/stallman.jpg)
![](../../images/gnu.png)

---


## 1990 : Création de Linux

- Linus Torvalds écrit Linux dans son garage

![](../../images/torvalds.jpg)
![](../../images/tux.png)

---


## 1990 : Création de Linux

_I'm doing a (free) operating system (**just a hobby, won't be big and professional like gnu**) for 386(486) AT clones. This has been brewing since april, and is starting to get ready. I'd like any feedback on things people like/dislike in minix, as my OS resembles it somewhat (same physical layout of the file-system (due to practical reasons) among other things)._

_I've currently ported bash(1.08) and gcc(1.40), and things seem to work. This implies that I'll get something practical within a few months, and I'd like to know what features most people would want. Any suggestions are welcome, but I won't promise I'll implement them :-)_

_Linus (torvalds@kruuna.helsinki.fi)_

_PS. [...] It is NOT portable [...] and it probably never will support anything other than AT-harddisks, as that's all I have :-(.
— Linus Torvalds_

---


## 1990 : Et en fait, Linux se développe...

- Linus Torvalds met Linux sous licence GPL
- Support des processeurs Intel
- Système (kernel + programmes) libre et ouvert
- Compatibles avec de nombreux standard (POSIX, SystemV, BSD)
- Intègre des outils de développement (e.g. compilateurs C)
- Excellent support de TCP/IP
- Création de Debian en 1993
-->

## 1990 : Linux se développe...

- Linus Torvalds met Linux sous licence GPL
- Support des processeurs Intel
- Système (kernel + programmes) libre et ouvert
- Compatibles avec de nombreux standard (POSIX, SystemV, BSD)
- Intègre des outils de développement (e.g. compilateurs C)
- Excellent support de TCP/IP
- Création de Debian en 1993

---

... L'informatique et Internet se démocratisent ...

En très résumé :

- Linux remporte le marché de l'infrastructure (routeur, serveurs, ..)
- Windows remporte le marché des machines de bureau / gaming
- Google remporte le marché des smartphones

---

---

## L'informatique contemporaine

![](../../images/datacenter.jpg)

![](../../images/laptop.jpg)
![](../../images/smartphone.jpg)

---

<!-->

## Linux aujourd'hui

- Très présent dans les routeurs, les serveurs et les smartphones
- Indépendant de tout constructeur
- Evolutif mais très stable
- Le système est fait pour être versatile et personnalisable selon son besoin
- Pratiques de sécurités beaucoup plus saines et claires que Microsoft

---

## Les distributions

Un ensemble de programmes "packagés", préconfigurés, intégré pour un usage ~précis ou suivant une philosophie particulière

- Un noyau (Linux)
- Des programmes (GNU, ...)
- Des pré-configurations
- Un gestionnaire de paquet
- Un (ou des) environnements graphiques (Gnome, KDE, Cinnamon, Mate, ...)
- Une suite de logiciel intégrée avec l'environnement graphique
- Des objectifs / une philosophie

---

## Les distributions

![](../../images/debian.png)
![](../../images/ubuntu.png)
![](../../images/mint.png)
![](../../images/centos.png)
![](../../images/arch.png)
![](../../images/kali.png)
![](../../images/android.jpg)
![](../../images/yunohost.png)

- **Debian** : réputé très stable, typiquement utilisé pour les serveurs
- **Ubuntu, Mint** : grand public
- **CentOS**, RedHat : pour les besoins des entreprises
- **Archlinux** : un peu plus technicienne, très à jour avec les dernières version des logiciels
- **Kali Linux** : orientée sécurité et pentesting
- **Android** : pour l'embarqué (téléphone, tablette)
- **YunoHost** : auto-hébergement grand-public

---

## Les distributions

Et bien d'autres : Gentoo, LinuxFromScratch, Fedora, OpenSuse, Slackware, Alpine, Devuan, elementaryOS, ...

---

## Linux, les environnement

- Gnome
- Cinnamon, Mate
- KDE
- XFCE, LXDE
- Tiling managers (awesome, i3w, ...)

---

## Linux, les environnements (Gnome)

![](../../images/gnome.jpg)

---

## Linux, les environnements (KDE)

![](../../images/kde.jpg)

---

## Linux, les environnements (Cinnamon)

![](../../images/cinnamon.jpg)

---

## Linux, les environnements (XFCE)

![](../../images/xfce.jpg)

---

## Linux, les environnements (Awesome)

![](../../images/awesome.jpg)

---

# 1. Installer une distribution

## Linux Mint

- (Choix arbitraire du formateur)
- Distribution simple, sobre, pas spécialement controversée (?)
- Profite de la stabilité de Debian et de l'accessibilité d'Ubuntu

---

-->

# 1. Rappels sur l'informatique

---

# « Informatique »

---

<!-- # L'ordinateur comme outil universel

Votre laptop doit être pour vous ce que le sabre laser est au Jedi -->

---

# 1. Rappels sur l'informatique

## Architecture d'un ordinateur

![](../../images/computer.png)

---

# 1. Rappels sur l'informatique

## Le rôle d'un OS

User
Programs
Operating System
Hardware

L'OS :

- sais communiquer avec le hardware pour exploiter les ressources
- créer des abstractions pour les programmes (e.g. fichiers)
- partage le temps de calcul entre les programmes
- s'assure que les opérations demandées sont légales

---

# 1. Rappels sur l'informatique

## Architecture d'Internet

- Décentralisé / distribué / "organique"
- Intelligence à l'extérieur

![](../../images/internet.jpg)

---

# 1. Rappels sur l'informatique

## Architecture d'Internet

- IP : routage des paquets "au mieux"
- TCP : tunnel fiable pour communiquer (IP+accusés de réception)

---

# 1. Rappels sur l'informatique

## Architecture d'Internet

Le web : un protocole parmis d'autre pour échanger de l'information, dans un format précis (pages web)
Le mail : un autre protocole(s) pour échanger de l'information, dans un autre format (les courriers)

Autres protocoles : DNS, SSH, IRC, torrent, ...

---

# 1. Rappels sur l'informatique

## Architecture d'Internet

- Programmes
- Protocole
- TCP
- IP
- Cables

Modèle client / serveur

---

---

# 2. La ligne de commande

---

## Structure d'une commande

```
  evince  --fullscreen     presentation.pdf
   |     '------------'    '------------'
   |           |                      |
   v           v                      v
  nom       options              arguments
```

---

## Exemples

Une commande peut être simple :

```
cd
```

ou assez complexe :

```
dnsmasq -x /run/dnsmasq/dnsmasq.pid -u dnsmasq -7 /etc/dnsmasq.d,.dpkg-dist,.dpkg-old,.dpkg-new --local-service
```

---

## `passwd` - Changer son password

---

## `pwd` - Afficher le dossier courant

_Print current working directory_

---

## `cd` - Naviguer dans les dossiers

```
cd  /un/dossier   # Change de dossier courant
cd                # Revient dans le home
cd ..             # Remonte d'un dossier (par exemple /home si on était dans /home/alex)
cd -              # Retourne dans le dossier où on était juste avant
```

N.B : On ne peut pas faire `cd /un/fichier` ! Ça n'a pas de sens !

---

## `ls` - Liste les fichiers d'un dossier

```
ls            # Liste les fichiers du repertoire courant
ls  /usr/bin  # Liste les fichiers du repertoire /usr/bin
ls  -a        # (ou --all) Liste les fichiers (y compris cachés)
ls  -l        # Avec des détails (type, permissions, proprio, date de modif)
ls  -t        # Trie par date de modification
ls  -h        # (ou --human-readable) Tailles lisibles comme '24K' ou '3G'
ls  *.py      # Liste tous les fichiers du repertoire courant qui se finissent par `.py`
```

(on peut combiner les options et arguments)

---

- Utiliser `ls` et `cd`, c'est comme naviguer avec un explorateur de fichier graphique !

- Un bon Jedi est toujours être attentif à :
  - où il est
  - ce qu'il cherche à faire
  - ce qu'il tape
  - ce que la machine renvoie

---

## Nettoyer son terminal

- `clean` efface tout ce qui est affiché dans le terminal
- `reset` permet de réinitialiser le terminal (utile pour certaines situation où le terminal est "cassé")
- `exit` permet de fermer un terminal
- (`logout` est similaire à `exit`)

---

## Obtenir de l'aide sur des commandes

```
man nom_de_commande
```

(navigation avec les fleches, `/mot` pour chercher un mot, `q` pour quitter)

Ou :

```
nom_de_comande --help
```

---

## Annuler / arrêter une commande en cours d'execution

- Si une commande prends trop longtemps, il est possible de l'annuler avec [Ctrl]+C

```
alex@shadow:~$ sleep 30
[...]
[Ctrl]+C
alex@shadow:~$
```

- [Ctrl]+C est à utiliser avec parcimonie ! Interrompre certaines commande peut causer des problèmes...
- (N.B. : [Ctrl]+C / [Ctrl]+V ne fais pas copier/coller dans la console !)

---

## Raccourcis et astuces de ninja

### [Tab]

- [Tab] x1 permet d'autocompléter les noms de commande et les noms de fichier (si pas d'ambiguité)
- [Tab] x2 permet de suggérer les différentes possibilités
- Double-effect kisscool : utiliser [Tab] vous permet de valider au fur à mesure que la commande et le fichier existe !

### Historique

- Vous pouvez utiliser ↑ pour retrouver les commandes précédentes
- Ou aussi : `history`

---

### Utilisez [Tab] !

---

## Utilisez [Tab] !

---

# Utilisez [Tab] !

---

# Utilisez [Tab] !

---

# Utilisez [Tab] !

---

<!-- # Le système de fichier

---

# Le système de fichier

## Généralités

- (En anglais : _filesystem_, abrégé _fs_)
- La façon dont sont organisés et référencé les fichiers
- Une abstraction de la mémoire
- Analogie : une bibliothèque avec seulement les pages des livres dans les étagères
- Le _fs_ connait le nom, la taille, l'emplacemenent des différents morceaux, la date de création, ...

---

# Le système de fichier

## Partitionnement d'un disque

- Un disque peut être segmenté en "partitions"
- Chaque partition héberge des données indépendantes des autres et sous un format / filesystem différent

![](../../images/parts.png)

---

# Le système de fichier

## Quelques systèmes de fichier classiques

- _FAT16_, _FAT32_ : disquettes, Windows 9x (~obsolète)
- _NTFS_ : système actuellement utilisé par Windows
- **EXT3**, **EXT4** : système typiquement utilisé par Linux (Ubuntu, Mint, ...)
- _HFS+_ : système utilisé par MacOS
- _TMPFS_ : système de fichier pour gérer des fichiers temporaires (`/tmp/`)
- _ZTFS_, _BRTFS_, _Tahoe-LAFS_, _FUSE_, _IPFS_, ...

--- -->

# Le système de fichier

## Sous UNIX / Linux

"Tout est fichier"

- **fichiers ordinaires** (`-`) : données, configuration, ...
- **répertoire** (directory, `d`) : gérer l'aborescence, ...
- **spéciaux** :
  - devices (`c`, `b`) (clavier, souris, disque, ...)
  - sockets (`s`), named pipe (`p`) (communication entre programmes)
  - links (`l`) ('alias' de fichiers, ~comme les raccourcis sous Windows)

---

# Le système de fichier

## Un fichier

- Un inode (numéro unique représentant le fichier)
- _Des_ noms (chemins d'accès)
  - Un même fichier peut être à plusieurs endroits en meme temps (hard link)
- Des propriétés
  - Taille
  - Permissions
  - Date de création, modification

---

# Le système de fichier

## Nommage des fichiers

- Noms sensibles à la casse
- (Eviter d'utiliser des espaces)
- Un fichier commençant par `.` est "caché"
- Les extensions de fichier sont purement indicatives : un vrai mp3 peut s'apeller musique.jpg et vice-versa
- Lorsqu'on parle d'un dossier, on l'ecrit plutôt avec un `/` à la fin pour expliciter sa nature

---

# Le système de fichier

## Arborescence de fichier

```
coursLinux/
├── dist/
│   ├── exo.html
│   └── presentation.html
├── exo.md
├── img/
│   ├── sorcery.jpg
│   └── tartiflette.png
├── presentation.md
└── template/
    ├── index.html
    ├── remark.min.js
    └── style.scss
```

---

# Le système de fichier

## Filesystem Hierarchy Standard

- `/` : racine de toute la hierarchie
- `/bin/`, `/sbin/` : programmes essentiels (e.g. `ls`)
- `/boot/` : noyau et fichiers pour amorcer le système
- `/dev/`, `/sys` : périphériques, drivers
- `/etc/` : fichiers de configuration
- `/home/` : répertoires personnels des utilisateurs
- `/lib/` : librairies essentielles
- `/proc/`, `/run` : fichiers du kernel et processus en cours
- `/root/` : répertoire personnel de `root`
- `/tmp/` : fichiers temporaires
- `/usr/` : progr. et librairies "non-essentielles", doc, données partagées
- `/var/` : fichiers / données variables (e.g. cache, logs, boîtes mails)

---

# Le système de fichier

## Répertoires personnels

- Tous les utilisateurs ont un répertoire personnel
- Classiquement `/home/<user>/` pour les utilisateurs "normaux"
- Le home de root est `/root/`
- D'autres utilisateurs ont des home particulier (`/var/mail/`, ...)

---

# Le système de fichier

## Filesystem Hierarchy Standard

![](../../images/filetree.png)

---

# Le système de fichier

## Designation des fichiers

"Rappel" :

- `.` : désigne le dossier actuel
- `..` : désigne le dossier parent
- `~` : désigne votre home

Un chemin peut être :

- Absolu : `/home/alex/dev/yunohost/script.sh`
- Relatif : `../yunohost/script.sh` (depuis `/home/alex/dev/apps/`)

Un chemin relatif n'a de sens que par rapport à un dossier donné... mais est souvent moins long à écrire

---

![](../../images/relativepath_1_1.png)

---

![](../../images/relativepath_1_2.png)

---

![](../../images/relativepath_1_3.png)

---

![](../../images/relativepath_1_4.png)

---

![](../../images/relativepath_1_5.png)

---

![](../../images/relativepath_2_1.png)

---

![](../../images/relativepath_2_2.png)

---

![](../../images/relativepath_2_3.png)

---

![](../../images/relativepath_2_4.png)

---

![](../../images/relativepath_2_5.png)

---

![](../../images/relativepath_2_6.png)

---

![](../../images/relativepath_2_7.png)

---

# Le système de fichier

## Chemins relatifs

- d'exemples, tous équivalents (depuis `/home/alex/dev/apps/`)

* `/home/alex/dev/yunohost/script.sh`
* `~/dev/yunohost/script.sh`
* `../yunohost/script.sh`
* `./../yunohost/script.sh`
* `./wordpress/../../yunohost/script.sh`
* `../.././music/.././../barbara/.././alex/dev/ynh-dev/yunohost/script.sh`

---

# Le système de fichier

## Manipuler des fichiers (1/4)

- `ls` : lister les fichiers
- `cat <fichier>` : affiche le contenu d'un fichier dans la console
- `wc -l <fichier>` : compte le nombre de lignes dans un fichier

Exemples :

```bash
ls /usr/share/doc/                       # Liste les fichiers de /usr/share/doc
wc -l /usr/share/doc/nano/nano.html      # 2005 lignes !
```

---

# Le système de fichier

## Manipuler des fichiers (2/4)

- `head <fichier>`, `tail <fichier>` : affiche les quelques premières ou dernières ligne du fichier
- `less <fichier>` : regarder le contenu d'un fichier de manière "interactive"
  - ↑, ↓, ⇑, ⇓ pour se déplacer
  - `/mot` pour chercher un mot
  - `q` pour quitter

```bash
tail -n 30 /usr/share/doc/nano/nano.html # Affiche les 30 dernieres lignes du fichier
less /usr/share/doc/nano/nano.html       # Regarder interactivement le fichier
```

---

# Le système de fichier

## Manipuler des fichiers (3/4)

- `touch <fichier>` : créer un nouveau fichier, et/ou modifie sa date de modification
- `nano <fichier>` : éditer un fichier dans la console
  - (`nano` créera le fichier si besoin)
  - [Ctrl]+X pour enregistrer+quitter
  - [Ctrl]+W pour chercher
  - [Alt]+Y pour activer la coloration syntaxique
- `vim <fichier>` : alternative à nano
  - plus puissant (mais plus complexe)

---

# Le système de fichier

## Manipuler des fichiers (4/4)

- `cp <source> <destination>` : copier un fichier
- `rm <fichier>` : supprimer un fichier
- `mv <fichier> <destination>` : déplace (ou renomme) un fichier

Exemple

```bash
cp cours.html coursLinux.html  # Créée une copie avec un nom différent
cp cours.html ~/bkp/linux.bkp  # Créée une copie de cours.html dans /home/alex/bkp/
rm cours.html                  # Supprime cours.html
mv coursLinux.html linux.html  # Renomme coursLinux.html en linux.html
mv linux.html ~/archives/      # Déplace linux.html dans ~/archives/
```

---

# Le système de fichier

## Manipuler des dossiers (1/3)

- `pwd` : connaître le dossier de travail actuel
- `cd <dossier>` : se déplacer vers un autre dossier

---

# Le système de fichier

## Manipuler des dossiers (2/3)

- `mkdir <dossier>` : créer un nouveau dossier
- `cp -r <source> <destination>` : copier un dossier et l'intégralité de son contenu

Exemples :

```bash
mkdir ~/dev           # Créé un dossier dev dans /home/alex
cp -r ~/dev ~/dev.bkp # Créé une copie du dossier dev/ qui s'apelle dev.bkp/
cp -r ~/dev /tmp/     # Créé une copie de dev/ et son contenu dans /tmp/
```

---

# Le système de fichier

## Manipuler des dossiers (3/3)

- `mv <dossier> <destination>` : déplace (ou renomme) un dossier
- `rmdir <dossier>` : supprimer un dossier vide
- `rm -r <dossier>` : supprimer un dossier et tout son contenu récursivement

Exemples :

```bash
mv dev.bkp  dev.bkp2   # Renomme le dossier dev.bkp en dev.bkp2
mv dev.bkp2 ~/trash/   # Déplace dev.bkp2 dans le dossier ~/trash/
rm -r ~/trash          # Supprime tout le dossier ~/trash et son contenu
```

---

# Le système de fichier

## Les liens durs (hard link)

![](../../images/hardlink.png)

- `ln <source> <destination>`
- Le même fichier ... à plusieurs endroits !
- Supprimer une instance de ce fichier ne supprime pas les autres

---

# Le système de fichier

## Les liens symboliques (symlink)

![](../../images/symlink.png)

- `ln -s <cible> <nom_du_lien>`
- Similaire à un "raccourci", le fichier n'est pas vraiment là .. mais comme si
- Supprimer le fichier pointé par le symlink "casse" le lien

---

# Le système de fichier

## Les liens symbolic (symlink)

![](../../images/symlink.png)

- Dans ce exemple, le lien a été créé avec
  - `ln -s ../../../conf/ynh.txt conf.json`
- `conf.json` est "le raccourci" : on peut le supprimer sans problème
- `ynh.txt` est la cible : le supprimer rendra inopérationnel le raccourci

---

<!-- # Le système de fichier

## Les points de montage

![](../../images/mounpoints.png)

---

# Le système de fichier

## Notation des patitions

Les disques partitions sous Linux sont généralement dénommées :

- `/dev/sda` (premier disque)
  - `/dev/sda1` (première partition de /dev/sda)
  - `/dev/sda2` (deuxieme partition de /dev/sda)
- `/dev/sdb` (deuxieme disque)
  - `/dev/sdb1` (première partition de /dev/sdb)
  - `/dev/sdb2` (deuxieme partition de /dev/sdb)
  - `/dev/sdb3` (troisieme partition de /dev/sdb)

---

# Le système de fichier

## Outil pour lister les disques, gérer les partions

```bash
$ fdisk -l
Disk /dev/sda: 29.8 GiB, 32017047552 bytes, 62533296 sectors
[...]
Device       Start      End  Sectors  Size Type
/dev/sda1     2048  2099199  2097152    1G Linux filesystem
/dev/sda2  2099200 62524946 60425747 28.8G Linux filesystem
```

```bash
$ fdisk /dev/sda
[editer interactivement le partition de /dev/sda]
```

---

# Le système de fichier

## Outil pour lister les disques, gérer les partions

`parted` et `gparted` (outil graphique très pratique !)

---

# Le système de fichier

## Les points de montage

Une partition ou n'importe quel "bidule de stockage" peut être "monté" dans le système de fichier

- partition d'un disque
- clef usb
- image iso
- stockage distant
- ...

---

# Le système de fichier

## Les points de montage

Les points de montages sont gérés avec `mount`

```bash
$ mkdir /media/usbkey
$ mount /dev/sdb1 /media/usbkey
$ ls /media/usbkey
# [le contenu de la clef usb s'affiche]
```

---

# Le système de fichier

## Les points de montage

On peut "démonter" un element monté avec `umount`

```bash
$ umount /media/usbkey
```

---

# Le système de fichier

## Les points de montage : `/etc/fstab`

`/etc/fstab` décrit les systèmes de fichier montés automatiquement au boot

```text
# <file system>     <mountpoint> <type>  <options>       <dump>  <pass>
UUID=[id tres long] /            ext4    default         0       1
UUID=[id tres long] /home/       ext4    defaults        0       2
```

<small>(historiquement, la premiere colomne contenait `/dev/sdxY`, mais les UUID sont plus robustes)</small>

---

# Le système de fichier

## Les points de montage : outils

Juste `mount` permet aussi de lister les différents points de montage

```bash
$ mount
[...]
/dev/sda1 on /boot type ext4 (rw,noatime,discard,data=ordered)
/dev/sda2 on / type ext4 (rw,noatime,discard,data=ordered)
/dev/sdb1 on /media/usbkey type ntfs (rw,noatime,discard,data=ordered)
```

---

# Le système de fichier

## Les points de montage : outils

Il existe aussi `df` :

```bash
$ df -h
Filesystem      Size  Used Avail Use% Mounted on
dev             2.8G     0  2.8G   0% /dev
run             2.8G  1.1M  2.8G   1% /run
/dev/dm-0        29G   22G  5.0G  82% /
tmpfs           2.8G   22M  2.8G   1% /dev/shm
tmpfs           2.8G  1.9M  2.8G   1% /tmp
/dev/sda1       976M  105M  804M  12% /boot
tmpfs           567M   16K  567M   1% /run/user/1000
/dev/sdb1       3.9G  105M  3.7M   3% /media/usbkey
```

---

# Le système de fichier

## Les points de montage : outils

Et aussi `lsblk` :

```bash
$ lsblk
NAME          MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
sda             8:0    0 29.8G  0 disk
├─sda1          8:1    0    1G  0 part  /boot
└─sda2          8:2    0 28.8G  0 part  /
```

---

# Utilisateurs et groupes

---
--- -->

# Utilisateurs et groupes

## Généralités

- une entité / identité (!= être humain) qui demande des choses au système
- possède des fichiers, peut en créer, modifier, naviguer, ...
- peut lancer des commandes / des processus

# Utilisateurs et groupes

## Répertoire des utilisateurs

Classiquement, les utilisateurs sont répertoriés dans `/etc/passwd`

```
alex:x:1000:1000:Zee Aleks:/home/alex:/bin/bash
```

- identifiant / login
- `x` (historique)
- uid (id utilisateur)
- gid (id de groupe)
- commentaire
- répertoire home
- shell de démarrage

---

# Utilisateurs et groupes

## root

- `uid=0`, `gid=0`
- Dieu sur la machine
- **With great power comes great responsabilities**
  - Si un attaquant devient root, l'OS est entièrement compromis (à jamais)

![](../../images/iamroot.jpg)
![](../../images/heistheone.png)

---

# Utilisateurs et groupes

## Passer root (ou changer d'utilisateur)

```bash
su          # Demande à ouvrir un shell en tant que root
su barbara  # Demande à ouvrir un shell en tant que barbara
exit        # Quitter un shell
```

---

# Utilisateurs et groupes

## Sudo

- On peut autoriser les utilisateurs à faire des choses en root en leur donnant les droits 'sudo'

```bash
su -c "ls /root/"   # Executer 'ls /root/' en tant que root (de manière ephemere)
sudo ls /root/      # Meme chose mais avec sudo
sudo whoami         # Renvoie "root"
sudo su             # Ouvrir un shell root via sudo...
```

- Suivant la commande demandée, le mot de passe n'est pas le même...
  - su : mot de passe root
  - sudo : mot de passe utilisateur

---

# Utilisateurs et groupes

## Les groupes

- Chaque user à un groupe associé qui possède le même nom
- Des groupes supplémentaires peuvent être créés
- Ils permettent ensuite de gérer d'accorder des permissions spécifiques

Exemples :

- `students`
- `usb`
- `power`

---

# Utilisateurs et groupes

## Mot de passe

- Autrefois dans `/etc/passwd` (accessibles à tous mais hashés)
- Maintenant dans `/etc/shadow` (accessibles uniquement via root)

```
alex:$6$kncRwIMqSb/2PLv3$x10HgX4iP7ZImBtWRChTyufsG9XSKExHyg7V26sFiPx7htq0VC0VLdUOdGQJBJmN1Rn34LRVAWBdSzvEXdkHY.:0:0:99999:7:::
```

---

# (Parenthèse sur le hashing)

```
$ md5sum coursLinux.html
458aca9098c96dc753c41ab1f145845a
```

...Je change un caractère...

```
$ md5sum coursLinux.html
d1bb5db7736dac454c878976994d6480
```

---

# (Parenthèse sur le hashing)

Hasher un fichier (ou une donnée) c'est la transformer en une chaîne :

- de taille fixe
- qui semble "aléatoire" et chaotique (mais déterministe !)
- qui ne contient plus l'information initiale

Bref : une empreinte caractérisant une information de manière très précise

---

# Utilisateurs et groupes

## Commandes utiles

```bash
whoami                  # Demander qui on est...!
groups                  # Demander dans quel groupe on est
id                      # Lister des infos sur qui on est (uid, gid, ..)
passwd <user>           # Changer son password (ou celui de quelqu'un si on est root)
who                     # Lister les utilisateurs connectés
useradd <user>          # Créé un utilisateur
userdel <user>          # Supprimer un utilisateur
groupadd <group>        # Ajouter un groupe
usermod -a -G <group> <user>  # Ajouter un utilisateur à un groupe
```

---

# Permissions

---

# Permissions

## Généralités

- Chaque fichier a :
  - un utilisateur proprietaire
  - un groupe proprietaire
  - des permissions associés
- (`root` peut tout faire quoi qu'il arrive)
- Système relativement minimaliste mais suffisant pour pas mal de chose
  - (voir SELinux pour des mécanismes avancés)

```
$ ls -l coursLinux.html
-rw-r--r-- 1 alex alex 21460 Sep 28 01:15 coursLinux.html

    ^         ^     ^
    |         |     '- groupe proprio
    |          '- user proprio
    les permissions !
```

---

# Permissions

![](../../images/permissions.jpg)

---

# Permissions

![](../../images/permissions2.png)

---

# Permissions

## Permissions des **fichiers**

- `r` : lire le fichier
- `w` : écrire dans le fichier
- `x` : executer le fichier

---

# Permissions

## Permissions des **dossiers**

- `r` : lire le contenu du dossier
- `w` : créer / supprimer des fichiers
- `x` : traverser le répertoire

(On peut imager que les permissions d'un dossier soient `r--` ou `--x`)

---

# Permissions

## Gérer les propriétaires

**(Seul root peut faire ces opérations !!)**

```bash
chown <user> <cible>          # Change l'user proprio d'un fichier
chown <user>:<group> <cible>  # Change l'user et groupe proprio d'un fichier
chgrp <group> <cible>         # Change juste le groupe d'un fichier
```

Exemples :

```bash
chown barbara:students coursLinux.md  # "Donne" coursLinux.md à barbara et au groupe students
chown -R barbara /home/alex/dev/      # Change le proprio récursivement !
```

---

# Permissions

## Gérer les permissions

```bash
chmod <changement> <cible>   # Change les permissions d'un fichier
```

Exemples

```bash
chmod u+w   coursLinux.html  # Donne le droit d'ecriture au proprio
chmod g=r   coursLinux.html  # Remplace les permissions du groupe par "juste lecture"
chmod o-rwx coursLinux.html  # Enlève toutes les permissions aux "others"
chmod -R +x ./bin/           # Active le droit d'execution pour tout le monde et pour tous les fichiers dans ./bin/
```

---

# Permissions

## Représentation octale

![](../../images/chmod_octal.png)

---

# Permissions

![](../../images/chmod_octal2.png)

---

# Permissions

## Gérer les permissions .. en octal !

```bash
chmod <permissions> <cible>
```

Exemples

```bash
chmod 700 coursLinux.html  # Fixe les permissions à rwx------
chmod 644 coursLinux.html  # Fixe les permissions à rw-r--r--
chmod 444 coursLinux.html  # Fixe les permissions à r--r--r--
```

---

# Permissions

## Chown vs. chmod

![](../../images/chown_chmod.png)

---

# Permissions

Lorsque l'on fait :

```bash
$ /etc/passwd
```

On tente d'executer le fichier !

Obtenir comme réponse

```bash
-bash: /etc/passwd: Permission denied
```

ne signifie pas qu'on a pas les droits de lecture sur le fichier, mais bien que l'on a "juste" pas le droit de l'executer <small>(car ça n'a en fait pas de sens de chercher à l'executer)</small>

---

# Processus

---

# Processus

## Généralités

- Un processus est _une instance_ d'un programme en cours d'éxécution
- (Un même programme peut tourner plusieurs fois sous la forme de plusieurs processus)

- Un processus utilise des ressources :

  - code qui s'execute dans le CPU, ou en attente en cache/RAM
  - données du processus en cache/RAM
  - autres ressources (port, fichiers ouverts, ...)

- Un processus a des attributs (iidentifiant, proprio, priorité, ...)

---

# Processus

## Execution (1/2)

La machine comprends seulement du code machine ("binaire").

Un programme est donc soit :

- compilé (par ex. un programme en C)
- interprété par un autre programme, qui lui est compilé (par ex. un programme en python, interprété par l'interpreteur python)

Rappel : UNIX est multi-tâche, multi-utilisateur

- partage de temps, execution parallèle
- coordonnées par le kernel

---

# Processus

## Execution (2/2)

Un processus est lancé soit :

- en interactif (depuis un shell / la ligne de commande)
- de manière automatique (tâche programmées, c.f. `at` et jobs cron)
- en tant que daemon/service

En mode interactif, on peut interragir directement avec le processus pendant qu'il s'execute

---

# Processus

## Attributs

- Propriétaire
- PID (processus ID)
- PPID (processus ID du parent !)
- Priorité d'execution
- Commande / programme lancé
- Entrée, sortie

---

# Processus

## Lister les processus et leurs attributs (1/2)

```bash
ps aux            # Liste tous les processus
ps ux -U alex     # Liste tous les processus de l'utilisateur alex
ps -ef --forest   # Liste tous les processus, avec des "arbres de parenté"
pstree            # Affiche un arbre de parenté entre les processus
```

Exemple de `ps -ef --forest`

```
  935   927  0 Sep25 ?      00:00:52  \_ urxvtd
 3839   935  0 Sep26 pts/1  00:00:00      \_ -bash
16076  3839  0 00:49 pts/1  00:00:49      |   \_ vim coursLinux.html
20796   935  0 Sep27 pts/2  00:00:00      \_ -bash
 2203 20796  0 03:10 pts/2  00:00:00      |   \_ ps -ef --forest
13070   935  0 00:27 pts/0  00:00:00      \_ -bash
13081 13070  0 00:27 pts/0  00:00:00          \_ ssh dismorphia -t source getIrc.sh
```

---

# Processus

## Lister les processus et leurs attributs (2/2)

Et aussi :

```bash
top               # Liste les processus actif interactivement
  -> [shift]+M    #    trie en fonction de l'utilisation CPU
  -> [shift]+P    #    trie en fonction de l'utilisation RAM
  -> q            # Quitte
```

---

# Processus

## Priorité des processus (1/2)

- Il est possible de régler la priorité d'execution d'un processus
- "Gentillesse" (_niceness_) entre -20 et 19
  - -20 : priorité la plus élevée
  - 19 : priorité la plus basse
- Seul les process du kernel peuvent être "méchant"
  - niceness négative, et donc les + prioritaires

---

# Processus

## Priorité des processus (2/2)

```bash
nice -n <niceness> <commande> # Lancer une commande avec une certaine priorité
renice <modif> <PID>       # Modifier la priorité d'un process
```

Exemples :

```bash
# Lancer une création d'archive avec une priorité faible
nice 5 tar -cvzf archive.tar.gz /home/
# Redéfinir la priorité du processus 9182
renice +10 9182
```

---

# Processus

## Gérer les processus interactif

```bash
<commande>            # Lancer une commande de façon classique
<commande> &          # Lancer une commande en arrière plan
[Ctrl]+Z  puis 'bg'   # Passer la commande en cours en arrière-plan
fg                    # Repasser une commande en arrière-plan en avant-plan
jobs                  # Lister les commandes en cours d'execution
```

---

# Processus

## Tuer des processus

```bash
kill <PID>     # Demande gentillement à un processus de finir ce qu'il est en train de faire
kill -9 <PID>  # Tue un processus avec un fusil à pompe
pkill <nom>    # (pareil mais via un nom de programme)
pkill -9 <nom> # (pareil mais via un nom de programme)
```

Exemples

```bash
kill 2831
kill -9 2831
pkill java
pkill -9 java
```

---

<!-- # Processus

![](../../images/dontsigkill.png)

--- -->

<!-- # Processus

## `screen`

`screen` permet de lancer une commande dans un terminal que l'on peut récupérer plus tard

1. On ouvre une session avec `screen`
2. On lance ce que l'on veut dedans
3. On peut sortir de la session avec `<Ctrl>+A` puis `D`.
4. La commande lancée continue à s'executer
5. On peut revenir dans la session plus tard avec `screen -r` -->
