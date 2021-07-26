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

10.1 : Créer un fonction `liste_users` qui lit le fichier `/etc/passwd` et retourne la liste des utilisateurs ayant comme shell de login `/bin/bash`.

10.2 : Dans le code Python, écrire un modèle d'email comme:

```python
modele = """
Bonjour {prenom} !
Voici en pièce jointe les billets pour votre voyage en train vers {destination}.
"""
```

Ecrire une fonction `generer_email` qui remplace dans `modele` les chaines `{prenom}`et `{destination}` par des arguments fourni à la fonction, et enregistre le résultat dans un fichier `email_{prenom}.txt`. Par exemple, `generer_email("Alex", "Strasbourg")` générera le texte et sauvegardera le résultat dans `email_Alex.txt`.

10.3 : Écrire une fonction qui permet d'afficher un fichier sans les commentaires et les lignes vides. Spécifier le caractère qui symbolise le début d'un commentaire en argument de la fonction. (Ou pourra utiliser la méthode `strip()` des chaînes de caractère pour identifier plus facilement les lignes vides)
