---
title: Correction 3.1 - Cercles et Cylindres
draft: false
weight: 20
---

Dans cet exercice nous allons représenter des objets et calculs géométriques simples en coordonnées entières. Utilisez des annotations de types `: int`, `-> None`, `-> int` et `: Tuples[int ...]` dès que possible. Testez régulièrement la consistance de ces types avec `mypy fichier.py`.

- Implémenter une classe `Cercle` avec comme attributs un rayon `rayon` et les coordonnées `x` et `y` de son centre. Par exemple on pourra instancier un cercle avec `mon_cercle = Cercle(5, (3,1))`

- Dans la classe `Cercle`, implémenter une propriété `aire` dépendante du rayon qu'on peut appeler avec `mon_cercle.aire`.

- Implémenter une classe `Cylindre`, fille de `Cercle`, qui est caractérisée par un rayon `rayon`, une hauteur `hauteur` et des coordonnées `x`, `y` et `z`. On écrira le constructeur de `Cylindre` en appelant le constructeur de `Cercle`.

- Dans la classe `Cercle`, implémenter une méthode `intersect` qui retourne `True` ou `False` suivant si deux cercles se touchent. Exemple d'utilisation : `c1.intersect(c2)`

- Surcharger la méthode `intersect` pour la classe `Cylindre`, en se basant sur le résultat de la méthode de la classe mère.

### Correction

{{% expand "Correction 3.1" %}}

```python
from typing import Tuple
from math import sqrt

class Cercle:

    def __init__(self, rayon: int, coord_centre: Tuple[int, int]=(0,0) ) -> None:
        self.centre: Tuple[int, ...] = coord_centre
        self.r: int = rayon

    @property
    def aire(self) -> float:
        return 3.1415 * self.r * self.r

    def intersect(self, second_cercle) -> bool:
        somme_des_rayons: int = self.r + second_cercle.r
        distance_des_centres: float = sqrt(
            (self.centre[0] - second_cercle.centre[0])**2
            + (self.centre[1] - second_cercle.centre[1])**2
        )
        return somme_des_rayons >= distance_des_centres


class Cylindre(Cercle):

    def __init__(self, rayon: int, hauteur: int, coord_centre: Tuple[int, int, int]=(0, 0, 0) ) -> None:
        super().__init__(rayon)
        self.centre: Tuple[int, ...] = coord_centre
        self.hauteur: int = hauteur

    def intersect(self, second_cylindre) -> bool:
        cercles_intersects: bool = super().intersect(second_cylindre)
        
        somme_des_hauteurs: int = self.hauteur + second_cylindre.hauteur
        distance_z_des_centres: int = abs(self.centre[2] - second_cylindre.centre[2])
        hauteur_intersects: bool = somme_des_hauteurs / 2 >= distance_z_des_centres
        
        return cercles_intersects and hauteur_intersects



mon_cercle = Cercle(5, (3,1))
mon_cylindre = Cylindre(10,8,(-2,-6,-1))

mon_cercle2 = Cercle(5, (10,20))
mon_cylindre2 = Cylindre(5,10,(1,-2,4))

print(mon_cercle.centre)
print(mon_cercle.aire) 
print(mon_cylindre.centre)
print(mon_cylindre.aire)

print(mon_cercle.intersect(mon_cercle2))
print(mon_cylindre.intersect(mon_cylindre2))
```
{{% /expand %}}
