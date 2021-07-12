---
title: 5. Les boucles
draft: false
weight: 20
---


Répéter des opération est le coeur de la puissance de calcul des ordinateur. On peut pour cela utiliser des boucles ou des appels récusifs de fonctions. Les deux boucles python sont `while` et `for`.

### La boucle while

`while <condition>:` veut dire "tant que la condition est vraie répéter ...". C'est une boucle simple qui teste à chaque tour (avec une sorte de if) si on doit continuer de boucler.

Exemple:

```python
a = 0
while (a < 10) # On répète les deux instructions de la boucle tant que a est inférieur à 7
    a = a + 1 # A chaque tour on ajoute 1 à la valeur de a
    print(a)
```

### La boucle for et les listes

<!-- TODO partie iterateur -->
La boucle `for` en Python est plus puissante et beaucoup plus utilisée que la boucle `while` car elle "s'adapte aux données" et aux objets du programme grâce à la notion d'itérateur que nous détaillerons plus loin. (De ce point de vue, la boucle `for` python est très différente de celle du C/C++ par exemple)

On peut traduire la boucle Python `for element in collection:` en français par "Pour chaque élément de ma collection répéter ...". Nous avons donc besoin d'une "collection" (en fait un iterateur) pour l'utiliser. Classiquement on peut utiliser une liste python pour cela:

```python
ma_liste = [7, 2, -5, 4]

for entier in ma_liste:
    print(entier)
```

Pour générer rapidement une liste  d'entiers et ainsi faire un nombre défini de tours de boucle on utilise classiquement la fonction `range()`

```python
print(range(10))

for entier in range(10):
    print(entier) # Afficher les 10 nombres de 0 à 9
```

```python
for entier in range(1, 11):
    print(entier) # Afficher les 10 nombres de 1 à 10
```

```python
for entier in range(2, 11, 2):
    print(entier) # Afficher les 5 nombres pairs de 2 à 10 (le dernier paramètre indique d'avancer de 2 en 2)
```


## `continue` et `break`

`continue` permet de passer immédiatement à l'itération suivante

`break` permet de sortir immédiatement de la boucle


```python
for i in range(0,10):
    if i % 2 == 0:
        continue

    print("En ce moment, i vaut " + str(i))
```

-> Affiche le message seulement pour les nombres impairs


```python
for i in range(0,10):
    if i == 7:
        break

    print("En ce moment, i vaut " + str(i))
```

-> Affiche le message pour 0 à 6

### Ex.5 Boucles

5.1.1 : Écrire une fonction qui, pour un nombre donné, renvoie la table de multiplication. Dans un premier temps, on pourra se contenter d'afficher les résultats. Par exemple print(table_du_7()) affichera:

```python
7
14
21
...
70
```

puis ensuite on peut améliorer la présentation pour obtenir le résultat :

```python
Table du 7
----------
 1 x 7 = 7
 2 x 7 = 14
 [..]
 10 x 7 = 70
 ```

5.1.2 : Cette fois, passer le nombre en argument. La fonction devient par exemple `table_multiplication(7)`

5.1.3 : En appelant cette fonction plusieurs fois, afficher les tables de multiplication pour tous les nombres entre 1 et 10.

5.1.4 : Protéger l'accès à toute cette connaissance précieuse en demandant, au début du programme, un "mot de passe" jusqu'à ce que le bon mot de passe soit donné.

5.2 : (Optionnel) Écrire une fonction qui permet de déterminer si un nombre est premier. Par exemple `is_prime(3)` renverra True, et `is_prime(10)` renverra False.

5.3.1 : Jeu des allumettes

Le jeu des allumettes est un jeu pour deux joueurs, où n allumettes sont disposées, et chaque joueur peut prendre à tour de rôle 1, 2 ou 3 allumettes. Le perdant est celui qui se retrouve obligé de prendre la dernière allumette.


- Écrire une fonction afficher_allumettes capable d'afficher un nombre donné d'allumettes (donné en argument), par exemple avec le caractère |
- Écrire une fonction choisir_nombre qui demande à l'utilisateur combien d'allumette il veut prendre. Cette fonction vérifiera que le choix est valide (en entier qui est soit 1, 2 ou 3).
- Commencer la construction d'une fonction partie_allumettes qui pour le moment, se contente de :
    - Initialiser le nombre d'allumette sur la table
    - Afficher des allumettes avec `afficher_allumettes`
    - Demander à l'utilisateur combien il veut prendre d'allumettes `avec choisir_nombre`
    - Propager ce choix sur le nombre d'allumette actuellement sur la table
    - Afficher le nouvel état avec `afficher_allumettes`

5.3.2 : (Optionnel) Modifier `partie_allumettes` pour gérer deux joueurs (1 et 2) et les faire jouer à tour de rôle jusqu'à ce qu'une condition de victoire soit détectée (il reste moins d'une allumette...).

5.3.3 : (Optionnel) Intelligence artificielle

Reprendre le jeu précédent et le modifier pour introduire une "intelligence" artificielle qui soit capable de jouer en tant que 2ème joueur. (Par exemple, une stratégie très simple consiste à prendre une allumette quoiqu'il arrive)
