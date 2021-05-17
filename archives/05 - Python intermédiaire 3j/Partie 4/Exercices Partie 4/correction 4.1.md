---
title: Correction 4.1 - Un paquet pythonique
draft: false
weight: 30
---


## 4.1 Utiliser les syntaxes de liste sur la classe `Paquet`

- Plutôt que d'utiliser `len(mon_paquet.cartes)` pour avoir le nombre de carte on voudrait utiliser `len(mon_paquet)`. Implémentez la méthode spéciale `__len__` pour renvoyer la longueur du paquet. Profitez-en pour empêcher que les utilisateurs de la classe modifient directement le paquet en rendant l'attribut `cartes` privé. Testez votre programme en mettant à jour le code `main.py`

- Maintenant que l'attribut `cartes` n'est plus censé être accessible hors de la classe, nous avons besoin d'un nouvelle méthode pour accéder à une carte du paquet depuis le programme principal. Implémentez la méthode spéciale `__getitem__` pour pouvoir accéder à une carte avec `mon_paquet[position]`. Tester la dans le programme principal.

- Notre `Paquet` ressemble maintenant beaucoup à une véritable liste python. Essayez dans le `main.py` d'utiliser la méthode `shuffle` classique de Python pour mélanger un paquet de carte : Il manque quelque chose.

- Dans l'interpréteur (`python3` ou `ipython3`) affichez la liste des méthode de la classe paquet en utilisant `dir()`. Les méthodes en python sont assignées dynamiquement aux classes et peuvent être modifiées au fur et à mesure du programme. Ajoutons une méthode `__setitem__` directement depuis l'interpréteur (démo). Affichez à nouveau le dictionnaire `dir()` de `mon_paquet` pour voir la nouvelle méthode ajoutée.

- Ajoutez maintenant `__setitem__` dans le code de `Paquet`. Supprimez et remplacez la méthode `melanger` par `shuffle` dans le code du projet.



{{% expand "`carte.py`" %}}
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

{{% expand "`paquet.py`" %}}

```python
from carte import Carte
import random

class Paquet:
    
    def __init__(self):
        self._cartes = []
        for valeur in Carte.valeurs_valides:
            for couleur in Carte.couleurs_valides:
                c = Carte(valeur, couleur)
                self._cartes.append(c)
                
    def __repr__(self):
        return str(self._cartes)

    # Permet d'utiliser la fonction len(mon_paquet)
    def __len__(self):
        return len(self._cartes)

    # Permet d'utiliser les notations de liste : mon_paquet[3] ou mon_paquet[2:5] (slice)
    def __getitem__(self, position):
        return self._cartes[position]

    # Permet d'utiliser les assignations de liste : mon_paquet[3] = carte("DAME", "TREFLE")
    def __setitem__(self, position, carte):
        self._cartes[position] = carte
    
    def couper(self):
        coupe_index = random.randint(0, len(self._cartes))
        self._cartes = self._cartes[coupe_index:] + self._cartes[:coupe_index]

    def piocher(self):
        pioche = self._cartes[0]
        self._cartes = self._cartes[1:]
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

{{% expand "`main.py`" %}}

```python
from carte import Carte
from random import shuffle

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

shuffle(P)

print("\nApres melange\n")
print(P)


print(f"Actuellement il y a {len(P)} cartes dans le paquet")
print(P.piocher())
print(P.piocher())
print(f"Actuellement il y a {len(P)} cartes dans le paquet")

cartes_alice, cartes_bob = P.distribuer(nb_joueurs=2, nb_cartes=5)
print(cartes_alice)
print(cartes_bob)
```

{{% /expand %}}