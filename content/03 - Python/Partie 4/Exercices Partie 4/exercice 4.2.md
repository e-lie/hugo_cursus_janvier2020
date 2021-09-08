---
title: Exercice 4.2 - Un itérateur de cartes
draft: false
weight: 21
---

## 4.2 Itérateurs de carte : génération de la suite de carte à partir d'une carte


Plutôt que de générer les 52 cartes avec une boucle for dans le constructeur du paquet on voudrait utiliser un générateur/itérateur associé à la classe carte.

- Ajoutez à `carte.py` une classe `IterateurDeCarte` pour générer la suite des cartes à partir d'un objet carte.
    1. D'abord créez la classe `IterateurDeCarte` qui prend en argument une Carte à la création et qui possède des méthodes `__next__(self)` qui retourne la carte suivante dans l'ordre des cartes et `__iter__` qui lui permet de se renvoyer lui même pour continuer l'itération.
    1. Ajoutez une méthode `__iter__` à la classe carte qui renvoie un itérateur basée sur la carte courante.

- Générez les 52 cartes du paquet à partir de notre iterateur de carte.

- Ajoutez un paramètre facultatif `carte_de_départ` au contructeur de paquet pour commencer la génération du paquet à partie d'une carte du milieu de la série de carte possible.

- Modifiez le constructeur de la classe `Carte` pour qu'elle prenne en argument des valeurs et couleurs possibles qui ne soit pas les valeurs classique. Testez cette fonctionnalité dans `main.py` en générant un jeu de "UNO" (sans les cartes "Joker" noire) à la place d'un jeu classique. ![Cartes de Uno](https://upload.wikimedia.org/wikipedia/commons/2/28/Baraja_de_UNO.JPG)


## Bonus : d'autres générateurs de carte

Les listes sont des collections finies et les itérateurs de liste sont donc toujours finis. Cependant un itérateur n'a pas de taille en général et peut parfois renvoyer des valeurs indéfiniment des valeurs (grace à un générateur infini par exemple).

- Modifiez l'itérateur de carte pour qu'il se base sur un générateur de carte infini utilisant les nombre de la suite de fibonacci et les quatre couleurs du UNO. (Voir correction de fibonacci dans la partie 1)

