---
title: Cours 2 - Révisions structures de données, librairies, exceptions, ...
draft: false
weight: 20
---


# 8. Structures de données

Les listes et dictionnaires permettent de stocker des séries
d'information...

## 8.1 Les listes

Une collection d'éléments **ordonnés** référencé par un indice

```python
favourite_pokemons = [ "Bulbizarre", "Roucoups", "Insecateur" ]
fibonnaci = [ 1, 1, 2, 3, 5, 8 ]
stuff = [ 3.14, 42, "bidule", ["a", "b", "c"] ]
```


```python
favourite_pokemons = [ "Bulbizarre", "Roucoups", "Insecateur" ]
```


### Accès à element particulier ou a une "tranche"

```python
favourite_pokemons[1]      ->  "Roucoups"
favourite_pokemons[-2:]    ->  ["Roucoups", "Insecateur"]
```


### Longueur

```python
len(favourite_pokemons)    -> 3
```


### Tester qu'un élément est (ou n'est pas) dans une liste

```python
"Insecateur" in favourite_pokemons   # -> True
"Mewtwo" not in favourite_pokemons   # -> True
```


```python
favourite_pokemons = [ "Bulbizarre", "Roucoups", "Insecateur" ]
```


#### Iteration

```python
for pokemon in favourite_pokemons:
    print(pokemon + " est un de mes pokemons préférés !")
```


#### Iteration avec index

```python
print("Voici la liste de mes pokemons préféré:")
for i, pokemon in enumerate(favourite_pokemons):
    print(str(i+1) + " : " + pokemon)
```


```python
favourite_pokemons = [ "Bulbizarre", "Roucoups", "Insecateur" ]
```


#### Modification d'un élément

```python
favourite_pokemons[1] = "Roucarnage"
```

#### Ajout à la suite, contatenation

```python
favourite_pokemons.append("Mewtwo")
```

#### Insertion, concatenation

```python
favourite_pokemons.insert(1, "Papillusion")
favourite_pokemons += ["Pikachu", "Scarabrute"]
```


### Exemple de manip classique : filtrer une liste pour en construire une nouvelle

```python
favourite_pokemons = [ "Bulbizarre", "Roucoups", "Insecateur" ]

# Création d'une liste vide
pokemons_starting_with_B = []

# J'itère sur la liste de pokémons favoris
for pokemon in favourite_pokemons:

   # Si le nom du pokémon actuel commence par B
   if pokemon.startswith("B"):

      # Je l'ajoute à la liste
      pokemons_starting_with_B.append(pokemon)
```


À la fin, `pokemons_starting_with_B` contient:

```python
["Bulbizarre"]
```


#### Transformation de string en liste

```python
"Hello World".split()    -> ["Hello", "World"]
```

#### Transformation de liste en string

```python
' | '.join(["a", "b", "c"])      -> "a | b | c"
```



## 8.2 Les dictionnaires

Une collection **non-ordonnée** (apriori) de **clefs** a qui sont associées des **valeurs**

```python
phone_numbers = { "Alice":   "06 93 28 14 03",
                  "Bob":     "06 84 19 37 47",
                  "Charlie": "04 92 84 92 03"  }
```

### Accès à une valeur

```python
phone_numbers["Charlie"]        -> "04 92 84 92 03"
phone_numbers["Elie"]           -> KeyError !
phone_numbers.get("Elie", None) -> None
```

### Modification d'une entrée, ajout d'une nouvelle entrée

```python
phone_numbers["Charlie"] = "06 25 65 92 83"
phone_numbers["Deborah"] = "07 02 93 84 21"
```


### Tester qu'une clef est dans le dictionnaire

```python
"Elie" in phone_numbers    # -> False
"Bob" not in phone_numbers # -> False
```

```python
phone_numbers = { "Alice":   "06 93 28 14 03",
                  "Bob":     "06 84 19 37 47",
                  "Charlie": "04 92 84 92 03"  }
```

### Iteration sur les clefs

```python
for prenom in phone_numbers:     # Ou plus explicitement: phone_numbers.keys()
    print("Je connais le numéro de "+prenom)
```


### Iteration sur les valeurs

```python
for phone_number in phone_numbers.values():
    print("Quelqu'un a comme numéro " + phone_number)
```


### Iterations sur les clefs et valeurs

```python
for prenom, phone_number in phone_numbers.items():
    print("Le numéro de " + prenom + " est " + phone_number)
```


## 8.3 Construction plus complexes

Liste de liste, liste de dict, dict de liste, dict de liste, ...

