---
title: 10. Fichiers
draft: false
weight: 21
---


## Lire "brutalement"

```python
mon_fichier = open("/etc/passwd", "r")
contenu_du_fichier = mon_fichier.readlines()
mon_fichier.close()

for ligne in contenu_du_fichier:
    print(ligne)
```

Attention à bien distinguer:

- le nom du fichier (`passwd`) et son chemin d'accès absolu (`/etc/passwd`)
- le vrai fichier qui existe sur le disque
- la variable / objet Python (dans l'exemple, nommée `f`) qui est une interface pour interagir avec ce fichier


## Lire, avec une "gestion de contexte"

```python
with open("/etc/passwd", "r") as mon_fichier:
    contenu_du_fichier = mon_fichier.readlines()

for ligne in contenu_du_fichier:
    print(ligne)
```

### Explications

- `open("fichier", "r")` ouvre un fichier en lecture
- `with ... as ...` ouvre un contexte, à la fin duquel le fichier sera fermé automatiquement
- `f.readlines()` permet d'obtenir une liste de toutes les lignes du fichier


## Lire

- `f.readlines()` renvoie une **liste** contenant les lignes une par une
- `f.readline()` renvoie une ligne du fichier à chaque appel.
- `f.read()` renvoie une (grande) **chaĩne** contenant toutes les lignes concaténées

- Attention, si je modifie la variable `contenu_du_fichier` ... je ne modifie pas vraiment le fichier sur le disque ! Pour cela, il faut explicitement demander à *écrire* dans le fichier.


## Ecrire

### En remplacant tout !

```python
with open("/home/alex/test", "w") as f:
    f.write("Plop")
```

### À la suite (« append »)

```python
with open("/home/alex/test", "a") as f:
    f.write("Plop")
```


## Fichiers et exceptions

```python
try:
    with open("/some/file", "r") as f:
        lines = f.readlines()
except:
    raise Exception("Impossible d'ouvrir le fichier en lecture !")
```



## Un autre exemple

```python
try:
    with open("/etc/shadow", "r") as f:
        lines = f.readlines()
except PermissionError:
    raise Exception("Pas le droit d'ouvrir le fichier !")
except FileNotFoundError:
    raise Exception("Ce fichier n'existe pas !")
```


## Note "technique" sur la lecture des fichiers

- Il y a un "curseur de lecture". On peut lire petit morceaux par petit morceaux ... une fois arrivé au bout, il n'y a plus rien à lire, il faut replacer le curseur si on veut de nouveau lire.

```python
f = open("/etc/passwd")
print(f.read())  # ---> Tout plein de choses
print(f.read())  # ---> Rien !
f.seek(0)        # On remet le curseur au début
print(f.read())  # ---> Tout plein de choses !
```

### Ex.10 Fichiers

9.1 : Créer un fonction liste_users qui lit le fichier `/etc/passwd` et retourne la liste des utilisateurs ayant comme shell de login `/bin/bash`.

9.2 : Dans le code Python, écrire un modèle d'email comme:

```python
modele = """
Bonjour {prenom} !
Voici en pièce jointe les billets pour votre voyage en train vers {destination}.
"""
```

Ecrire une fonction `generer_email` qui remplace dans `modele` les chaines `{prenom}`et `{destination}` par des arguments fourni à la fonction, et enregistre le résultat dans un fichier `email_{prenom}.txt`. Par exemple, `generer_email("Alex", "Strasbourg")` générera le texte et sauvegardera le résultat dans `email_Alex.txt`.

9.3 : Écrire une fonction qui permet d'afficher un fichier sans les commentaires et les lignes vides. Spécifier le caractère qui symbolise le début d'un commentaire en argument de la fonction. (Ou pourra utiliser la méthode `strip()` des chaînes de caractère pour identifier plus facilement les lignes vides)

### Ex.10. Librairies

Les énoncés des exercices suivants peuvent être un peu plus ouverts que les précédents, et ont aussi pour objectifs de vous inciter à explorer la documentation des librairies (ou Internet en général...) pour trouver les outils dont vous avez besoin. Il existe de nombreuse façon de résoudre chaque exercice.

JSON, requests et argparse

10.1.1 : Télécharger le fichier `https://app.yunohost.org/apps.json` (avec votre navigateur ou `wget` par exemple). Écrire une fonction qui lit ce fichier, le charge en tant que données json. Écrire une autre fonction capable de filter le dictionnaire pour ne garder que les apps d'un level supérieur à `n` donné en argument. Écrire une fonction similaire pour le status (`working, inprogress, notworking`).

10.1.2 : Améliorer le programme précédent pour récupérer la liste directement depuis le programme avec `requests`. (Ajoutez une instruction pour s'assurer que le code du retour est bien 200 avant de continuer).

10.1.3 : Exporter le résultat d'un filtre (par exemple toutes les applications avec level >= 7) dans un fichier json.

10.1.4 : À l'aide de la librairie `argparse`, paramétrez le tri à l'aide d'un argument donné en ligne de commande. Par exemple: `python3 filtre_apps.py --level 7` exportera dans "result.json" seulement les apps level >= 7.

### CSV

10.2.1 : Récupérer le fichier de données CSV auprès du formateur, le lire, et afficher le nom des personnes ayant moins de 24 ans. Pour ce faire, on utilisera la librarie csv.

10.2.2 : Trier les personnes du fichier CSV par année de naissance et enregistrer une nouvelle version de ce fichier avec seulement le nom et l'année de naissance. Pour trier, on pourra utiliser `sorted` et son argument `key`.

### Random

10.3 : Écrire une fonction `jets_de_des(N)` qui simule N lancés de dés 6 et retourne le nombre d'occurence de chaque face dans un dictionnaire. Par exemple : ``{1: 13, 2:16, 3:12, ... }``. Calculer ensuite la frequence (`nb_occurences / nb_lancés_total`) pour chaque face. Testez avec un N grand et en déduire si votre dé virtuel est pipé ou non.

10.4 : Écrire un fonction `create_tmp_dir` qui choisi un nombre au hasard entre 0 et 100000 puis créer le dossier `/tmp/tmp-{lenombre}` et retourne le nom du dossier ainsi créé. On pourra utiliser la librairie `random` pour choisir un nom aléatoire, et `os.system` ou `subprocess.check_call` pour créer le dossier.

### Interaction avec le systeme de fichier

10.5.1 : Écrire une fonction qui permet de trouver récursivement dans un dossier tous les fichiers modifiés il y a moins de 5 minutes.

10.5.2 : À l'aide d'une deuxième fonction permettant d'afficher les n dernières lignes d'un fichier, afficher les 10 dernières lignes des fichiers récemment modifiés dans /var/log

### Interaction avec l'OS

10.6 : Écrire une fonction qui récupère l'utilisation actuelle de la mémoire RAM via la commande `free`. La fonction retournera une utilisation en pourcent.

10.7 : Écrire une fonction qui renvoie les 3 processus les plus gourmands actuellement en CPU, et les 3 processus les plus gourmands en RAM (avec leur consommation actuelle, chacun en CPU et en RAM)
