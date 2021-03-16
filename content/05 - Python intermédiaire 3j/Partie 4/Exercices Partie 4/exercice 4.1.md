---
title: Exercice 4.1 - Un paquet pythonique
draft: false
weight: 20
---


## 4.1 Utiliser les syntaxes de liste sur la classe `Paquet`

- Plutôt que d'utiliser `len(mon_paquet.cartes)` pour avoir le nombre de carte on voudrait utiliser `len(mon_paquet)`. Implémentez la méthode spéciale `__len__` pour renvoyer la longueur du paquet. Profitez-en pour empêcher que les utilisateurs de la classe modifient directement le paquet en rendant l'attribut `cartes` privé. Testez votre programme en mettant à jour le code `main.py`

- Maintenant que l'attribut `cartes` n'est plus censé être accessible hors de la classe, nous avons besoin d'un nouvelle méthode pour accéder à une carte du paquet depuis le programme principal. Implémentez la méthode spéciale `__getitem__` pour pouvoir accéder à une carte avec `mon_paquet[position]`. Tester la dans le programme principal.

- Notre `Paquet` ressemble maintenant beaucoup à une véritable liste python. Essayez dans le `main.py` d'utiliser la méthode `shuffle` classique de Python pour mélanger un paquet de carte : Il manque quelque chose.

- Dans l'interpréteur (`python3` ou `ipython3`) affichez la liste des méthode de la classe paquet en utilisant `dir()`. Les méthodes en python sont assignées dynamiquement aux classes et peuvent être modifiées au fur et à mesure du programme. Ajoutons une méthode `__setitem__` directement depuis l'interpréteur (démo). Affichez à nouveau le dictionnaire `dir()` de `mon_paquet` pour voir la nouvelle méthode ajoutée.

- Ajoutez maintenant `__setitem__` dans le code de `Paquet`. Supprimez et remplacez la méthode `melanger` par `shuffle` dans le code du projet.
