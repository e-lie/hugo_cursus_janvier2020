---
title: Correction 4.1 - Des objets géométriques
draft: true
weight: 20
---

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