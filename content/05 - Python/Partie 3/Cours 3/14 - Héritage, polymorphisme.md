---
title: 14. Héritage et polymorphisme
draft: false
weight: 20
---


# Héritage

Une classe peut hériter d'une autre pour étendre ses fonctionnalités. Inversement, cela permet de *factoriser* plusieurs classes ayant des fonctionnalités communes.

Par exemple, les **cercles**, les **carrés** et les **étoiles** sont trois types de **figures géométriques**.

En tant que figure géométriques, elles ont toutes un centre, une couleur et une épaisseur utilisés pour le dessin. On peut les déplacer, et on peut calculer leur aire.



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







```python
class FigureGeometrique:

    # def __init__ ...

    def deplacer(self, dx=0, dy=0):
        self.centre = (self.centre[0]+dx, self.centre[1]+dy)

  

class Cercle(FigureGeometrique):

    # def __init__ ...

    def aire(self):
        return 3.1415 * self.rayon * self.rayon

class Carre(FigureGeometrique):

    # def __init__ ...
```



### Polymorphisme 

```python
formes = [Cercle((3, 5), 2, "red"),
          Carre((5, -1), 3, "green"),
          Cercle((-2, 4), 5, "yellow"),
          Carre((4, -2), 2, "purple")]

for forme in formes:
    print(forme.aire())
```

(c.f. aussi [autre exemple sur stack overflow](https://stackoverflow.com/a/3724160))



## À retenir

- `class Cercle(FigureGeometrique)` fais hériter `Cercle` de `FigureGeometrique`
- `super().__init__(...)` permet d'appeler *le constructeur de la classe mère*
- Les classes filles disposent des méthodes de la classe mère mais peuvent les **surcharger** (c.f. exemple avec `aire`)
- `super().une_methode(...)` permet d'appeler `une_methode` telle que définie dans la classe mère.
- `isinstance` verifie l'heritage ! `isinstance(cercle_rouge, FigureGeometrique)` vaut `True` !

