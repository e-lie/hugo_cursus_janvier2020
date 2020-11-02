---
title: 14. Héritage et polymorphisme
draft: false
weight: 20
---


## Héritage

Une classe peut hériter d'une autre pour étendre ses fonctionnalités. Inversement, cela permet de *factoriser* plusieurs classes ayant des fonctionnalités communes.

Par exemple, les **cercles**, les **carrés** et les **étoiles** sont trois types de **figures géométriques**.

En tant que figure géométriques, elles ont toutes un centre, une couleur et une épaisseur utilisés pour le dessin. On peut les déplacer, et on peut calculer leur aire.

- L'héritage permet d'ordonner des objets proches en les apparentant pour s'y retrouver.
- Il permet également de **factoriser** du code en repérant des comportements génériques utilisés dans plusieurs contextes et en les mettant dans un parent commun.


```python
class FigureGeometrique:

    def __init__(self, centre, couleur="black", epaisseur=0.1):
        raise NotImplementedError("La classe fille doit implémenter cette fonction!")
        self.centre = centre
        self.couleur = couleur
        self.epaisseur = epaisseur

    def deplacer(self, dx=0, dy=0):
        self.centre = (self.centre[0]+dx, self.centre[1]+dy)

    def aire(self):
        raise NotImplementedError("La classe fille doit implémenter cette fonction!")

class Cercle(FigureGeometrique):

    def __init__(self, centre, rayon, couleur="black", epaisseur=0.1):
        self.rayon = rayon
        super().__init__(centre, couleur, epaisseur)

    def aire(self):
        return 3.1415 * self.rayon * self.rayon

class Carre(FigureGeometrique):

    def __init__(self, centre, cote, couleur="black", epaisseur=0.1):
        self.cote = cote
        super().__init__(centre, couleur, epaisseur)

    def aire(self):
        return self.cote ** 2


cercle_rouge = Cercle((3, 5), 2, "red")
carre_vert  = Carre((5, -1), 3, "green", epaisseur=0.2)

cercle_rouge.deplacer(dy=2)
carre_vert.deplacer(dx=-3)

print(carre_vert.centre) # -> affiche (2, -1)
print(carre_vert.aire()) # -> affiche 9
```

- Les cercles et les carrés "descendent" ou "héritent" de la classe `FigureGeometrique` avec la syntaxe `class Carre(FigureGeometrique)`.

- La méthode `deplacer` de la classe mère est disponible automatiquement dans les classes filles

Ainsi pour factoriser du code on peut repèrer un comportement commun à plusieurs éléments de notre programme et on créé une classe mère avec une méthode exprimant ce comportement de façon générique. Tous les classes fille pourront utiliser ce comportement. Si on le change plus tard il sera changé dans tout le programme (puissant pour refactoriser le code)

Cependant il est rare qu'un comportement soit exactement identique entre deux classes. On veut souvent changer légèrement ce comportement selon la classe utilisée. Pour cela on utilise le **polymorphisme**.


## Polymorphisme

### Surcharge de fonction

Dans le cas de l'aire de nos figures, chaque figure doit pouvoir calculer son aire mais le calcul est différent pour chaque type de figure concrête.

- On définit une méthode abstraite `aire` dans la classe mère pour indiquer que chaque figure a une méthode aire. Comme une figure en général n'a pas de calcul d'aire la méthode abstraite déclenche une exception (Utilisez ici `NotImplementedError()` qui est faite pour ça)

- On redéfinit la méthode `aire` dans chaque classe fille. La méthode `aire` fille **écrase** ou **surcharge** celle de la classe mère et sera appelée à la place de celle-ci dès qu'on veut l'aire d'une figure géométrique.


### Découper le travail en méthode mère et fille avec `super().methode()`

Souvent on veut quand même utiliser la méthode de la classe même pour faire une partie du travail (commun à toute les classes filles) et ensuite spécialiser le travail en ajoutant des actions suplémentaires dans la méthode fille qui surcharge la méthode mère. A cause de la surcharge la méthode mère n'est pas du tout appelée automatiquement donc il faut le faire "manuellement".

**Exemple ci-dessus:** pour créer un carré:
- on appelle d'abord le constructeur de la classe mère qui initialise `centre`, `couleur` et  `epaisseur` avec `super().__init__()`
- Puis on initialise `cote` qui est un attribut du carré (mais pas du cercle donc pas dans le constructeur général)

De façon générale `super()` renvoie une instance de la classe mère.

### Classe Abstraite

Une classe abstraite est une classe dont on ne peut pas créer d'instance. Elle est simplement là pour définir un modèle minimal que toutes les classes fille doivent suivre (et étendre).

En Python on créé généralement une classe abstraite en levant l'exception `NotImplementedError` dans le constructeur `__init__`. 

### Travailler avec la classe mère

On parle de **polymorphisme** quand on utilise la classe abstraite pour gérer uniformément plusieurs type d'objets de classe différente et qu'on laisse le langage choisir le comportement en fonction du contexte.

Par exemple on peut faire une liste de `FigureGeometrique` de différents types et afficher les aire de chacune. Python devinera automatiquement quelle méthode appeler : 

```python
formes = [Cercle((3, 5), 2, "red"),
          Carre((5, -1), 3, "green"),
          Cercle((-2, 4), 5, "yellow"),
          Carre((4, -2), 2, "purple")]

for forme in formes:
    print(forme.aire())
```

(c.f. aussi [autre exemple sur stack overflow](https://stackoverflow.com/a/3724160))

Le polymorphisme est puissant car il permet d'économiser beaucoup de `if` et autre branchements:

On aurait pu écrire l'exemple précédent avec des `if isinstance(figure, Cercle):` par exemple mais cela aurait été beaucoup moins élégant.

## À retenir

- `class Cercle(FigureGeometrique)` fais hériter `Cercle` de `FigureGeometrique`
- `super().__init__(...)` permet d'appeler *le constructeur de la classe mère*
- Les classes filles disposent des méthodes de la classe mère mais peuvent les **surcharger** (c.f. exemple avec `aire`)
- `super().une_methode(...)` permet d'appeler `une_methode` telle que définie dans la classe mère.
- `isinstance` verifie l'heritage ! `isinstance(cercle_rouge, FigureGeometrique)` vaut `True` !


### Tester la classe pour s'adapter

Souvent pour adapter le comportement d'un programme on veut savoir de quel type est un objet:

-  `isinstance` verifie l'heritage ! `isinstance(cercle_rouge, FigureGeometrique)` vaut `True` !

