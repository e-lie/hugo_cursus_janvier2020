---
title: 4. Conditions et branchements conditionnels
draft: false
weight: 20
---

Pour pouvoir écrire des applications il faut des techniques permettant de contrôler le déroulement du programme dans différentes directions, en fonction des circonstances. Pour cela, nous devons disposer d’instructions capables de tester une certaine condition et modifier le comportement du programme en conséquence.

La principale instruction conditionnelle est, en python comme dans les autres langages **impératifs**, le `if` (Si condition alors ...) assorti généralement du `else` (Sinon faire ...) et en python de la contraction `elif` de `else if` (Sinon, Si condition alors ...)e

### Syntaxe générale

```python
if condition:
    instruction1
    instruction2
elif (autre condition):
    instruction3
elif (encore autre condition):
    instruction4
else:
    instruction5
    instruction6
```

Attention à l'indentation !


Tout n'est pas nécessaire, par exemple on peut simplement mettre un `if` :

```python
if condition:
    instruction1
    instruction2
```


###  Exemple

```python
a = 0
if a > 0 :
    print("a est positif")
elif a < 0 :
    print("a est négatif")
else:
    print("a est nul")
```


### Lien avec les booléens

Les conditions comme `a > 0` sont en fait transformées en booléen lorsque la ligne est interprétée.

On aurait pu écrire :

```python
a_est_positif = (a > 0)

if a_est_positif:
    [...]
else:
    [...]
```

### Écrire des conditions

```python
angle == pi      # Égalité
angle != pi      # Différence
angle > pi       # Supérieur
angle >= pi      # Supérieur ou égal
angle < pi       # Inférieur
angle <= pi      # Inférieur ou égal
```

### Combiner des conditions

```python
x = 2

print("x > 0:", x > 0) # vrai 
print("x > 0 and x == 2:", x > 0 and x == 2) # vrai et vrai donne vrai
print("x > 0 and x == 1:", x > 0 and x == 2) # vrai et faux donne faux
print("x > 0 or x == 1:", x > 0 or x == 1) # vrai ou faux donne vrai
print("not x == 1:", not x == 1) # non faux donne vrai
print("x > 0 or not x == 1:", x > 0 or not x == 1) # vrai ou (non faux) donne vrai ou vrai donne vrai
```

###  Conditions "avancées"

#### Chercher des choses dans des chaînes de caractères

```python
"Jack" in nom           # 'nom' contient 'Jack' ?
nom.startswith("Jack")  # 'nom' commence par 'Jack' ?
nom.endswith("ack")     # 'nom' fini par 'row' ?
```

Remarque: l'opérateur `in` est très utile et générale en Python: il sert à vérifier qu'un élément existe dans une collection. Par exemple si l'entier 2 est présent dans une liste d'entier ou comme ici si un mot est présent dans une chaine de caractère.

#### 'Inline' `if`s

On peut rassembler un if else sur une ligne comme suit:

```python
parite = "pair" if n % 2 == 0 else "impair"
```


#### Tester si une variable a une valeur de façon "pythonique".

En python pour tester si une variable contient une valeur vide ou pas de valeur (c-à-d valeur `None`) on aime bien, par convention "pythonique", écrire simplement `if variable:` :

```python
reste_division = a % 2

if reste_division:
    print("a est pair parce que le reste de sa division par 2 est nul")
else:
    print("a est impair")
```

Pareil pour tester si unt chaîne de caractère est vide ou nulle:

```python
texte = input()

if texte:
    print("vous avez écrit: ", texte)
else:
    print("pas de texte")
    print("texte is None :", texte is None)
    print("texte == \"\" (chaine vide) :", texte == "")
```

Remarque: dans notre dernier cas il n'est pas forcément important de savoir si `texte` est `None` ou une chaîne vide mais plutôt de savoir si on a effectivement une valeur "significative" à afficher. C'est souvent le cas et c'est pour cela qu'on privilégie `if variable` pour simplifier la lecture du code.

#### Vraisemblance (truthiness) d'un valeur

L'usage de `if variable:` comme précédemment est basé sur la truthiness ou vraisemblance de la variable. On dit que `a` est vraisemblable si la conversion de `a` en booléen donne `True` : `bool(3)` donne `True` on dit que 3 est `truthy`, `bool(None)` donne `False` donc `None` est `falsy`.
TODO Nous verrons dans la partie sur le `Python Data Model` que cela implique des choses pour nos classes de programmation orientée objet en python (en Résumé on veut que `if monObjet:` soit capable de tester si l'objet est initialisé et utilisable) 
Autrement dit en python on aime utiliser la vraisemblance implicite des variables pour tester si leur valeur est significative/initialisée ou non.

<!-- TODO Nous verrons dans la partie sur le `Python Data Model` que cela implique des choses pour nos classes de programmation orientée objet en python (en Résumé on veut que `if monObjet:` soit capable de tester si l'objet est initialisé et utilisable)  -->
