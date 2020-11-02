---
title: 0. Setup de développement Python
draft: false
weight: 20
---

Notre premier outil développement est bien sur l'interpréteur `python` lui même utilisé pour lancer un fichier de code.

## Installation

- Sur linux installer le paquet `python3` (généralement déjà installé parce que linux utilise beaucoup python)
- Sur Windows installer depuis `python.org` ou depuis un outil comme chocolatey
- Sur MacOs déjà installé mais pour gérer un version plus à jours on peut le faire manuellement depuis python.org ou avec homebrew.


## Python 2 vs Python 3

- Python 2 existe depuis 2000
- Python 3 existe depuis 2008
- Fin de vie de Python 2 en 2020
- ... mais encore la version par défaut dans de nombreux système ... (c.f. `python --version`)

Généralement il faut lancer `python3` explicitement ! (et non `python`) pour utiliser python3

### Différences principales

- `print "toto"` ne fonctionnera pas en Python 3 (utiliser `print("toto")`
- Nommage des paquets debian (`python-*` vs `python3-*`)
- Gestion de l'encodage
- `range`, `xrange`
- Disponibilité des librairies ?

Il existe des outils comme 2to3 pour ~automatiser la transition.

### Executer un script explicitement avec python

```bash
$ python3 hello.py
```

### ou implicitement (shebang)

```python
#!/usr/bin/env python3

print("Hello, world!")
```

puis on rend le fichier executable et on l'execute

```bash
$ chmod +x hello.py
$ ./hello.py
```


### En interactif

```bash
$ python3
>>> print("Hello, world!")
```

### `ipython3` : alternative à la console python 'classique'

```bash
$ sudo apt install ipython3
$ ipython3
In [1]: print("Hello, world!")
```

#### Principaux avantages:

- Complétion des noms de variables et de modules avec TAB
- Coloré pour la lisibilité
- Plus explicite parfois
- des commandes magiques comme `%cd`, `%run script.py`, 

#### Inconvénients:

- Moins standard
- à installer en plus de l'interpréteur python.

##### pour quitter : `exit`

## Les éditeurs de code

VSCode est un éditeur de code récent et très à la mode, pour de bonnes raisons:
- Il est simple ou départ et fortement extensible: à l'installation seules les fonctionnalités de base sont disponibles
    - Éditeur de code avec coloration et raccourcis pratiques
    - Navigateur de fichier (pour manipuler une grande quantité de fichers et sous dossier sans sortir de l'éditeur)
    - Recherche et remplacement flexible avec des expressions régulières (très important pour trouver ce qu'on cherche et faire de refactoring)
    - Terminal intégrée (On a plein d'outils de développement à utiliser dans le terminal)
    - Une interface git assez simple très bien faite (git on s'y perd facilement, une bonne interface aide à s'y retrouver)

Indépendamment du logiciel choisi on trouve en général toutes ces fonctionnalités dans un éditeur de code.

### Observons un peu tout ça avec une démo de VSCode et récapitulons l'importance des ces fonctions.

## Installer des extensions pertinentes

Au sein de l'éditeur nous voulons coder en Python et également:
- Pouvoir détecter les erreurs de syntaxe.
- Pouvoir explorer le code python réparti dans plusieurs fichiers (sauter à la définition d'une fonction par exemple).
- Complétion automatique des noms de symboles (ça peut être pénible parfois).
- Pouvoir debugger le code python de façon agréable.
- Pouvoir refactorer (changer le nom de variables ou fonctions partout automatiquement).

Installez l'extension `Python` (et affichez la documentation si vous êtes curieux) en allant dans la section `Extensions` (Icone de gauche avec 4 carrés dont un détaché)

Nous allons également utiliser git sérieusement donc nous allons installer une super extension git appelée `Gitgraph` pour pouvoir mieux explorer l'historique d'un dépôt git.

Enfin vous pouvez installer d'autres extensions pour personnaliser l'éditeur comme l'extension VIM si vous aimez habituellement utiliser cet éditeur.

### Opensource et extensibilité : ne pas s'enfermer dans un environnement de travail

- VSCode est développé par Microsoft et partiellement opensource (Le principal code est accessible mais pas tout)
- VSCodium est la version opensource communautaire de VSCode mais certaines fonctions puissantes et pratiques sont seulement dans VSCode (les environement distant Docker et SSH par exemple)
- Un fork récent et complètement opensource de VSCode qui peut fonctionner directement dans le navigateur (Cf. gitpod.io). Moins mature.

Ces trois logiciels sont très proches et vous pouvez coder vos extensions (compatibles avec les 3) pour étendre ces éditeur.

Il me semble important pour choisir un outil de se demander si on possède l'outil ou si l'outil nous possède (plus ou moins les deux en général). Pour pouvoir gérér la complexité du développement moderne on dépend de pas mal d'outils. Savoir choisir des outils ouverts et savoir utiliser également les outils en ligne commande (`git`, `pylint`, etc cf. suite du cours) est très important pour ne pas s'enfermer dans un environnement limitant et possessif.