```python
contacts = { "Alice":  { "phone": "06 93 28 14 03",
                         "email": "alice@megacorp.eu" },

             "Bob":    { "phone": "06 84 19 37 47",
                         "email": "bob.peterson@havard.edu.uk" },

             "Charlie": { "phone": "04 92 84 92 03" } }
```


```python
contacts = { "Alice":  { "phone": "06 93 28 14 03",
                         "email": "alice@megacorp.eu" },

             "Bob":    { "phone": "06 84 19 37 47",
                         "email": "bob.peterson@harvard.edu.uk" },

             "Charlie": { "phone": "04 92 84 92 03" } }
```


### Recuperer le numero de Bob

```python
contacts["Bob"]["phone"]   # -> "06 84 19 37 47"
```


### Ajouter l'email de Charlie

```python
contacts["Charlie"]["email"] = "charlie@orange.fr"
```


### Ajouter Deborah avec juste une adresse mail

```python
contacts["Deborah"] = {"email": "deb@hotmail.fr"}
```



## 8.3 Les sets

Les `set`s sont des collections d'éléments **unique** et **non-ordonnée**

```python
chat = set(["c", "h", "a", "t"])        # -> {'h', 'c', 'a', 't'}
chien = set(["c", "h", "i", "e", "n")   # -> {'c', 'e', 'i', 'n', 'h'}
chat - chien                            # -> {'a', 't'}
chien - chat                            # -> {'i', 'n', 'e'}
chat & chien                            # -> {'h', 'c'}
chat | chien                            # -> {'c', 't', 'e', 'a', 'i', 'n', 'h'}
chat.add("z")                           # ajoute `z` à `chat`
```



## 8.4 Les tuples

Les tuples permettent de stocker des données de manière similaire à une liste, mais de manière **non-mutable**.
Generalement itérer sur un tuple n'a pas vraiment de sens...

Les tuples permettent de **grouper des informations ensembles**.
Typiquement : des coordonnées de point.

```python
xyz = (2,3,5)
xyz[0]        # -> 2
xyz[1]        # -> 3
xyz[0] = 5    # -> Erreur!
```

Autre exemple `dictionnaire.items()` renvoie une liste de tuple `(clef, valeur)` :

```python
[ (clef1, valeur1), (clef2, valeur2), ... ]
```



## 8.5 List/dict comprehensions

Les "list/dict comprehensions" sont des syntaxes particulière permettant de rapidement construire des listes (ou dictionnaires) à partir d'autres structures.

### Syntaxe (list comprehension)

```python
[ new_e for e in liste if condition(e) ]
```

### Exemple (list comprehension)

Carré des entiers impairs d'une liste

```python
[ e**2 for e in liste if e % 2 == 1 ]
```


## 8.5 List/dict comprehensions

Les "list/dict comprehensions" sont des syntaxes particulière permettant de rapidement construire des listes (ou dictionnaires) à partir d'autres structures.

### Syntaxe (dict comprehension)

```python
{ new_k:new_v for k, v in d.items() if condition(k, v) }
```

### Exemple (dict comprehension)

Carré des entiers impairs d'une liste

```python
{ nom: age-20 for nom, age in ages.items() if age >= 20 }
```

## 8.6 Générateurs

