---
title: 19. Organiser son code en modules, packages et librairies
draft: false
weight: 20
---

## Modules Python

Les modules Python sont le plus haut niveau d'organisation du code (plus que les classes).

Ils servent à regrouper des ensembles de classes et fonctions apparentées.

Un module est ce qu'on importe grace à `import` ou `from ... import ...`.

### Un module peut être un simple fichier

Si on met des fichiers python dans le même dossier ils constituent automatiquement des modules.

fichier `mon_module.py`:

```python

ma_variable = 1

def ma_fonction(arg: int):
    return ma_variable + arg
```

fichier `mon_module2.py`:

```python
from mon_module import ma_fonction

ma_variable = 2

```

fichier `mon_programme_principal.py`

```python
import mon_module
import mon_module2


if __name__ == "__main__"
    ma_variable = 3
    print(mon_module.ma_variable) # -> 1 
    print(mon_module2.ma_variable) # -> 2
    print(ma_variable) # -> 3
    print(mon_module2.ma_fonction(ma_variable))
```

- Les modules sont des namespaces pour leurs variables : mon_module.ma_variable != mon_module2.mavariables != mavariables

- Les imports de modules sont transitifs : si on importe `module2` qui importe `module1` alors on a `module1` disponible même si on a pas importé directement `module1`.

- Le code d'un module est exécuté au moment de l'import (si ya un print qui traine dans le corps d'un module ça risque de se voir...)

### Packages : quand on a beaucoup de code...

On ne s'y retrouve plus avec un seul module ou quelques fichiers à la racine du projet.

- On met les fichiers dans plusieurs dossiers bien ordonnés

- On ajoute des fichiers `__init__.py` dans chaque sous dossiers et ça fait un module

### Exemple

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

dire_bonjour("Marius") # -> "Bonjour Marius !"

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

bonjour.dire_bonjour("Marius") # -> "Bonjour Marius !"

print(bonjour)
# -> <module 'mylib.bonjour' from 'mylib/bonjour.pyc'>
```

### Faire une librairie 

Si on a besoin de le distribuer ou simplement pour le séparer du reste du code peut ensuite transformer son package en une librairie installable grâce à un outil nommée `setuptools` et/ou `pip`.

Cf. Exercice 4.3