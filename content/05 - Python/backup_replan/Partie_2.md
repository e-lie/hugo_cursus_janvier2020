---
title: Partie 1 - Révisions ? 
draft: true
---

### Exercice: Installer et découvrir ipython

Afficher la documentation sur une fonction, l'exemple de print

## Les variables et types élémentaires

TODO Une variable est une étiquette sur un morceau de mémoire ou sur un ensemble d'étiquettes sur des morceaux de mémoire.

Mettre des images

TODO lister les types élémentaires

TODO les autres types sont des objets

TODO Une valeur et donc par extension a un type dans tous les langages. Python gère les types implicitement et les devine en fonction du contexte.

### Exercice : convertir, tester le type des variables

## Les éditeurs de code

VSCode est un éditeur de code récent et très à la mode, pour de bonnes raisons:
- Il est simple ou départ et fortement extensible: à l'installation seules les fonctionnalités de base sont disponibles
    - Éditeur de code avec coloration et raccourcis pratiques
    - Navigateur de fichier (pour manipuler une grande quantité de fichers et sous dossier sans sortir de l'éditeur)
    - Recherche et remplacement flexible avec des expressions régulières (très important pour trouver ce qu'on cherche et faire de refactoring)
    - Terminal intégrée (On a plein d'outils de développement à utiliser dans le terminal)
    - Une interface git assez simple très bien faite (git on s'y perd facilement, une bonne interface aide à s'y retrouver)

Indépendamment du logiciel choisi on trouve en général toutes ces fonctionnalité sdans un éditeur de code

#### Exercice : Observons un peu tout ça avec une démo et récapitulons l'importance des ces fonctions.

### Installer des extensions pertinentes

Au sein de l'éditeur nous voulons coder en Python et également:
- Pouvoir détecter les erreurs de syntaxe.
- Pouvoir explorer le code python réparti dans plusieurs fichiers (sauter à la définition d'une fonction par exemple).
- Complétion automatique des noms de symboles (ça peut être pénible parfois).
- Pouvoir debugger le code python de façon agréable.
- Pouvoir refactorer (changer le nom de variables ou fonctions partout automatiquement).

Installez l'extension `Python` (et affichez la documentation si vous êtes curieux) en allant dans la section `Extensions` (Icone de gauche avec 4 carrés dont un détaché)

Nous allons également utiliser git sérieusement donc nous allons installer une super extension git appelée `Gitgraph` pour pouvoir mieux explorer l'historique d'un dépôt git.

Enfin vous pouvez installer d'autres extensions pour personnaliser l'éditeur comme l'extension VIM si vous aimez habituellement utiliser cet éditeur.

### Opensource et extensibilité : ne pas s'enfermer dans un environnement de travail

- VSCode est développé par Microsoft et partiellement opensource (Le principal code est accessible mais pas tout)
- VSCodium est la version opensource communautaire de VSCode mais certaines fonctions puissantes et pratiques sont seulement dans VSCode (les environement distant Docker et SSH par exemple)
- Un fork récent et complètement opensource de VSCode qui peut fonctionner directement dans le navigateur (Cf. gitpod.io). Moins mature.

Ces trois logiciels sont très proches et vous pouvez coder vos extensions (compatibles avec les 3) pour étendre ces éditeur.

Il me semble important pour choisir un outil de se demander si on possède l'outil ou si l'outil nous possède (plus ou moins les deux en général). Pour pouvoir gérér la complexité du développement moderne on dépend de pas mal d'outils. Savoir choisir des outils ouverts et savoir utiliser également les outils en ligne commande (`git`, `pylint`, etc cf. suite du cours) est très important pour ne pas s'enfermer dans un environnement limitant et possessif.

## Fonctions, structures de contrôle et le flux d'exécution

### Execution ou branchements conditionnels

Pour pouvoir écrire des applications il faut des techniques permettant de contrôler le déroulement du programme dans différentes directions, en fonction des circonstances. Pour cela, nous devons disposer d’instructions capables de tester une certaine condition et modifier le comportement du programme en conséquence.

La principale instruction conditionnelle est, en python comme dans les autres langages **impératifs**, le `if` (Si condition alors ...) assorti généralement du `else` (Sinon faire ...) et en python de la contraction `elif` de `else if` (Sinon, Si condition alors ...)

```python
a = 0
if a > 0 :
    print("a est positif")
elif a < 0 :
    print("a est négatif")
else:
    print("a est nul")
```

Il existe aussi le `switch` + `case` en python mais on l'utilise peu et vous pouvez éviter de l'utiliser.

#### Indentations

TODO prendre l'explication du livre

#### Opérateurs de comparaison

```python
x == y # x est égal à y
x != y # x est différent de y
x > y # x est plus grand que y
x < y # x est plus petit que y
x >= y # x est plus grand que, ou égal à y
x <= y # x est plus petit que, ou égal à y
```

Les opérateurs de comparaisons permettent comparer deux valeurs python (des nombres mais aussi des objets, comme nous le verrons plus loin) pour renvoyer vrai ou faux (un booléen).

On a également souvent besoin de combiner plusieurs expressions booléennes. On utilise pour cela: `and`, `or` et `not`.

```python
x = 2

print("x > 0:", x > 0) # vrai 
print("x > 0 and x == 2:", x > 0 and x == 2) # vrai et vrai donne vrai
print("x > 0 and x == 1:", x > 0 and x == 2) # vrai et faux donne faux
print("x > 0 or x == 1:", x > 0 or x == 1) # vrai ou faux donne vrai
print("not x == 1:", not x == 1) # non faux donne vrai
print("x > 0 or not x == 1:", x > 0 or not x == 1) # vrai ou (non faux) donne vrai ou vrai donne vrai
```

Les parenthèses sont facultatives mais si on a un doute sur la priorité des opérateurs les mettre rend la chose parfois plus lisible.

```python
print("x > 0 or (not x == 1) :", x > 0 or (not x == 1)) # vrai ou (non faux) donne vrai ou vrai donne vrai
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

Autrement dit en python on aime utiliser la vraisemblance implicite des variables pour tester si leur valeur est significative/initialisée ou non.

TODO Nous verrons dans la partie sur le `Python Data Model` que cela implique des choses pour nos classes de programmation orientée objet en python (en Résumé on veut que `if monObjet:` soit capable de tester si l'objet est initialisé et utilisable) 

### Répeter des instructions : les boucles et la récursivité

Répéter des opération est le coeur de la puissance de calcul des ordinateur. On peut pour cela utiliser des boucles ou des appels récusifs de fonctions. Les deux boucles python sont `while` et `for`.

#### La boucle while

`while <condition>:` veut dire "tant que la condition est vraie répéter ...". C'est une boucle simple qui teste à chaque tour (avec un if) si on doit continuer de boucler.

Exemple:

```python
a = 0
while (a < 10) # On répète les deux instructions de la boucle tant que a est inférieur à 7
    a = a + 1 # A chaque tour on ajoute 1 à la valeur de a
    print(a)
```

#### La boucle for et les listes

TODO partie iterateur : La boucle `for` en Python est plus puissante et beaucoup plus utilisée que la boucle `while` car elle "s'adapte aux données" et aux objets du programme grâce à la notion d'itérateur que nous détaillerons plus loin. (De ce point de vue, la boucle `for` python est très différente de celle du C/C++ par exemple)

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
print("\n")

for entier in range(1, 11):
    print(entier) # Afficher les 10 nombres de 1 à 10
print("\n")

for entier in range(2, 11, 2):
    print(entier) # Afficher les 5 nombres pairs de 2 à 10 (le dernier paramètre indique d'avancer de 2 en 2)
print("\n")
```

#### Les listes en python

TODO Récupérer le Ne élément d'une liste
TODO append()
TODO lien vers la documentation


### Exercice : Algorithmique et programmation : Coder quelques versions de la suite de fibonacci

TODO mesurer les Performance de notre fonction !

TODO Correction de fibonacci et plus d'exercices algorithmiques: https://www.python-course.eu/python3_recursive_functions.php

## Debug python dans VSCode

TODO

### Exercice : Comprendre le calcul de la suite de fibonacci en récursif et en itératif par le debug

TODO L'appel d'une fonction remplit la pile d'appel comme on le voit bien dans le debugger

#### Mémoïzation avec un décorateur de la librairie functools

TODO

## Indications et vérifications de type en Python : à prendre au sérieux !
### Exercice: ajouter des indications de type dans notre factorielle et utiliser mypy

TODO la suite de cette page devrait être une nouvelle partie et très détaillé

## Collections et itérateurs en Python

https://docs.python.org/fr/3/library/itertools.html

#### Les compréhensions de liste et de dictionnaire

```python
lst = [10, 50, 75, 83, 98, 84, 32] 
 
[print(x) for x in lst] 
```

### Exercice: Itération sur une donnée XML

TODO trier et réformater la suite

users = etree.Element("users")
user = etree.SubElement(users, "user")
user.set("data-id", "101")
nom = etree.SubElement(user, "nom")
nom.text = "Zorro"
metier = etree.SubElement(user, "metier")
metier.text = "Danseur"
print(etree.tostring(users, pretty_print=True))

Résultat

<users>
    <user data-id="101">    
        <nom>Olivier</nom>
        <metier>Danseur</metier> 
    </user>
</users>

Code pour arriver au document XML initial:

users = etree.Element("users")

users_data = [
("101", "Zorro", "Danseur"),
("102", "Hulk", "Footballeur"),
("103", "Zidane", "Star"),
("104", "Beans", "Epicier"),
("105", "Batman", "Veterinaire"),
("106", "Spiderman", "Veterinaire"),
]

for user_data in users_data:
    user = etree.SubElement(users, "user")
    user.set("data-id", user_data[0])
    nom = etree.SubElement(user, "nom")
    nom.text = user_data[1]
    metier = etree.SubElement(user, "metier")
    metier.text = user_data[2]


print(etree.tostring(users, pretty_print=True))

Les méthodes des noeuds

Si vous voulez approfondir vos connaissances sur la lib lxml , il existe plein d'autres méthodes associées aux noeuds que vous pouvez voir en lançant la commande help( <noeuds> ) . L'aide est en anglais et comme je suis sympa, je vous l'ai traduite:

addnext(element)      : Ajoute un élément en tant que frère juste après l'élément
addprevious(element)  : Ajoute un élément en tant que frère juste avant l'élément
append(element)       : Ajoute un sous-élément à la fin de l'élément 
clear()               : Supprime tous les sous-éléments
extends(elements)     : Etend les éléments passé en paramètre
find(path)            : Recherche le premier sous-élément qui correspond au tag/path
findall(path)         : Recherche tous les sous-éléments qui correspondent au tag/path
findtext(path)        : Trouve le texte du premier sous-élément qui correspond au tag/path
get(key)              : Recupère l'attribut d'un élément
getchildren()         : Retourne tous les enfants d'un élément ! attention déprécié pour list(element)
getnext()             : Retourne le prochain élément frère
getparent()           : Retourne le parent de l'élément
getprevious()         : Retourne l'élément frère précédant
index(child)          : Trouve la position de l'élément
insert(index)         : Insère un sous-élément à la position indiquée
items()               : Retourne les attributs d'un élément (dans un ordre aléatoire)
keys()                : Retourne une liste des noms des attributs
remove(element)       : Supprime l'élément passé en paramètre
replace(el1, el2)     : Remplace el1 par el2
set(key, value)       : Créer un attribut avec une valeur
values()              : Retourne les valeurs des attributs
xpath(path)           : Evalue une expression xpath
