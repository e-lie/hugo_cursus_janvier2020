---
title: Correction exercice 3.2 - Jeu de carte
draft: false
weight: 20
---


### Une classe Carte pour représenter les éléments d'un jeu

- Dans un fichier `carte.py`, créer une classe `Carte`. Une carte dispose d'une `valeur` (1 à 10 puis VALET, DAME et ROI) et d'une `couleur` (COEUR, PIQUE, CARREAU, TREFLE). Par exemple, on pourra créer des cartes en invoquant `Carte(3, 'COEUR')` et `Carte('ROI', 'PIQUE')`. 

- Implémenter la méthode `points` pour la classe `Carte`, qui retourne un nombre entre 1 et 13 en fonction de la valeur de la carte. Valider ce comportement depuis un fichier `main.py` qui importe la classe Carte.

- Implémenter la méthode `__repr__` pour la classe `Carte`, de sorte à ce que `print(Carte(3, "COEUR"))` affiche `<Carte 3 de COEUR>`.

```python
c = Carte("DAME", "PIQUE")

print(c.couleur)
# Affiche PIQUE

print(c.points)
# Affiche 12

print(c)
# Affiche <Carte DAME de PIQUE>
```

{{% expand "Correction 3.2 `carte.py`" %}}

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

Pour sécuriser l'usage ultérieur de notre jeu de carte on aimerait que les cartes ne puissent être crées et modifiées qu'avec des valeurs correctes (les 4 couleurs et 13 valeurs précisées)

- Modifiez le constructeur pour valider que les données fournies sont valides. Sinon levez une exception (on utilise conventionnellement le type d'exception `ValueError` pour cela ou un type d'exception personnalisé).

- Modifiez également les paramètres `couleur` et `valeur` pour les rendre privés, puis créer des accesseurs et mutateurs qui permettent d'y accéder en mode public et de valider les données à la modification.

{{% expand "Correction 3.2 `carte.py`" %}}

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

- Dans un nouveau fichier `paquet.py`, créer une classe `Paquet` correspondant à un paquet de 52 cartes. Le constructeur devra créer toute les cartes du jeu et les stocker dans une liste ordonnée. Vous aurez probablement besoin d'importer la classe `Carte`. Testez le comportement de cette classe en l'important et en l'utilisant dans `main.py`.

- Implémenter la méthode `melanger` pour la classe `Paquet` qui mélange l'ordre des cartes.

- Implémenter la méthode `couper` qui prends un nombre aléatoire du dessus du paquet et les place en dessous.

- Implémenter la méthode `piocher` qui retourne la `Carte` du dessus du paquet (eticla l'enlève du paquet)

1.0 : Implémenter la méthode `distribuer` qui prends en argument un nombre de carte et un nombre de joueurs (e.g. `p.distribuer(joueurs=4, cartes=5)`), pioche des cartes pour chacun des joueurs à tour de rôle, et retourne les mains correspondantes.


```python
p = Paquet()
p.melanger()

main_alice, main_bob = p.distribuer(joueurs=2, cartes=3)

print(main_alice)
# affiche par exemple [<Carte 3 de PIQUE>, <Carte VALET de CARREAU>, <Carte 1 de trefle>]

print(p.pioche())
# affiche <Carte 9 de CARREAU>

print(main_alice[1].points())
# affiche 11
```

{{% expand "Correction 3.2 `paquet.py`" %}}

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
                
    def __repr__(self): # fonction qui décrit comment représenter une carte sous forme texte (exp avec print) 
        return str(self.cartes) # Renvoie simplement l'affichage de la liste de cartesTRE 
    
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


{{% expand "Correction 3.2 `main.py`" %}}

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

