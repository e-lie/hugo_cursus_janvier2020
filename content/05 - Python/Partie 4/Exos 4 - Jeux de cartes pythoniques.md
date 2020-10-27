---
title: Exo 4 - Rendre le jeu de carte Pythonique avec le Python Object Model
draft: false
weight: 20
---


## 4.1 Utiliser les syntaxes de liste sur la classe `Paquet`

- Plutôt que d'utiliser `len(mon_paquet.cartes)` pour avoir le nombre de carte on voudrait utiliser `len(mon_paquet)`. Implémentez la méthode spéciale `__len__` pour renvoyer la longueur du paquet. Profitez-en pour empêcher que les utilisateurs de la classe modifient directement le paquet en rendant l'attribut `cartes` privé. Testez votre programme en mettant à jour le code `main.py`

- Maintenant que l'attribut `cartes` n'est plus censé être accessible hors de la classe, nous avons besoin d'un nouvelle méthode pour accéder à une carte du paquet depuis le programme principal. Implémentez la méthode spéciale `__getitem__` pour pouvoir accéder à une carte avec `mon_paquet[position]`. Tester la dans le programme principal.

- Notre `Paquet` ressemble maintenant beaucoup à une véritable liste python. Essayez dans le `main.py` d'utiliser la méthode `shuffle` classique de Python pour mélanger un paquet de carte : Il manque qqch.

- Dans l'interpréteur (`python3` ou `ipython3`) affichez la liste des méthode de la classe paquet en utilisant `dir()`. Les méthodes en python sont assignées dynamiquement aux classes et peuvent être modifiées au fur et à mesure du programme. Ajoutons une méthode `__setitem__` directement depuis l'interpréteur (démo). Affichez à nouveau le dictionnaire `dir()` de `mon_paquet` pour voir la nouvelle méthode ajoutée.

- Ajoutez maintenant `__setitem__` dans le code de `Paquet`. Supprimez et remplacez la méthode `melanger` par `shuffle` dans le code du projet.


## 4.2 Itérateurs de carte : génération de la suite de carte à partir d'une carte


Plutôt que de générer les 52 cartes avec une boucle for dans le constructeur du paquet on voudrait utiliser un générateur/itérateur associé à la classe carte.

- Ajoutez à `carte.py` une classe `IterateurDeCarte` pour générer la suite des carte à partir d'un objet carte.
    1. D'abord créez la classe `IterateurDeCarte` qui prend en argument une Carte à la création et qui possède des méthodes `__next__(self)` qui retourne la carte suivante dans l'ordre des cartes.
    1. 

- Générez les 52 cartes du paquet à partir de notre iterateur de carte.

- Ajoutez un paramètre facultatif `carte_de_départ` au contructeur de paquet pour commencer la génération du paquet à partie d'une carte du milieu de la série de carte possible.

- Modifiez le constructeur de la classe `Carte` pour qu'elle prenne en argument des valeurs et couleurs possibles qui ne soit pas les valeurs classique. Testez cette fonctionnalité dans `main.py` en générant un jeu de "UNO" (sans les cartes "Joker" noire) à la place d'un jeu classique. ![Cartes de Uno](https://upload.wikimedia.org/wikipedia/commons/2/28/Baraja_de_UNO.JPG)


## Bonus : d'autres générateurs de carte

Les listes sont des collections finies et les itérateurs de liste sont donc toujours finis. Cependant un itérateur n'a pas de taille en général et peut parfois générer indéfiniment des valeurs.

Bonus: Modifiez l'itérateur de carte pour en faire un générateur de carte aléatoire infini.

