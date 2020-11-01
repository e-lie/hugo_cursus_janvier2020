---
title: Correction 4.2 - Un itérateur de cartes
draft: false
weight: 31
---

## 4.2 Itérateurs de carte : génération de la suite de carte à partir d'une carte


Plutôt que de générer les 52 cartes avec une boucle for dans le constructeur du paquet on voudrait utiliser un générateur/itérateur associé à la classe carte.

- Ajoutez à `carte.py` une classe `IterateurDeCarte` pour générer la suite des cartes à partir d'un objet carte.
    1. D'abord créez la classe `IterateurDeCarte` qui prend en argument une Carte à la création et qui possède des méthodes `__next__(self)` qui retourne la carte suivante dans l'ordre des cartes et `__iter__` qui lui permet de se renvoyer lui même pour continuer l'itération.
    1. Ajoutez une méthode `__iter__` à la classe carte qui renvoie un itérateur basée sur la carte courante.

- Générez les 52 cartes du paquet à partir de notre iterateur de carte.

- Ajoutez un paramètre facultatif `carte_de_départ` au contructeur de paquet pour commencer la génération du paquet à partie d'une carte du milieu de la série de carte possible.

- Modifiez le constructeur de la classe `Carte` pour qu'elle prenne en argument des valeurs et couleurs possibles qui ne soit pas les valeurs classique. Testez cette fonctionnalité dans `main.py` en générant un jeu de "UNO" (sans les cartes "Joker" noire) à la place d'un jeu classique. ![Cartes de Uno](https://upload.wikimedia.org/wikipedia/commons/2/28/Baraja_de_UNO.JPG)


{{% expand "`carte.py`" %}}

```python
class InvalidCardColor(ValueError):
    pass


class InvalidCardValue(ValueError):
    pass


class Carte:
    """Représente une carte à jouer classique d'un jeu de 52 cartes"""
    
    def __init__(self, valeurs_valides, couleurs_valides, valeur, couleur):
        self.valeurs_valides = valeurs_valides
        self.couleurs_valides = couleurs_valides
        # La bonne méthode pour gérer les valeurs incorrectes en python est généralement de lever une exception
        # (traditionnellement de type ValueError qui est faite pour cela mais une Exception spécifique c'est encore mieux car cela explique l'erreur à l'utilisateur de la classe)
        if valeur not in self.valeurs_valides:
            raise InvalidCardValue
        if couleur not in self.couleurs_valides:
            raise InvalidCardColor
        self._valeur = valeur
        self._couleur = couleur

    @property
    def valeur(self):
        return self._valeur

    @valeur.setter
    def valeur(self, valeur):
        if valeur not in self.valeurs_valides:
            raise InvalidCardValue
        self._valeur = valeur

    @property
    def couleur(self):
        return self._couleur

    @couleur.setter
    def couleur(self, couleur):
        if couleur not in self.couleurs_valides:
            raise InvalidCardColor
        self._couleur = couleur

    @property
    def points(self):
        return Carte.valeurs_valides.index(self.valeur) + 1
        
    def __repr__(self):
        return f"<Carte {self.valeur} de {self.couleur}>"

    def __iter__(self):
        return IterateurDeCarte(self.valeurs_valides, self.couleurs_valides, self)



class IterateurDeCarte:
    def __init__(self, valeurs_valides, couleurs_valides, carte):
        self.valeurs_valides = valeurs_valides
        self.couleurs_valides = couleurs_valides
        self._tuples_valeur_couleur = [(valeur, couleur) for valeur in valeurs_valides
                                               for couleur in couleurs_valides]
        self.index = self._tuples_valeur_couleur.index((carte.valeur, carte.couleur))

    def __next__(self):
        if self.index+1 == len(self._tuples_valeur_couleur) :
            raise StopIteration
        tuple_carte_suivante = self._tuples_valeur_couleur[self.index+1]
        self.index = self.index + 1
        return Carte(self.valeurs_valides, self.couleurs_valides, tuple_carte_suivante[0], tuple_carte_suivante[1])

    def __iter__(self):
        # Une fois qu'il a renvoyé un élément et a mis l'index à jour,
        # l'itérateur doit se renvoyer au contexte appelant pour que l'itération puisse continuer
        return self

```

{{% /expand %}}



{{% expand "`paquet.py`" %}}

```python
from carte import Carte
import random

class Paquet:
    
    def __init__(self, carte_initiale):
        self._cartes = list(iter(carte_initiale))
                
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
from paquet import Paquet
from random import shuffle


# On utilise ici des tuples car il sont immutable (pas possible d'ajouter des couleurs/valeurs pendant l'exécution)
# On utilise des noms en majuscule car c'est une convention pour dire que ce sont des constantes
# On aurait également put utiliser des enums pour représenter les couleurs/valeurs possibles (classique en programmation)
couleurs_valides = ("ROUGE", "VERT", "BLEU", "JAUNE")
valeurs_valides = tuple(list(range(0,10)) + ["CHANGE_SENS", "PASSE_TOUR", "+2_CARTES"])

carte_uno_initiale = Carte(valeurs_valides, couleurs_valides, 0, "ROUGE")

paquet_uno = Paquet(carte_uno_initiale)

print(paquet_uno)
shuffle(paquet_uno)
print(paquet_uno)
```

{{% /expand %}}

## Bonus : d'autres générateurs de carte

Les listes sont des collections finies et les itérateurs de liste sont donc toujours finis. Cependant un itérateur n'a pas de taille en général et peut parfois générer indéfiniment des valeurs (grace à un générateur infini par exemple).

- Modifiez l'itérateur de carte pour qu'elle se base sur un générateur de carte aléatoire infini.

