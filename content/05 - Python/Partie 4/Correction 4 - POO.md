---
title: Correction 4 - POO
draft: false
weight: 20
---

## Correction Exercice 4.1

{{% expand "Correction 4.1" %}}

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

## Correction Exercice 4.2

### Une classe Carte pour représenter les éléments d'un jeu

{{% expand "Correction 4.2 `carte.py`" %}}

```python
class InvalidCardColor(ValueError):
    pass


class InvalidCardValue(ValueError):
    pass


class Carte:
    """Représente une carte à jouer classique d'un jeu de 52 cartes"""
    # On utilise ici des tuples car il sont immutables (pas possible d'ajouter des couleurs/valeurs pendant l'exécution)
    # On utilise des noms en majuscule car c'est une convention pour dire que ce sont des constantes
    # On aurait également put utiliser des enums pour représenter les couleurs/valeurs possibles (classique en programmation)
    couleurs_valides = ("TREFLE", "CARREAU", "COEUR", "PIQUE")
    valeurs_valides = tuple(list(range(1,11)) + ["VALET", "DAME", "ROI"])
    
    def __init__(self, valeur, couleur):
        # La bonne méthode pour gérer les valeurs incorrectes en python est généralement de lever une exception
        # (traditionnellement de type ValueError qui est faite pour cela mais une Exception spécifique c'est encore mieux car cela explique l'erreur à l'utilisateur de la classe)
        if valeur not in Carte.valeurs_valides:
            raise InvalidCardValue
        if couleur not in Carte.couleurs_valides:
            raise InvalidCardColor
        self.valeur = valeur
        self.couleur = couleur

    @property
    def points(self):
        return Carte.valeurs_valides.index(self.valeur) + 1
        
    def __repr__(self):
        return f"<Carte {self.valeur} de {self.couleur}>"
        
```

{{% /expand %}}

### Encapsulation et validation des valeurs de carte possibles

{{% expand "Correction 4.2 `carte.py`" %}}

```python
class InvalidCardColor(ValueError):
    pass


class InvalidCardValue(ValueError):
    pass


class Carte:
    """Représente une carte à jouer classique d'un jeu de 52 cartes"""
    # On utilise ici des tuples car il sont immutable (pas possible d'ajouter des couleurs/valeurs pendant l'exécution)
    # On utilise des noms en majuscule car c'est une convention pour dire que ce sont des constantes
    # On aurait également put utiliser des enums pour représenter les couleurs/valeurs possibles (classique en programmation)
    couleurs_valides = ("TREFLE", "CARREAU", "COEUR", "PIQUE")
    valeurs_valides = tuple(list(range(1,11)) + ["VALET", "DAME", "ROI"])
    
    def __init__(self, valeur, couleur):
        # La bonne méthode pour gérer les valeurs incorrectes en python est généralement de lever une exception
        # (traditionnellement de type ValueError qui est faite pour cela mais une Exception spécifique c'est encore mieux car cela explique l'erreur à l'utilisateur de la classe)
        if valeur not in Carte.valeurs_valides:
            raise InvalidCardValue
        if couleur not in Carte.couleurs_valides:
            raise InvalidCardColor
        self._valeur = valeur
        self._couleur = couleur

    @property
    def valeur(self):
        return self._valeur

    @valeur.setter
    def valeur(self, valeur):
        if valeur not in Carte.valeurs_valides:
            raise InvalidCardValue
        self._valeur = valeur

    @property
    def couleur(self):
        return self._couleur

    @couleur.setter
    def couleur(self, couleur):
        if couleur not in Carte.couleurs_valides:
            raise InvalidCardColor
        self._couleur = couleur
`
    @property
    def points(self):
        return Carte.valeurs_valides.index(self.valeur) + 1
        
    def __repr__(self):
        return f"<Carte {self.valeur} de {self.couleur}>"

```
{{% /expand %}}


### La classe Paquet, une collection de cartes

{{% expand "Correction 4.2 `paquet.py`" %}}

```python
from carte import Carte
import random

class Paquet:
    
    def __init__(self):
        
        self.cartes = []
        for valeur in Carte.valeurs_valides:
            for couleur in Carte.couleurs_valides:
                c = Carte(valeur, couleur)
                self.cartes.append(c)
                
    def __repr__(self):
        
        return str(self.cartes)
    
    def melanger(self):
        
        random.shuffle(self.cartes)
        
    def couper(self):
        
        coupe_index = random.randint(0, len(self.cartes))

        self.cartes = self.cartes[coupe_index:] + self.cartes[:coupe_index]


    def piocher(self):
        
        pioche = self.cartes[0]
        self.cartes = self.cartes[1:]
        
        return pioche
    
    def distribuer(self, nb_joueurs, nb_cartes):
        
        distribution = [ [] for i in range(0, nb_joueurs)]
        
        for i in range(0, nb_joueurs):
            for _ in range(0, nb_cartes):
                carte = self.piocher()
                distribution[i].append(carte)
                
        return distribution
        
```

{{% /expand %}}


{{% expand "Correction 4.2 `main.py`" %}}

```python
from carte import Carte

c1 = Carte(3, "PIQUE")
c2 = Carte("DAME", "COEUR")

print(c1)
print(c1.points)

print(c2)
print(c2.points)

#############################

from paquet import Paquet

P = Paquet()

print("\nAvant coupe\n")
print(P)

P.couper()


print("\nApres coupe\n")
print(P)

P.melanger()

print("\nApres melange\n")
print(P)


print(f"Actuellement il y a {len(P.cartes)} cartes dans le paquet")
print(P.piocher())
print(P.piocher())
print(f"Actuellement il y a {len(P.cartes)} cartes dans le paquet")

cartes_alice, cartes_bob = P.distribuer(nb_joueurs=2, nb_cartes=5)
print(cartes_alice)
print(cartes_bob)

```

{{% /expand %}}