(Pas vraiment une structure de données, mais c'est lié aux boucles ...)

- Une fonction qui renvoie **des** résultats "au fur et à mesure" qu'ils sont demandés ...
- Se comporte comme un itérateur
- Peut ne jamais s'arrêter ...!
- Typiquement, évite de créer des listes intermédiaires


## exemple SANS generateur

```python
mes_pokemons = { "Bulbizarre": 12,    "Pikachu": 25,
                 "Rattata": 15,       "Rondoudou": 23
                 # [...]
               }

def au_moins_niveau_20(pokemons):

    output = []
    for pokemon, niveau in pokemons.items():
        if niveau >= 20:
            output.append(pokemon)

    return output

for pokemon in au_moins_niveau_20(mes_pokemons):
   ...
```

## exemple AVEC generateur

```python
mes_pokemons = { "Bulbizarre": 12,    "Pikachu": 25,
                 "Rattata": 15,       "Rondoudou": 23
                 # [...]
               }

def au_moins_niveau_20(pokemons):

    for pokemon, niveau in pokemons.items():
        if niveau >= 20:
            yield pokemon

for pokemon in au_moins_niveau_20(mes_pokemons):
   ...
```

Il n'est pas nécessaire de créer la liste intermédiaire `output`


## Un autre exemple

```python
def factorielle():

   n = 1
   acc = 1

   while True:
       acc *= n
       n += 1

       yield acc
```



# Recap'

## Programmation impérative / procédurale

- Comme une recette de cuisine qui manipule de l'information
- Une suite d'opération à effectuer
- Différents concepts pour construire ces opérations:
    - des variables
    - des fonctions
    - des conditions
    - des boucles
    - des structures de données (listes, dictionnaires)


# Recap'

## Variables

```python
x = "Toto"
x = 40
y = x + 2
print("y contient " + str(y))
```


# Recap'

## Fonctions

```python
def aire_triangle(base, hauteur):
    calcul = base * hauteur / 2
    return calcul

A1 = aire_triangle(3, 5)      # -> A1 vaut 15 !
A2 = aire_triangle(4, 2)      # -> A2 vaut 8 !
```

- Indentation
- Arguments (peuvent être optionnels si on spécifie une valeur par défaut)
- Variables locales
- `return` pour pouvoir récupérer un résultat depuis l'extérieur
- Appel de fonction


## Conditions

```python
def aire_triangle(base, hauteur):

    if base < 0 or hauteur < 0:
        print("Il faut donner des valeurs positives!")
        return -1

    calcul = base * hauteur / 2
    return calcul
```

- Indentation
- Opérateurs (`==`, `!=`, `<=`, `>=`, `and`, `or`, `not`, `in`, ...)
- Mot clefs `if`, `elif`, `else`


## Listes, dictionnaires et boucles

```python
breakfast = ["Spam", "Eggs", "Bacon", "Spam"]
breakfast.append("Coffee")

print("Au petit dej' je mange: ")
for stuff in breakfast:
    print(stuff)
```


```python
ingredients_gateau = {"farine": 200,
                      "beurre": 100,
                      "chocolat": 150}

for ingredient, qty in ingredients_gateau.items():
    print("J'ai besoin de " + str(qty) + "g de " + ingredient)
```

## Algorithmes simples : `max`

```python
def max(liste_entiers):
    if liste_entiers == []:
        print("Erreur, peut pas calculer le max d'une liste vide")
        return None

    m = liste_entiers[0]
    for entier in liste_entiers:
        if m < entier:
            m = entier

    return m
```

## Algorithmes simples : filtrer une liste

```python
def pairs(liste_entiers):

    resultat = []

    for entier in liste_entiers:
        if entier % 2 == 0:
            resultat.append(entier)

    return resultat
```






# 9. Fichiers



## 9.0 Lire "brutalement"

```python
f = open("/etc/passwd", "r")
contenu_du_fichier = f.readlines()
f.close()

for ligne in contenu_du_fichier:
    print(ligne)
```

Attention à bien distinguer:
- le nom du fichier (`passwd`) et son chemin d'accès absolu (`/etc/passwd`)
- le vrai fichier qui existe sur le disque
- la variable / objet Python (dans l'exemple, nommée `f`) qui est une interface pour interagir avec ce fichier


## 9.1 Lire, avec une "gestion de contexte"

```python
with open("/etc/passwd", "r") as f:
    contenu_du_fichier = f.readlines()

for ligne in contenu_du_fichier:
    print(ligne)
```

### Explications

- `open("fichier", "r")` ouvre un fichier en lecture
- `with ... as ...` ouvre un contexte, à la fin duquel le fichier sera fermé automatiquement
- `f.readlines()` permet d'obtenir une liste de toutes les lignes du fichier


## 9.1 Lire

- `f.readlines()` renvoie une **liste** contenant les lignes une par une
- `f.read()` renvoie une (grande) **chaĩne** contenant toutes les lignes concaténées

- Attention, si je modifie la variable `contenu_du_fichier` ... je ne modifie pas vraiment le fichier sur le disque ! Pour cela, il faut explicitement demander à *écrire* dans le fichier.


## 9.2 Ecrire

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


## 9.3 Fichiers et exceptions

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


## 9.4 Note "technique" sur la lecture des fichiers

- Il y a un "curseur de lecture". On peut lire petit morceaux par petit morceaux ... une fois arrivé au bout, il n'y a plus rien à lire, il faut replacer le curseur si on veut de nouveau lire.

```python
f = open("/etc/passwd")
print(f.read())  # ---> Tout plein de choses
print(f.read())  # ---> Rien !
f.seek(0)        # On remet le curseur au début
print(f.read())  # ---> Tout plein de choses !
```


# 10. Librairies

L'une des puissances de python vient de l'écosystème de librairie disponibles.

Librairie / bibliothèque / module : un ensemble de fonctionnalité déjà pensés et
éprouvées, prêtes à l'emploi.

### Syntaxes d'import

```python
import un_module          # -> Importer tout un module
un_module.une_fonction()  # -> Appeler la fonction une_function()
                          #    du module
```

### Exemple

```python
import math

math.sqrt(2)   # -> 1.4142135623730951
```

### Importer juste des choses précises

```python
from un_module import une_fonction, une_autre

une_fonction(...)
```

### Exemple

```python
from math import sqrt, sin, cos

sqrt(2)   # -> 1.4142135623730951
```

## 10.1 Exemple : `json`

```python
{
    "mailman": {
        "branch": "master",
        "level": 2,
        "state": "working",
        "url": "https://github.com/yunohost-apps/mailman_ynh",
        "flags": [ "mailing-list", "lightweight" ]
    },
    "mastodon": {
        "branch": "master",
        "level": 3,
        "state": "inprogress",
        "url": "https://github.com/YunoHost-Apps/mastodon_ynh",
        "flags": [ "social network", "good-UX" ]
    }
}
```

## 10.1 Exemple : `json`

```python
import json

# Ouvrir, lire et interpreter un fichier json
with open("applications.json") as f:
    j = json.loads(f.read())


# Trouver l'état de l'application mailman
j["mailman"]["state"]     # -> "working"
```


## 10.2 Exemple : `requests`

Envoyer une requête HTTP et récuperer la réponse (et potentiellement le
contenu d'une page)

```python
import requests

r = requests.get("https://en.wikipedia.org/wiki/Python", timeout=30)

print(r.status_code)    # -> 200 si ça a marché
print(r.text)           # -> Le contenu de la page
```

## 10.3 Exemple : `csv`

```python
import csv

# Ouvrir et lire les lignes d'un fichier csv
with open("table.csv") as f:
    table = csv.reader(f, delimiter='|')
    for row in table:
        print(row[1]) # Afficher le 2eme champ
        print(row[3]) # Afficher le 4eme champ

with open("newtable.csv", "w") as f:
    newtable = csv.write(f, delimiter=",")
    newtable.writerow(["Alice", 32, "Lyon"])
    newtable.writerow(["Bob", 29, "Bordeaux"])
```

## 10.4 Exemple : `os`

`os` permet d'interagir avec le système d'exploitation pour réaliser différent
type d'action... Certaines étant spécifiques à l'OS en question (Linux, Windows,
...)

Quelques exemples :

```python
import os
os.listdir("/etc/")            # Liste les fichiers dans /etc/
os.path.join("/etc", "passwd") # Génère un chemin à partir de plusieurs parties
os.system("touch /etc/toto")   # (à éviter) Execute une commande "brute"
```

Voir aussi : copie ou suppression de fichiers, modification des permissions, ...

## 10.5 Exemple : `sys`

permet d'interagir / de s'interfacer avec le systeme

Par exemple:

```python
import sys

sys.stdout   # La sortie standard du programme
sys.path     # Les chemins depuis lesquels sont chargés les imports
sys.argv     # Tableau des arguments passés en ligne de commande
sys.exit(1)  # Sortir du programme avec un code de retour de 1
```

## 10.6 Exemple : `argparse`

- Du vrai parsing d'argument en ligne de commande
- (Un peu long à initialiser mais puissant)

## 10.7 Exemple : `subprocess`

`subprocess` peut typiquement être utiliser pour lancer des commandes et
récupérer leur résultat

```python
out = subprocess.check_output(["echo", "Hello World!"])
print(out)    # -> Affiche 'Hello World'
```

- `check_output` : recupère la sortie d'une commande
- `check_call` : verifie que la commande a bien marché (code de retour '0') ou declenche une exception
- `Popen` : méthode plus bas niveau


## 10.8 Exemple : `io`

Par exemple, pour créer des objets "file-like". Par exemple :

```python
import io

f = io.StringIO("some initial text data")
print(f.read())    # -> 'some initial text data'
f.seek(0)
f.write("i am writing")
print(f.read())    # -> 'i am writing text data'
```


![](../../images/moar.jpg)


## Moar ?

- Debian packages : `python-*`
- Python package manager : `pip`

## Exemples

- JSON, XML, HTML, YAML, ...
- Regular expressions
- Logging, Parsing d'options, ...
- Internationalisation
- Templating
- Plots, LDAP, ...


## 10.7 `pip`

- Gestionnaire de paquet / modules Python
- PIP : "Pip Install Packages"
- PyPI : Python Package Index
- Installer un paquet :
    - `pip3 install <paquet>`
- Rechercher un paquet :
    - `pip3 search <motclef>`
- Installer une liste de dépendances :
    - `pip3 install -r requirements.txt`
- Lister les paquets installés
    - `pip3 list`, `pip3 freeze`
- Les paquets installés sont dans `/usr/lib/python*/dist-packages/`

## 10.8 Ecrire ses propres modules

Considérant les fichiers suivants :

```bash
├── main.py
└── mylib/
    ├── __init__.py
    └── bonjour.py      # <-- Contient "def dire_bonjour..."
```

Depuis `main.py`, je peux faire

```python
from mylib.bonjour import dire_bonjour

dire_bonjour("Alex") # -> "Bonjour Alex !"

print(dire_bonjour)
# -> <function dire_bonjour at 0x7fb964fab668>
```

Considérant les fichiers suivants :

```bash
├── main.py
└── mylib/
    ├── __init__.py
    └── bonjour.py      # <-- Contient "def dire_bonjour..."
```

Depuis `main.py`, je peux *aussi* faire

```python
from mylib import bonjour

bonjour.dire_bonjour("Alex") # -> "Bonjour Alex !"

print(bonjour)
# -> <module 'mylib.bonjour' from 'mylib/bonjour.pyc'>
```

# Exercices complementaires

- Utiliser la librairie random pour simuler 100 lancés de dé 6 et calculer dans un dictionnaire la frequence de chaque face obtenue
- Dans l'exo precedent, utiliser argparse pour gerer via des arguments en ligne de commande :
    - le nombre de lancés (par ex. -n 30)
    - afficer le nombre de lancés bruts (--raw) ou bien la frequence (--freq)
- Charger deux dates en utlisant la librairie datetime et calculer le "timedelta" en jours
   - "2019-08-25T00:07:46Z"
   - "2016-05-17T12:06:54Z"

# 11. Outils et bonnes pratiques

# Documentation

Pour les librairies (et Python en général) :
- `docs.python.org`
- `devdocs.io`
- stack overflow ...
- doc strings !!

Pour votre code :
- nom de variables, fonctions, argument !!!
- commentaires, doc strings
- gestionnaire de version
- generation de doc automatique ?

# Faire du "bon code"

**La lisibilité est la priorité numéro 1**

Un programme est vivant et évolue. Mieux vaut un programme cassé mais lisible (donc débuggable) qu'un programme qui marche mais incompréhensible (donc fragile et/ou qu'on ne saura pas faire évoluer)

(c.f. Guido van Rossum chez Dropbox)

Autrement dit : **la lisibilité pour vous et vos collègues a énormément d'importance pour la maintenabilité et l'évolution du projet**


# Lisibilité, "bon code"

- **Keep It Simple**
- **Sémantique** : utiliser des noms de variables et de fonctions concis et pertinents
- **Commentaires** : *lorsque c'est nécessaire*, pour démystifier ce qu'il se passe
- **Modularité** : découper son programme en fonctions qui chacune résolvent un sous-problème
- **Couplage faible** : garder ses fonctions autant que possibles indépendantes, limiter les effets de bords
- **Prendre le temps de refactoriser** quand nécessaire
    - si je répète plusieurs fois les mémes opérations, peut-être définir une nouvelle fonction
    - si le contenu d'une variable ou d'une fonction change, peut-être changer son nom
- **Ne pas abuser** des principes précédents
    - trop d'abstractions tue l'abstraction
    - tout ça viens avec le temps et l'expérience


# Lisibilité, "bon code"

[How to write good code](https://xkcd.lapin.org/strips/844Code%20correct.png)


# Conventions de nommages des variables, fonctions et classes

Variables et fonctions en snake case : `nom_de_ma_variable`

Constantes globales en macro case: `NOM_DE_MA_CONSTANTE`

Nom de classes en upper camel case : `NomDeMaClasse`


# Syntaxe, PEP8, linters

- Le style d'écriture de python est standardisé via la norme PEP8
- Il existe des "linter" pour détecter le non-respect des conventions (et également certaines erreurs logiques)
    - Par exemple `flake8`, `pylint`
- Intégration possible dans `vim` et autres IDE...
- `autopep8` ou `black` permettent de corriger un bon nombre de problème automatiquement


# Faire des vrais tests avec pytest

Dans mylib.py 
```
def func(x):
    return x + 1
```

Dans tests.py
```
from mylib import func

def test_answer():
    assert func(3) == 5
```

puis lancer: `pytest tests.py`

`pytest` considere comme des tests toutes les fonctions qui commencent par `test_`



