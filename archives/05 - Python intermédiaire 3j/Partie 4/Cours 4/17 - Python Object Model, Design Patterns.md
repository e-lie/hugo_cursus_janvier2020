---
title: 17. Python Object Model et sujets avancés
draft: false
weight: 20
---


# Python Object Model 

Si on regarde un autre langage orienté objet avant Python il paraît étrange de mettre `len(collection)` au lieu de `collection.len()` (faire comme s'il s'agissait d'un fonction plutôt que d'une méthode). Cette apparente bizarrerie est la partie émergée d'un iceberg qui, lorsqu'il est bien compris, est la clé de ce qui est pythonique. L'iceberg est appelé le Python Object(ou Data) Model, et il décrit l'API que vous pouvez utiliser pour faire jouer vos propres objets avec les constructions idiomatiques du langage Python. (traduction d'un paragraphe du livre Fluent Python)

Cette API (application programming interface = série de fonctions qui décrivent ce qu'on peut faire) se compose d'attributs et méthodes "spéciales" qui sont encadrées par des doubles underscores (`__` ) comme `__add__`.

## Exemple 1: redéfinir l'addition avec `__add__`

On peut créer une méthode `def __add__(self,  autre_objet_de_la_classe): ...` pour dans nos classe pour redéfinir le symbole `+` appliqué à nos objets.

Exemple un vecteur 2D:

```python
class Vector2d:
    typecode = 'd'

    def __init__(self, x, y):
        self.x = float(x)
        self.y = float(y)

    def __add__(self, autre_vecteur):
        return Vector2d(self.x + autre_vecteur.x, self.y + autre_vecteur.y)

nouveau_vecteur = Vector2d(3, 4) + Vector2d(3, 7) # -> Vector2d(6, 11)
```

On parle aussi dans ce cas de **surcharge d'opérateur** qui est un classique dans les langage de POO.


## Exemple 2: faire de notre objet un conteneur pythonique avec `__setitem__` et `__getitem__`

```python
class MaCollectionEnnuyeuse:
    def __init__(self, collection):
        self.mesitems = list(collection)

    def __getitem__(self, indice):
        return self.mesitems[indice] 

    def __setitem__(self, indice, item_a_ajouter):
        return self.mesitems[indice] = item_a_ajouter

print(MaCollectionEnnuyeuse("Hello")[0:1]) # -> Renvoie 'He'
```

Une fois qu'on a implémenté le minimum de l'interface on peut utiliser des fonctions python intégrées par exemple ici on peut faire directement

```python
shuffle(MaCollectionEnnuyeuse('Diantre')) # -> Mélange les lettres de Diantre 
```

En fait, on peut dire qu'être une liste en python c'est plus ou moins avoir les méthodes spéciales qui définissent la liste. Pareil pour le dictionnaire. Un bon exemple de ce principe est l'itérable : tout objet qui peut renvoyer un iterateur avec `__iter__` est utilisable dans une boucle for (puissant)

## Exemple3 : les iterateurs

En python pour pouvoir utiliser la puissance de la boucle `for` on a besoin d'un objet **itérateur** ou d'un objet **itérable** c'est à dire un objet dont on peut tirer automatiquement un itérateur.

Une liste est itérable, ce qui veut dire qu'elle possède une fonction `__iter__` qui renvoie un itérateur sur ses éléments.

Un itérateur est un objet qui:
   
   - possède une méthode `__next__` qui renvoie l'élément suivant de l'itération
   - possède une méthode `__iter__` qui renvoie un objet itérateur avec lequel continuer l'itération (souvent un simple `return self`)
   - déclenche une exception de type `StopIteration` lorsqu'il n'y a plus d'élément à itérer


## Méthodes spéciales

Il existe plein de méthodes spéciales pour implémenter toutes les syntaxes, comportements sympathiques, et fonctions de base incluses dans Python (comme `shuffle` ou `sort`). Quelques autre:


- `__repr__` et `__str__` : génère automatiquement une représentation de l'objet sous forme de chaîne de caractères (la première est une représentation basique pour le debug, la deuxième prioritaire est pour une représentation plus élégante de l'objet) qui permet de faire un "joli" `print(mon_objet)`

```python
   def __str__(self):
      return "Cercle de couleur " + self.color + " et de rayon " + self.rayon
```

- `__eq__` : définir l'égalité entre deux objets. Très important pour faire des comparaison rapide et par exemple permettre de **trier** automatiquement vos objets dans une liste. Etc

- `__bool__`: Permet de convertir votre objet en booléen et ainsi de supporter des syntaxes comme

```python
if mon_objet:
    print("c'est bon")
else:
    print("c'est pas bon")
```

ETC...

Cf. le livre Fluent Python et [la doc officielle](https://docs.python.org/3/reference/datamodel.html)

Implémenter ces différentes fonctions d'API n'est pas obligation mais surtout utile pour construire du code (souvent de librairie) qui sera agréable à utiliser pour les autre développeurs habitués à Python.

## Design Patterns

En fait au delà de Python et de la POO, lorsqu'on construit des programmes on peut identifier des bonnes façon de résoudre des problèmes courants ou qui on une forme courante qu'on retrouve souvent dans les programmes. On appelle ces méthodes/forme des **Design Patterns**.

Par exemple l'iterateur (Pattern Iterator) est un design pattern que le langage Python implémente à sa façon et qui propose une solution pratique au parcours d'une collection d'objets.

Le Decorator est également un motif pour personnaliser le fonctionnement d'une fonction ou classe sans la modifier (et donc sans complexifier le code principal) il est implémenté en python grace à une syntaxe spécifique du langage très utilisée (Cf juste après).

Ces "motifs de conception" logicielle proviennent d'un ouvrage éponyme, influent dans les années 90, du Gang of Four (Gof). En réalité c'est même plus général que ce livre orienté POO car on peut identifier des Design Patterns dans des langages très différents par exemple fonctionnels.


Il existe pas mal d'autres Patterns non implémentés direactement dans le langage Python:

- https://fr.wikipedia.org/wiki/Patron_de_conception
- https://design-patterns.fr/introduction-aux-design-patterns


## Décorateurs

Les décorateurs sont en Python des sortes d'"emballages" qu'on ajoute aux fonctions et au classes pour personnaliser leur comportement sans modifier le code principal de la fonction. Concrêtement les décorateurs sont des 

En gros ça permet d'ajouter des prétraitements, des posttraitements et de modifier le comportement de la fonction elle

## Programmes asynchrones en Python

Très bonne synthèse pour python >= 3.8 : https://www.integralist.co.uk/posts/python-asyncio/

Une synthèse de la synthèse (Perte d'information ;)) : 

Un programme synchrone est un programme ou toutes les étapes de calculs sont éxecutées les unes à la suite des autres. Conséquence on attend la fin de chaque opération avant de continuer et si une opération prend du temps l'utilisateur attend.

Un programme asynchrone est un programme qui execute diférentes étapes de calcul sans respecter l'ordre linéraire du programme. Par exemple deux fonctions appelées en même temps et qui vont s'exécuter de façon concurrent (on les lance toutes les deux en même temps et elles se partagent les ressources de calculs).

Pour executer des morceaux de calculs de façon concurrente il y a pas mal d'approches dont:

1. le **multiprocessing** : on lance plusieurs processus au niveau de l'os, un peu l'équivalent de plusieurs programme en parallèle. Ils peuvent se répartir les multiples processeurs d'une machine ou d'un cluster. C'est intéressant pour les gros calcul mais pour faire plein de petites taches c'est pas très intéressant car le changement de process prend du temps.

1. le **multithreading** : on lance un processus système avec plusieurs processus "virtuels" "légers" à l'intérieur. Les différents threads peuvent aussi potentiellement utiliser plusieurs processeurs en même temps. Cependant le multithread est peu efficace en python (avec Cpython) à cause du Global Interpreter Lock. On utilise peu les threads.

1. **execution asynchrone dans un seul processus** (asyncio basé sur une **event loop**): En gros les différents morceaux du code concurrents ne s'exécutent pas "réellement" en même temps, ils se partagent le temps d'exécution d'un seul processus de calcul en se passant la main. Cette approche n'utilise pas tous les processeurs disponibles mais est légère et facilement controlable.

### Pourquoi un programme est-il lent ?

Avant de choisir une solution il faut étudier son programme pour diagnostiquer le ralentissement.

- Très couramment à cause de blocages au niveau des entrées/sortie (IO) lorsqu'on attend qu'un serveur (sur le réseau ou autre) ou un device (le disque ou autre) réponde à une demande.
- Parce que le calcul est très lourd et demande plein d'opérations processeur (CPU intensive) (courant mais plus rare dans les programmes réels)


Dans le premier cas il faut utiliser l'execution asynchrone (solution 3.) en coroutine (fonction commençant par `async def`) avec `asyncio`.

Dans le deuxième cas il faut utiliser le multiprocessing (solution 1.) pour maximiser les processeurs utilisés avec `concurrent.futures`.

On peut combiner facilement les deux approches si nécessaire.


### Concrêtement avec des exemples

On commence par essayer d'accélérer son programme avec `asyncio`

Exemple de asyncio:

```python
import asyncio

async def foo():
    print("Foo!")

async def hello_world():
    await foo()  # waits for `foo()` to complete
    print("Hello World!")

asyncio.run(hello_world())
```

Il faut s'habituer à cette façon de programmer :

- se rappeler qu'une fonction `async def` peut se réveille périodiquement pour s'exécuter (le flux d'exécution est plus dur à imaginer)
- Il faut aussi gérer la concurrence entre les coroutines (attendre un résultat dont on a besoin pour continuer le calcul d'une autre coroutine avec `await` par exemple)

Exemple2 avec `gather` pour attendre et rassembler les résultat de plusieurs taches:

```python
gather

import asyncio


async def foo(n):
    await asyncio.sleep(5)  # wait 5s before continuing
    print(f"n: {n}!")


async def main():
    tasks = [foo(1), foo(2), foo(3)]
    await asyncio.gather(*tasks)


asyncio.run(main())
```

Enfin pour compléter l'approche asyncio avec du multiprocessing (au cas ou c'est le processeur qui bloque et que le programme est toujours lent) on peut utiliser `concurrent.futures` et un Pool de Process (`ProcessPoolExecutor`).

Exemple de la doc Python ou on combine `asyncio` et `concurrent.futures`.

```python
import asyncio
import concurrent.futures


def blocking_io():
    # File operations (such as logging) can block the
    # event loop: run them in a thread pool.
    with open("/dev/urandom", "rb") as f:
        return f.read(100)


def cpu_bound():
    # CPU-bound operations will block the event loop:
    # in general it is preferable to run them in a
    # process pool.
    return sum(i * i for i in range(10 ** 7))


async def main():
    loop = asyncio.get_running_loop()

    # 1. Run in the default loop's executor:
    result = await loop.run_in_executor(None, blocking_io)
    print("default thread pool", result)

    # 2. Run in a custom thread pool:
    with concurrent.futures.ThreadPoolExecutor() as pool:
        result = await loop.run_in_executor(pool, blocking_io)
        print("custom thread pool", result)

    # 3. Run in a custom process pool:
    with concurrent.futures.ProcessPoolExecutor() as pool:
        result = await loop.run_in_executor(pool, cpu_bound)
        print("custom process pool", result)


asyncio.run(main())
```