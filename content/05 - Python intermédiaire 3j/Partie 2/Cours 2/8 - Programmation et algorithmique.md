---
title: 8. Programmation et algorithmique - récapituler
draft: false
weight: 20
---


## Programmation impérative / procédurale

- Comme une recette de cuisine qui manipule de l'information
- Une suite d'opération à effectuer
- Différents concepts pour construire ces opérations:
    - des variables
    - des fonctions
    - des conditions
    - des boucles
    - des structures de données (listes, dictionnaires)



## Variables

```python
x = "Toto"
x = 40
y = x + 2
print("y contient " + str(y))
```



## Fonctions

```python
def aire_triangle(base, hauteur):
    calcul = base * hauteur / 2
    return calcul

A1 = aire_triangle(3, 5)      # -> A1 vaut 15 !
A2 = aire_triangle(4, 2)      # -> A2 vaut 8 !
```

- Indentation
- Arguments (peuvent être optionnels si on spécifie une valeur par défaut)
- Variables locales
- `return` pour pouvoir récupérer un résultat depuis l'extérieur
- Appel de fonction


## Conditions

```python
def aire_triangle(base, hauteur):

    if base < 0 or hauteur < 0:
        print("Il faut donner des valeurs positives!")
        return -1

    calcul = base * hauteur / 2
    return calcul
```

- Indentation
- Opérateurs (`==`, `!=`, `<=`, `>=`, `and`, `or`, `not`, `in`, ...)
- Mot clefs `if`, `elif`, `else`


## Listes, dictionnaires et boucles

```python
breakfast = ["Spam", "Eggs", "Bacon", "Spam"]
breakfast.append("Coffee")

print("Au petit dej' je mange: ")
for stuff in breakfast:
    print(stuff)
```


```python
ingredients_gateau = {"farine": 200,
                      "beurre": 100,
                      "chocolat": 150}

for ingredient, qty in ingredients_gateau.items():
    print("J'ai besoin de " + str(qty) + "g de " + ingredient)
```

## Algorithmes simples : `max`

```python
def max(liste_entiers):
    if liste_entiers == []:
        print("Erreur, peut pas calculer le max d'une liste vide")
        return None

    m = liste_entiers[0]
    for entier in liste_entiers:
        if m < entier:
            m = entier

    return m
```

## Algorithmes simples : filtrer une liste

```python
def pairs(liste_entiers):

    resultat = []

    for entier in liste_entiers:
        if entier % 2 == 0:
            resultat.append(entier)

    return resultat
```

