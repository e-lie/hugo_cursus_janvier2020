---
title: Exercice 4.3 - fancy operations - Packages, scripts et tests
draft: false
weight: 22
---

## 4.3.1 Créer un script avec des paramètres documentés grâce à `docopt`

Le point de départ des exercices 4.3 à 4.5 est une librairie de calcul extrêment simple ennuyeuse puisqu'elle fournit des fonctions `fancy_add`, `fancy_substract` et `fancy_product`. Pour illustrer la réutilisation du code et des bonnes pratiques de développement, nous allons cependant la packager et l'utiliser pour contruire un outil de calcul en ligne de commande, et un autre basé sur une application web (`cli_calculator.py` et `web_calculator`).

- Récupérez avec `git clone` le projet de base à l'adresse https://github.com/e-lie/python202011-exercice-fancy-ops.git. Ouvrez le dans VSCode.

- Créez un environnement virtuel python3 dans un dossier `venv` pour travailler de façon isolée des autres projets et de l'environnement python du système: `virtualenv -p python3 venv`.

- Activez l'environnement dans votre terminal courant : `source ./venv/bin/activate` (`deactivate` pour desactiver l'environnement).

- Observer les fonctions de calculs présentes dans `fancy_operations.py`. Créez un script `cli_calculator.py` qui importe ces trois fonctions et les utilise pour faire des calculs simples.

- Essayez de debugger le script dans VSCode (normalement la configuration de debug est déjà présente dans car fournit dans le fichier `.vscode/launch.json` du projet).

- Installons la librairie externe docopt dans notre environnement virtuel:
    - Ajoutez `docopt` à un fichier `requirements.txt` à la racine du projet.
    - Installez cette dépendance grâce au gestionnaire de paquet `pip` : `pip install -r requirements.txt` (vérifiez bien que votre venv est activé avec `source venv/bin/activate`).

- En vous inspirant du cours et de la documentation de `docopt` utilisez cette librairie pour faire en sorte que `cli_calculator listops` affiche la liste des operations disponibles dans `fancy_operations.py`. On pourra pour cela ajouter dans `fancy_operations.py` un dictionnaire `fancy_operations` répertoriant les operations au format `{ 'add': fancy_add, ... }`.

## 4.3.2 Déplacer les fonctions de calcul dans un package de librairie

Pour ajouter une nouvelle classe `vector2d` à notre librairie nous allons la réorganiser en plusieurs fichiers et sous dossiers.

- Créez un dossier `computation_libs` pour la librairie à la racine du projet. À l'intérieur créer un sous dossier `fancy_int_operations` pour ranger nos fonctions.

- Déplacez et rangez les fonctions `fancy_add`, `fancy_product` et le dictionnaire `fancy_operations` à la racine de `fancy_int_operations` dans un fichier `__init__.py` de façon à pouvoir les importer dans `cli_calculator.py`  sous la forme `from computation_libs.fancy_int_operations import fancy_add, fancy_product, fancy_operations`.

- Déplacez de même `fancy_substract` de façon à pouvoir l'importer comme suit : `from computation_libs.fancy_int_operations.more_fancy_operations import fancy_substract`.

- Vérifiez que votre script `cli_calculator.py` fonctionne toujours.

- Ajoutez finalement la classe `Vector2d` suivante dans un fichier `computation_libs/vector2d.py`:

#### vector2d
{{% expand "`computation_libs/vector2d.py`" %}}

```python
from array import array
import math

class Vector2d:
    typecode = 'd'

    def __init__(self, x, y):
        self.__x = float(x)
        self.__y = float(y)

    @property
    def x(self):
        return self.__x

    @property
    def y(self):
        return self.__y

    def __iter__(self):
        return (i for i in (self.x, self.y))

    def __repr__(self):
        class_name = type(self).__name__
        return '{}({!r}, {!r})'.format(class_name, *self)

    def __str__(self):
        return str(tuple(self))

    def __bytes__(self):
        return (bytes([ord(self.typecode)]) +
                bytes(array(self.typecode, self)))

    def __eq__(self, other):
        return tuple(self) == tuple(other)

    def __hash__(self):
        return hash(self.x) ^ hash(self.y)

    def __abs__(self):
        return math.hypot(self.x, self.y)

    def __bool__(self):
        return bool(abs(self))

    def angle(self):
        return math.atan2(self.y, self.x)

    def __format__(self, fmt_spec=''):
        if fmt_spec.endswith('p'):
            fmt_spec = fmt_spec[:-1]
            coords = (abs(self), self.angle())
            outer_fmt = '<{}, {}>'
        else:
            coords = self
            outer_fmt = '({}, {})'
        components = (format(c, fmt_spec) for c in coords)
        return outer_fmt.format(*components)

    @classmethod
    def frombytes(cls, octets):
        typecode = chr(octets[0])
        memv = memoryview(octets[1:]).cast(typecode)
        return cls(*memv)


if __name__ == "__main__":
    
    v1 = Vector2d(3, 4)
    print(v1.x, v1.y)
    x, y = v1
    x, y
    v1
    v1_clone = eval(repr(v1))
    v1 == v1_clone
    print(v1)
    octets = bytes(v1)
    octets
    abs(v1)
    bool(v1), bool(Vector2d(0, 0))

    # Test of ``.frombytes()`` class method:

    v1_clone = Vector2d.frombytes(bytes(v1))
    v1_clone
    v1 == v1_clone

    # Tests of ``format()`` with Cartesian coordinates:

    format(v1)
    format(v1, '.2f')
    format(v1, '.3e')

    # Tests of the ``angle`` method::

    Vector2d(0, 0).angle()
    Vector2d(1, 0).angle()
    epsilon = 10**-8
    abs(Vector2d(0, 1).angle() - math.pi/2) < epsilon
    abs(Vector2d(1, 1).angle() - math.pi/4) < epsilon

    # Tests of ``format()`` with polar coordinates:

    format(Vector2d(1, 1), 'p')  # doctest:+ELLIPSIS
    format(Vector2d(1, 1), '.3ep')
    format(Vector2d(1, 1), '0.5fp')

    # Tests of `x` and `y` read-only properties:

    v1.x, v1.y
    # v1.x = 123 # -> raises AttributeError !

    # Tests of hashing:

    v1 = Vector2d(3, 4)
    v2 = Vector2d(3.1, 4.2)
    hash(v1), hash(v2)
    len(set([v1, v2]))
```

{{% /expand %}}


- Documentez cette classe grâce à un doctype contenant le texte suivant `A 2-dimensional vector class from the fluent python book chapter 9`.

## 4.3.3 Finir cli_calculator

- Ajoutez dans `cli_calculator.py` un deuxième cas d'usage docopt permettant d'appeler le script pour effectuer une operation comme suit: `python3 cli_calculator.py substract 3 4` affichera `3 - 4 = -1`. On pourra préciser le symbole `-, +, *` en complexifiant le dictionnaire `fancy_operations` pour indiquer le symbole correspondant à chaque opération.

- Gérer les mauvaises entrées utilisateurs grâce à un `try: ... except:`. On pourra afficher un message d'erreur tel que `Bad operation or operand (should be integers)` et finir le script en erreur grâce à `exit(1)`.

## 4.3.4 Créer un package python d'application web : web_calculator

- Ajoutez la librairie web `flask` aux dépendances du projet et installez la avec pip.

- Créez un package `web_app` initialisant une application flask quand on l'importe avec le code :

```python
from flask import Flask

web_app = Flask(__name__)
```