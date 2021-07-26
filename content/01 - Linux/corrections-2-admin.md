---
title: "Corrections exercices partie 2"
weight: 90
# pre: "<i class='fab fa-git'></i> - "
draft: false
---

### Gestion des archives

- 1.20 - Créez une archive (non-compressée !) de votre répertoire personnel avec `tar`.

```bash
tar -cvf monhome.tar /home/mon_utilisateur
```

- 1.21 - En utilisant `gzip`, produisez une version compressée de l'archive de la question précédente

```bash
gzip monhome.tar
# Ensuite on trouve monhome.tar.gz qui a une taille reduite
# comparé à avant
```

- 1.22 - Recommencez mais produisant une version compressée directement

```bash
tar -cvzf monhome.tar.gz /home/mon_utilisateur
```

- 1.23 - En fouillant dans les options de `tar`, trouvez un moyen de lister le contenu de l'archive

```bash
tar -tvf monhome.tar
```

- 1.24 - Créez un dossier `test_extract` dans `/tmp/`, déplacez l'archive dans ce dossier puis décompressez-là dedans.

```bash
mkdir /tmp/test_extract
mv monhome.tar.gz /tmp/test_extract
cd /tmp/test_extract
tar -xvzf monhome.tar.gz ./
```

- 1.25 - TODO 

- 1.26 - Il existe des fichiers de logs g-zippés comme `/var/log/apt/history.log.1.gz` (si vous avez fait quelques commandes avec apt). Il contient l'historique des opérations récentes effectuées avec `apt`. Ou encore : `/var/log/dmesg.1.gz` (si votre machine a déjà démarré plusieurs fois) qui contient les historiques de démarrage du système. On peut faire `find /var/log -name "*.gz"` pour trouver tous les fichiers de log zippés. Si l'on utilise uniquement `cat /var/log/apt/history.log.1.gz`, le résultat n'est pas lisible car il s'agit d'un flux binaire. Il est néanmoins possible de le dézipper "à la volée" en pipant le résultat dans gzip : 

```
cat /var/log/apt/history.log.1.gz | gzip -d
```

Ou bien il existe une commande `zcat` qui fait cette opération directement : 

```
zcat /var/log/apt/history.log.1.gz
```
