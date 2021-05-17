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
