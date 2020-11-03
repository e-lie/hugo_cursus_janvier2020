---
title: Exos 1
draft: false
weight: 20
---



### 1. Calculs dans l'interpréteur

- À l'aide de python, calculer le résultat des opérations suivantes :
    - `567×72`
    - `33⁴`
    - `98.2/6`
    - `((7×9)⁴)/6`
    - `vrai et non (faux ou non vrai)`


### 2. Interactivité

- Demander l'année de naissance de l'utilisateur, puis calculer et afficher l'âge qu'il
aura dans deux ans (approximativement, sans tenir compte du jour et mois de naissance...).


### 3. Chaînes de caractères

- Demander un mot à l'utilisateur. Afficher la longueur du mot avec
une message tel que `"Ce mot fait X caractères !"`

- Afficher le mot encadré avec des `####`. Par exemple:

```
##########
# Python #
##########
```

### 4. Fonctions

- Ecrire une fonction `centrer` prend en argument une chaîne de caractère, et retourne une nouvelle chaîne centrée sur 40 caractères. Par exemple `print(centrer("Python"))` affichera :

```text
|                Python                |
```

- Ajouter un argument optionnel pour gérer la largeur au lieu du 40 "codé en dur". Par exemple `print(centrer("Python", 20)) affichera :`

```text
|      Python      |
```

- Créer une fonction `encadrer` qui utilise la fonction `centrer` pour
produire un texte centré et encadré avec des `####`. Par exemple,
`print(encadrer("Python", 20))` affichera :

```text
####################
|      Python      |
####################
```


### 5. Conditions

- Reprendre la fonction `annee_naissance` et afficher un message d'erreur et sortir immédiatement de la fonction si l'argument fourni n'est pas un nombre entre 0 et 130. Valider le comportement en appelant votre fonction avec comme argument `-12`, `158`, `None` ou `"toto"`.

- Inspecter l'execution du code pas à pas à l'aide du debugger VSCode.

- Reprendre la fonction `centrer` de l'exercice 4.1 et gérer le cas où la largueur demandée est -1 : dans ce cas, ne pas centrer. Par exemple,
`print(encadrer("Python", -1))` affichera :

```text
##########
# Python #
##########
```

### 6. Performances et debugging : plusieurs implémentations de la suite de fibonacci

La célèbre suite de Fibonacci, liée au nombre d'or, est une suite d'entiers dans laquelle chaque terme est la somme des deux termes qui le précèdent. Mais elle est également un exercice classique d'algorithmique.

- Écrire une fonction `fibonacci_rec_naive(n)` qui calcule de façon récursive la suite de fibonacci.

- Créez une autre fonction `fibonacci_iter(n)` qui calcule de façon iterative la suite de fibonacci.

- Calculez le 40e terme de la suite avec chacune des implémentation précédente.

- Debuggez les deux implémentations. Que se passe-t-il ?

- A l'aide de la librairie timeit et de sa fonction timer (`from timeit import default_timer as timer`) qui renvoie le temps processeur courant, mesurez le temps d'exécution des deux fonctions.

- Écrire une fonction `fibonacci_rec_liste(n)` qui calcule récursivement la suite de fibonacci en utilisant une liste comme mémoire pour ne pas recalculer les terme déjà calculés.

- Bonus 1: Utilisons un décorateur de "caching" de fonction (`from functools import lru_cache as cache`) sur `fibonacci_rec_naive(n)` pour l'optimiser sans changer le code.

- Bonus 2: Écrivons une implémentation pythonique de fibonacci utilisant un générateur