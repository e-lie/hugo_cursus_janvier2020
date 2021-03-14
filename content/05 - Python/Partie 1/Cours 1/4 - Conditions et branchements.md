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

Attention à l'indentation !

Tout n'est pas nécessaire, par exemple on peut simplement mettre un `if` :

```python
if x == 50:
    print("une cinquantaine")
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
```