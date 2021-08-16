---
title: Exercice 3.1 - Cercles et Cylindres
draft: false
weight: 20
---

Dans cet exercice nous allons représenter des objets et calculs géométriques simples en coordonnées entières. Utilisez des annotations de types `: int`, `-> None`, `-> int` et `: Tuples[int ...]` dès que possible. Testez régulièrement la consistance de ces types avec `mypy fichier.py`.

- Implémenter une classe `Cercle` avec comme attributs un rayon `rayon` et les coordonnées `x` et `y` de son centre. Par exemple on pourra instancier un cercle avec `mon_cercle = Cercle(5, (3,1))`

- Dans la classe `Cercle`, implémenter une propriété `aire` dépendante du rayon qu'on peut appeler avec `mon_cercle.aire`.

- Implémenter une classe `Cylindre`, fille de `Cercle`, qui est caractérisée par un rayon `rayon`, une hauteur `hauteur` et des coordonnées `x`, `y` et `z`. On écrira le constructeur de `Cylindre` en appelant le constructeur de `Cercle`.

- Dans la classe `Cercle`, implémenter une méthode `intersect` qui retourne `True` ou `False` suivant si deux cercles se touchent. Exemple d'utilisation : `c1.intersect(c2)`

- Surcharger la méthode `intersect` pour la classe `Cylindre`, en se basant sur le résultat de la méthode de la classe mère.
