---
title: Administration Linux - feuille d'exercice n.1
draft: false
weight: 15
---

# Le gestionnaire de paquet (et les archives)

### Gestionnaire de paquet

- 1.11 Suite à l'installation de votre système, vous voulez vous assurer qu'il est à jour.

  - Lancez la commande `apt update`. Quels dépôts sont contactés pendant cette opération ?
  - À l'aide de `apt list --upgradable`, identifiez si `firefox`, `libreoffice`, `linux-firmware` et `apt` peuvent être mis à jour - et identifiez l'ancienne version et la nouvelle version.
  - Lancez la mise à jour avec `apt dist-upgrade`. Pendant le déroulement de la mise à jour, identifiez les trois parties clefs du déroulement : liste des tâches et validation par l'utilisateur, téléchargement des paquets, et installation/configuration.

- 1.12 - Cherchez avec `apt search` si le programme `sl` est disponible. (Utiliser `grep` pour vous simplifiez la tâche). À quoi sert ce programme ? Quelles sont ses dépendances ? (Vous pourrez vous aider de `apt show`). Finalement, installez ce programme en prêtant attention aux autres paquets qui seront installés en même temps.
- 1.13 - Même chose pour le programme `lolcat`
<!-- - 1.14 - Même chose pour le programme `nyancat` - mais cette fois, trouvez un moyen de télécharger le `.deb` directement depuis le site de debian qui référence les paquets, puis installez ce `.deb` avec `dpkg -i`. -->
- 1.15 - Parfois, il est nécessaire d'ajouter un nouveau dépôt pour installer un programme (parce qu'il n'est pas disponible, ou bien parce qu'il n'est pas entièrement à jour dans la distribution utilisée). Ici, nous prendrons l'exemple de `docker` qui n'est disponible que via un dépôt précis maintenu par Docker.

  - Regarder avec `apt search` et `apt show` (et `grep` !) si le paquet `docker` est disponible et quelle est la version installable.
  - Exécuter la commande suivante qui va ajouter le dépôt de Docker:

  ```bash
  sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
  ```

  <!-- - Ajouter un nouveau fichier (par exemple `docker.list`) dans `/etc/apt/sources.list.d` avec une unique ligne : `deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.0 multiverse` -->

  - Faire `apt update`. Que se passe-t-il ? Quels serveurs votre machine a-t-elle essayé de contacter ? Pourquoi cela produit-il une erreur ?
  - Ajoutez la clef d'authentification des paquets avec `curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`.
  - Vérifiez l'empreinte de la clé ajoutée qui devrait être `9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88`. Pour cela, cherchez les 8 derniers caractères de cette clé, comme ceci :

```bash
$ sudo apt-key fingerprint 0EBFCD88

pub   rsa4096 2017-02-22 [SCEA]
      9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
uid           [ unknown] Docker Release (CE deb) <docker@docker.com>
sub   rsa4096 2017-02-22 [S]
```

- Refaire `apt update`. Est-ce que ça fonctionne ?
- Regarder avec `apt search` et `apt show` (et `grep` !) si le paquet `docker-ce` est disponible et quelle est la version installable.
- Installer le paquet. Depuis où a-t-il été téléchargé ?
<!-- - Désinstallez ce paquet (en purgeant les données / fichiers) et supprimez le `mongodb.list` puis refaites un `apt update` pour remettre à plat la liste des paquets disponibles. -->

```

```

- 1.16 - Regardez le contenu de `/var/cache/apt/archives`. À quoi ces fichiers correspondent-ils ?
<!-- Trouvez deux méthodes pour nettoyer ces fichiers, l'une "brutale" avec `rm`, et l'autre "propre" avec `apt`. -->
- 1.17 - Utilisez `aptitude why` pour trouver la raison pour laquelle le paquet `libxcomposite1` est installé
- 1.18 - Utilisez `apt-rdepends` pour afficher la liste des dépendances de `libreoffice`.
- 1.19 - Identifiez l'utilité de la commande `apt moo`

### Gestion des archives

- 1.20 - Créez une archive (non-compressée !) de votre répertoire personnel avec `tar`.
- 1.21 - En utilisant `gzip`, produisez une version compressée de l'archive de la question précédente
- 1.22 - Recommencez mais produisant une version compressée directement
- 1.23 - En fouillant dans les options de `tar`, trouvez un moyen de lister le contenu de l'archive
- 1.24 - Créez un dossier `test_extract` dans `/tmp/`, déplacez l'archive dans ce dossier puis décompressez-là dedans.
- 1.25 - Trouvez un ou des fichiers `.gz` dans `/var/log` (ou ailleurs ?) et cherchez comment combiner `cat` et `gzip` pour lire le contenu de ce fichier sans créer de nouveau fichier.

<!-- ### Exercices avancés


- Investiguez les options de `apt-rdepends` et du programme `dot` pour générer un rendu en PNG du graphe de dépendance de `firefox`.
- Trouvez où télécharger le `.deb` du paquet `nyancat` depuis `ftp.debian.org`
- (Très avancé) Renseignez-vous sur `equivs` et créez un package virtuel `lolstuff` qui dépend de `sl`, `lolcat` et `nyancat` -->
