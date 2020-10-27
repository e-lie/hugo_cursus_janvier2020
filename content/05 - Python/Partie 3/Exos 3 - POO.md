---
title: Exos Partie 3 - POO
draft: false
weight: 20
---


## Exercice 4.1 : Cercles et cylindres

Dans cet exercice nous allons représenter des objets et calculs géométriques simples en coordonnées entières. Utilisez des annotations de types `: int`, `-> None`, `-> int` et `: Tuples[int ...]` dès que possible (voir [la documentation de mypy](https://mypy.readthedocs.io/en/stable/cheat_sheet_py3.html)).py`, créer une classe `Carte`. Une carte dispose d'une `valeur` (1 à 10 puis VALET, DAME et ROI) et d'une `couleur` (COEUR, PIQUE, CARREAU, TREFLE). Par exemple, on pourra créer des cartes en invoquant `Carte(3, 'COEUR')` et `Carte('ROI', 'PIQUE')`. 

- Implémenter la méthode `points` pour la classe `Carte`, qui retourne un nombre entre 1 et 13 en fonction de la valeur de la carte. Valider ce comportement depuis un fichier `main.py` qui importe la classe Carte.

- Implémenter la méthode `__repr__` pour la classe `Carte`, de sorte à ce que `print(Carte(3, "COEUR"))` affiche `<Carte 3 de COEUR>`.

```python
c = Carte("Q", "PIQUE")

print(c.couleur)
# Affiche PIQUE

print(c.points)
# Affiche 12

print(c)
# Affiche <Carte DAME de PIQUE>
```

## Exercice 4.2 : Programmer un jeu de carteù= à 

### Encapsulation et validation des valeurs de carte possibles

Pour sécuriser l'usage ultérieur de notre jeu de carte on aimerait que les cartes ne puissent être crées et modifiées qu'avec des valeurs correctes (les 4 couleurs et 13 valeurs précisées)

- Modifiez le constructeur pour valider que les données fournies sont valides. Sinon levez une exception (on utilise conventionnellement le type d'exception `ValueError` pour cela ou un type d'exception personnalisé).

- Modifiez également les paramètres `couleur` et `valeur` pour les rendre privés, puis créer des accesseurs et mutateurs qui permettent d'y accéder en mode public et de valider les données à la modification.

### La classe Paquet, une collection de cartes

- Dans un nouveau fichier `paquet.py`, créer une classe `Paquet` correspondant à un paquet de 52 cartes. Le constructeur devra créer toute les cartes du jeu et les stocker dans une liste ordonnée. Vous aurez probablement besoin d'importer la classe `Carte`. Testez le comportement de cette classe en l'important et en l'utilisant dans `main.py`.

- Implémenter la méthode `melanger` pour la classe `Paquet` qui mélange l'ordre des cartes.

- Implémenter la méthode `couper` qui prends un nombre aléatoire du dessus du paquet et les place en dessous.

- Implémenter la méthode `piocher` qui retourne la `Carte` du dessus du paquet (et l'enlève du paquet)

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

- Implémenter une classe `Cercle` avec comme attributs un rayon `rayon` et les coordonnées `x` et `y` de son centre. Par exemple on pourra instancier un cercle avec `mon_cercle = Cercle(5, (3,1))`

- Dans la classe `Cercle`, implémenter une propriété `aire` dépendante du rayon qu'on peut appeler avec `mon_cercle.aire`.

- Implémenter une classe `Cylindre`, fille de `Cercle`, qui est caractérisée par un rayon `rayon`, une hauteur `hauteur` et des coordonnées `x`, `y` et `z`. On écrira le constructeur de `Cylindre` en appelant le constructeur de `Cercle`.

- Dans la classe `Cercle`, implémenter une méthode `intersect` qui retourne `True` ou `False` suivant si deux cercles se touchent. Exemple d'utilisation : `c1.intersect(c2)`

- Surcharger la méthode `intersect` pour la classe `Cylindre`, en se basant sur le résultat de la méthode de la classe mère

## Exercice 4.2 : Programmer un jeu de carte

### Une classe Carte pour représenter les éléments d'un jeu

- Dans un fichier `carte.py`, créer une classe `Carte`. Une carte dispose d'une `valeur` (1 à 10 puis VALET, DAME et ROI) et d'une `couleur` (COEUR, PIQUE, CARREAU, TREFLE). Par exemple, on pourra créer des cartes en invoquant `Carte(3, 'COEUR')` et `Carte('ROI', 'PIQUE')`. 

- Implémenter la méthode `points` pour la classe `Carte`, qui retourne un nombre entre 1 et 13 en fonction de la valeur de la carte. Valider ce comportement depuis un fichier `main.py` qui importe la classe Carte.

- Implémenter la méthode `__repr__` pour la classe `Carte`, de sorte à ce que `print(Carte(3, "COEUR"))` affiche `<Carte 3 de COEUR>`.

```python
c = Carte("Q", "PIQUE")

print(c.couleur)
# Affiche PIQUE

print(c.points)
# Affiche 12

print(c)
# Affiche <Carte DAME de PIQUE>
```

### Encapsulation et validation des valeurs de carte possibles

Pour sécuriser l'usage ultérieur de notre jeu de carte on aimerait que les cartes ne puissent être crées et modifiées qu'avec des valeurs correctes (les 4 couleurs et 13 valeurs précisées)

- Modifiez le constructeur pour valider que les données fournies sont valides. Sinon levez une exception (on utilise conventionnellement le type d'exception `ValueError` pour cela ou un type d'exception personnalisé).

- Modifiez également les paramètres `couleur` et `valeur` pour les rendre privés, puis créer des accesseurs et mutateurs qui permettent d'y accéder en mode public et de valider les données à la modification.

### La classe Paquet, une collection de cartes

- Dans un nouveau fichier `paquet.py`, créer une classe `Paquet` correspondant à un paquet de 52 cartes. Le constructeur devra créer toute les cartes du jeu et les stocker dans une liste ordonnée. Vous aurez probablement besoin d'importer la classe `Carte`. Testez le comportement de cette classe en l'important et en l'utilisant dans `main.py`.

- Implémenter la méthode `melanger` pour la classe `Paquet` qui mélange l'ordre des cartes.

- Implémenter la méthode `couper` qui prends un nombre aléatoire du dessus du paquet et les place en dessous.

- Implémenter la méthode `piocher` qui retourne la `Carte` du dessus du paquet (et l'enlève du paquet)

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