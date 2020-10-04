## Importez une machine Linux

- Récupérez (sur un disque ou dans le partage réseau) une machine virtualbox ubuntu (18.04)
- Configurez-la avec 8Go de RAM si possible
- Démarrez la machine
- Faites les mises à jour (`sudo apt update` et `sudo apt upgrade`)

## Installer quelques logiciels

- Installez VSCode avec le gestionnaire de paquet `snap install code --classic`
- En ligne de commande (`apt`) installez `git`, `htop`, `ncdu`

## Explorer Ubuntu Bionic (18.04) : Démo

## Explorer l'éditeur VSCode : Démo

---

## Comment installer une machine virtuelle

- Un ordinateur "simulé" dans un ordinateur
  - VirtualBox est un logiciel permettant ce genre de chose
- Parti pris : Ubuntu avec Gnome

---

## Installer une machine virtuelle

![](../../images/vbox1.png)

---

![](../../images/vbox2.png)

---

Télécharger une Ubuntu 18.04 préinstallée sur OSboxes.org

![](../../images/osboxes_mint.png)

---

## Installer une machine virtuelle

- Installer Virtualbox
- Créer une nouvelle machine virtuelle
  - De type Linux / Ubuntu (64 bit)
  - 2048 Mo de RAM devraient suffir
  - Au moment de choisir le disque dur : fournir le fichier VDI de OSboxes / Linux Mint
- Démarrer la machine et observer les étapes de démarrage

---

# 2. Prendre en main sa machine et le terminal

## Se connecter

Pour cette première connexion, nous allons passer par un tty plutôt que par le login graphique.

Pour ce faire, appuyer sur Ctrl+Alt+F2 (ou F3, F4, ...)

```
Debian Stretch <nom_de_machine> tty0

<nom_de_machine> login: █
```

---

# 2. Prendre en main sa machine et le terminal

## Se connecter

Pour cette première connexion, nous allons passer par un tty plutôt que par le login graphique.

Pour ce faire, appuyer sur Ctrl+Alt+F2 (ou F3, F4, ...)

```
Debian Stretch <nom_de_machine> tty0

<nom_de_machine> login: votre_login
Password: █        # <<<< le mot de passe ne s'affiche pas du tout quand on le tape !
```

---

# 2. Prendre en main sa machine et le terminal

## Se connecter

Pour cette première connexion, nous allons passer par un tty plutôt que par le login graphique.

Pour ce faire, appuyer sur Ctrl+Alt+F2 (ou F3, F4, ...)

```
Debian Stretch <nom_de_machine> tty0

<nom_de_machine> login: votre_login
Password:
Last login: Wed 19 Sep 16:23:42 on tty2
votre_login@machine:~$ █
```

---

# 2. Prendre en main sa machine et le terminal

## Premières commandes

Changez votre mot de passe :

- Taper `passwd` puis _Entrée_ puis suivez les instructions

```
votre_login@machine:~$ passwd
Changing password for votre_login.
(current) UNIX password:
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
votre_login@machine:~$ █
```

---

![](../../images/password-mistakes.png)

---

# 2. Prendre en main sa machine et le terminal

## Premières commandes

- Taper `pwd` puis _Entrée_ et observer
- Taper `ls` puis _Entrée_ et observer
- Taper `cd /var` puis _Entrée_ et observer
- Taper `pwd` puis _Entrée_ et observer
- Taper `ls` puis _Entrée_ et observer
- Taper `ls -l` puis _Entrée_ et observer
- Taper `echo 'Je suis dans la matrice'` puis _Entrée_ et observer

---

# 2. Prendre en main sa machine et le terminal

## Discussion

- Nous nous sommes connectés à une machine
- Nous avons eu accès à un terminal
- Le terminal permet de taper des commandes pour interagir "directement" avec l'OS
- Des commandes comme dans "passer commande"
- Certaines affichent des choses, d'autres changent des états
- Vous pouvez ouvrir d'autres TTy / consoles avec Ctrl+Alt+F1, F2, F3, ..
