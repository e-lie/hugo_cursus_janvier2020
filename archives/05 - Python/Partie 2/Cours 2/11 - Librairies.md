---
title: 11. Librairies
draft: false
weight: 22
---

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

## Exemple : `json`

Le JSON est un format de fichier qui permet de décrire des données numériques complexe et imbriquées pour le stocker ou le transférer. Il s'agit du format de données dominant aujourd'hui sur le web. Il est utilisé dans tous les langages et Python intègre à l'installation une librairie pour le manipuler.

A noter également qu'il est quasiment isomorphe à un dictionnaire Python.

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

La fonction principale de la librairie est `loads()` qui tranforme une chaîne de caractère au format JSON en dictionnaire.

```python
import json

# Ouvrir, lire et interpreter un fichier json
with open("applications.json") as f:
    j = json.loads(f.read())


# Trouver l'état de l'application mailman
j["mailman"]["state"]     # -> "working"
```

## Exemple : `requests` pour un besoin web simple (bas niveau)

Envoyer une requête HTTP et récuperer la réponse (et potentiellement le
contenu d'une page).

```python
import requests

r = requests.get("https://en.wikipedia.org/wiki/Python", timeout=30)

print(r.status_code)    # -> 200 si ça a marché
print(r.text)           # -> Le contenu de la page
```

## Exemple : `csv`

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

## Exemple : `sys`

permet d'interagir / de s'interfacer avec le systeme (librairie système commune à toutes les plateforme)

Par exemple:

```python
import sys

sys.stdout   # La sortie standard du programme
sys.path     # Les chemins depuis lesquels sont chargés les imports
sys.argv     # Tableau des arguments passés en ligne de commande
sys.exit(1)  # Sortir du programme avec un code de retour de 1
```

## Exemple : `os`

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

## Exemple : `argparse`

- Du vrai parsing d'argument en ligne de commande
- (Un peu long à initialiser mais puissant)

## Exemple concurrent: `docopt`

Sert à la même chose que argparse mais beaucoup plus rapide à utiliser ! Docopt analyse la documentation du module pour deviner les arguments !

```python
"""Naval Fate.

Usage:
  naval_fate.py ship new <name>...
  naval_fate.py ship <name> move <x> <y> [--speed=<kn>]
  naval_fate.py ship shoot <x> <y>
  naval_fate.py mine (set|remove) <x> <y> [--moored | --drifting]
  naval_fate.py (-h | --help)
  naval_fate.py --version

Options:
  -h --help     Show this screen.
  --version     Show version.
  --speed=<kn>  Speed in knots [default: 10].
  --moored      Moored (anchored) mine.
  --drifting    Drifting mine.

"""
from docopt import docopt


if __name__ == '__main__':
    arguments = docopt(__doc__)
    print(arguments)
```

Ensuite `python naval_fate.py ship new monbateau --speed=15` renvoie un dictionnaire d'arguments du type:

```python
{'--drifting': False,    'mine': False,
 '--help': False,        'move': True,
 '--moored': False,      'new': True,
 '--speed': '15',        'remove': False,
 '--version': False,     'set': False,
 '<name>': ['Guardian'], 'ship': True,
 '<x>': '100',           'shoot': False,
 '<y>': '150'}
```

On peut les utiliser pour paramétrer le programme CLI !

## Exemple : `subprocess`

`subprocess` peut typiquement être utilisé pour lancer des commandes en parallèle du programme principal et 
récupérer leur résultat.

```python
out = subprocess.check_output(["echo", "Hello World!"])
print(out)    # -> Affiche 'Hello World'
```

- `check_output` : recupère la sortie d'une commande
- `check_call` : verifie que la commande a bien marché (code de retour '0') ou declenche une exception
- `Popen` : méthode plus bas niveau

Cf. Partie sur l'execution concurrente en Python


<!-- ## Exemple : `io`

Par exemple, pour créer des objets "file-like". Par exemple :

```python
import io

f = io.StringIO("some initial text data")
print(f.read())    # -> 'some initial text data'
f.seek(0)
f.write("i am writing")
print(f.read())    # -> 'i am writing text data'
``` -->


![](../../../../images/python/moar.jpg)


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


### Gestionnaire de paquet `pip`

- Gestionnaire de paquet / modules Python
- PIP : "Pip Install Packages"
- PyPI : Python Package Index : visitez https://pypi.org

(à ne pas confondre avec Pypy  un interpreter python écrit en Python)


- Installer un paquet :
    - `pip3 install <paquet>`
- Rechercher un paquet :
    - `pip3 search <motclef>`
- Installer une liste de dépendances :
    - `pip3 install -r requirements.txt`
- Lister les paquets installés
    - `pip3 list`, `pip3 freeze`
- Les paquets installés sont dans `/usr/lib/python*/dist-packages/`


### Virtualenv

- Environnement virtuel
- Isoler des paquets / dépendances pour utiliser des versions spécifiques

```bash
# La premiere fois :
sudo apt install python3-virtualenv virtualenv

# Creation d'un virtualenv 'venv'
virtualenv -p python3 venv
source venv/bin/activate

# Installation de dependances
pip3 install <une dependance...>
pip3 install <une autre dependance...>


# On développe, on teste, etc....


# Si on a fini et/ou que l'on veut "sortir" du virtualenv
deactivate
```

Documentation pour toutes les plateformes : https://packaging.python.org/guides/installing-using-pip-and-virtual-environments/

### Outils plus récents Pipenv et Conda

Pip et les virtualenv sont les outils classique pour gérer les dépendances en Python mais il existe également de nouvelles solutions moins classique

- `Pipenv` un outil rassemblant pip et virtualenv pour simplifier le processus de travail.
- `Conda` un gestionnaire de dépendances multiplateforme.

### Installer Pip et Virtualenv sur Windows

- https://matthewhorne.me/how-to-install-python-and-pip-on-windows-10/