---
title: Cours 1 - Révisions variables, fonction, structures de contrôle
draft: false
weight: 20
---


## Setup de développement

- VM Linux
- Python (3.x)

### Pour débutter

- **Thonny** : `apt install thonny`

### Plus tard

- **Vim** (éditeur en console pour ninjas)
- **Atom** (IDE relativement minimaliste, épuré et extensible)
- **Pycharm** (IDE très gros qui fait même le café)
- ???


## 0. Hello world !

Dans Thonny :

```python
print("Hello, world!")
```


## Parenthèse : Python 2 vs Python 3

- Python 2 existe depuis 2000
- Python 3 existe depuis 2008
- Fin de vie de Python 2 en 2020
- ... mais encore la version par défaut dans de nombreux système ... (c.f. `python --version`)

il faut lancer `python3` explicitement ! <small>(et non `python`)</small>

### Différences principales

- `print "toto"` ne fonctionnera pas en Python 3 (utiliser `print("toto")`
- Nommage des paquets debian (`python-*` vs `python3-*`)
- Gestion de l'encodage
- `range`, `xrange`
- Disponibilité des librairies ?

Il existe des outils comme 2to3 pour ~automatiser la transition


## 0. Executer du code Python (1/2)

### Executer un script explicitement avec python

```bash
$ python3 hello.py
```

### ou implicitement (shebang)

```python
#!/usr/bin/env python3

print("Hello, world!")
```

puis on rend le fichier executable et on l'execute

```bash
$ chmod +x hello.py
$ ./hello.py
```


## 0. Executer du code Python (2/2)

### En interactif

```bash
$ python3
>>> print("Hello, world!")
```

#### `ipython3` : alternative à la console 'classique'

```bash
$ sudo apt install ipython3
$ ipython3
In [1]: print("Hello, world!")
```

#### pour quitter : `exit`




## 1. Les variables


### 1.1. Exemple

```python
message = "Je connais la réponse à l'univers, la vie et le reste"
reponse = 6 * 7

print(message)
print(reponse)
```



![](../../images/sorcery.jpg)



### 1.2. Principe

- Les variables sont des abstractions de la mémoire
- Un contenant pour une information : nom + contenu
- Différent du concept mathématique

![](../../images/memory1.png)



### 1.2. Principe

- Les variables sont des abstractions de la mémoire
- Un contenant pour une information : nom + contenu
- Différent du concept mathématique

![](../../images/memory2.png)



### 1.3. Déclaration, utilisation

- En python : déclaration implicite
- Ambiguité : en fonction du contexte, `x` désigne soit le contenant, soit le contenu...

```python
x = 42     # déclare (implicitement) une variable et assigne une valeur
x = 3.14   # ré-assigne la variable avec une autre valeur
y = x + 2  # déclare une autre variable y, à partir du contenu de x
print(y)   # affichage du contenu de y
```

### 1.4 Nommage

- Caractères autorisés : caractères alphanumériques (`a-zA-Z0-9`) et `_`.
- **Les noms sont sensibles à la casse** : `toto` n'est pas la même chose que `Toto`!
- (Sans commencer par un chiffre)



### 1.5 Comparaison de différentes instructions

Faire un calcul **sans l'afficher ni le stocker nul part**:
```python
6*7
```

Faire un calcul et **l'afficher dans la console**:
```python
print(6*7)
```

Faire un calcul et **stocker le résultat dans une variable `r`** pour le réutiliser plus tard
```python
r = 6*7
```



### 1.6 Opérations mathématiques

```python
2 + 3   # Addition
2 - 3   # Soustraction
2 * 3   # Multiplication
2 / 3   # Division
2 % 3   # Modulo
2 ** 3  # Exponentiation
```



### 1.6 Calcul avec réassignation

```python
x += 3   # Équivalent à x = x + 3
x -= 3   # Équivalent à x = x - 3
x *= 3   # Équivalent à x = x * 3
x /= 3   # Équivalent à x = x / 3
x %= 3   # Équivalent à x = x % 3
x **= 3  # Équivalent à x = x ** 3
```



### 1.7 Types

```python
42            # Entier / integer               / int
3.1415        # Réel                           / float
"Alex"        # Chaîne de caractère (string)   / str
True / False  # Booléen                        / bool
None          # ... "rien" / aucun (similar à `null` dans d'autres langages)
```

Connaître le type d'une variable : `type(variable)`



### 1.8 Conversion de type

```python
int("3")      -> 3
str(3)        -> "3"
float(3)      -> 3.0
int(3.14)     -> 3
str(3.14)     -> "3.14"
float("3.14") -> 3.14
int(True)     -> 1
int("trois")  -> Erreur / Exception
```



## 2. Interactivité basique



En terminal, il est possible de demander une information à l'utilisateur
avec `input("message")`

```python
reponse = input("Combien font 6 fois 7 ?")
```

N.B. : ce que renvoie `input()` est une chaîne de caractère !



<br>
<br>
<br>
Demo dans Thonny



## 3. Chaînes de caractères

![](../../images/string.png)


#### Syntaxe des chaînes

- Entre simple quote (`'`) ou double quotes (`"`). Par exemple: `"hello"`
- `print("hello")` affiche le texte `Hello`
- `print(hello)` affiche **le contenu d'une variable qui s'apellerait** `Hello`

#### Longueur

```python
m = "Hello world"
len(m)        # -> 11
```



![](../../images/string.png)

#### Extraction

```python
m[:5]    # -> 'Hello'
m[6:8]   # -> 'wo'
m[-3:]   # -> 'rld'
```

#### Multiplication

```python
"a" * 6    # -> "aaaaaa"
```




#### Concatenation

```python
"Cette phrase" + " est en deux morceaux."
```

```python
name = "Alex"
age = 28
"Je m'appelle " + name + " et j'ai " + str(age) + " ans"
```
#### Construction à partir de données, avec `%s`

```python
"Je m'appelle %s et j'ai %s ans" % ("Alex", 28)
```

#### Construction à partir de données, avec `format`

```python
"Je m'appelle {name} et j'ai {age} ans".format(name=name, age=age)
```

#### Substitution

```python
"Hello world".replace("Hello", "Goodbye")   # -> "Goodbye world"
```

#### Chaînes sur plusieurs lignes

- `\n` est une syntaxe spéciale faisant référence au caractère "nouvelle ligne"

```python
"Hello\nworld"     # -> Hello <nouvelle ligne> world
```

#### Et bien d'autres choses !

c.f. documentation, e.g `https://devdocs.io/python~3.7/library/stdtypes#str`


## 4. Fonctions

### 4.1 Principe

Donner un nom à un ensemble d'instructions pour créer de la **modularité** et de la **sémantique**

```python
def ma_fonction(arg1, arg2):
    instruction1
    instruction2
    ...
    return resultat
```

    ![](../../images/fonction.png)

On peut ensuite utiliser la fonction avec les arguments souhaitées et récupérer le resultat :

```python
mon_resultat = ma_fonction("pikachu", "bulbizarre")
autre_resultat = ma_fonction("salameche", "roucoups")
```


##### **Calculs mathématiques**

```python
sqrt(2)        -> 1.41421 (environ)
cos(3.1415)    -> -1 (environ)
```

##### **Générer ou aller chercher des données**

```python
nom_du_departement(67)        -> "Bas-rhin"
temperature_actuelle("Lyon")  -> Va chercher une info sur internet et renvoie 12.5
```

##### **Convertir, formatter, filtrer, trier des données ...**

```python
int("3.14")                     -> 3
normalize_url("toto.com/pwet/") -> https://toto.com/pwet
sorted(liste_de_prenoms)     -> renvoie la liste triée alphabétiquement
```

##### **Afficher / demander des données **

```python
print("un message")
input("donne moi un chiffre entre 1 et 10 ?")
```

### 4.2 Exemples concrets

```python
def aire_triangle(base, hauteur):
    return base * hauteur / 2

A1 = aire_triangle(3, 5)      # -> A1 vaut 15 !
A2 = aire_triangle(4, 2)      # -> A2 vaut 8 !


def aire_disque(rayon):
    rayon_carree = rayon ** 2
    return 3.1415 * rayon_carree

A3 = aire_disque(6)           # -> A3 vaut (environ) 113 !

def aire_triangle(base, hauteur):
    return base * hauteur / 2

A1 = aire_triangle(3, 5)      # -> A1 vaut 15 !
A2 = aire_triangle(4, 2)      # -> A2 vaut 8 !


def aire_disque(rayon):
    rayon_carree = rayon ** 2
    return 3.1415 * rayon_carree

A3 = aire_disque(6)           # -> A3 vaut (environ) 113


def volume_cylindre(rayon, hauteur):
    return hauteur * aire_disque(rayon)

V1 = volume_cylindre(6, 4)   # -> A4 vaut (environ) 452
```

### 4.3 Écrire une fonction

#### Éléments de syntaxe

```python
def aire_disque(rayon):
    rayon_carree = rayon ** 2
    return 3.1415 * rayon_carree
```

![](../../images/fonction.png)


- `def`, `:`
- des instructions **indentées** !!
- des arguments (ou pas!)
- `return` (ou pas)


#### Les arguments

```python
def aire_disque(rayon):
    # [ ... ]
```

- Une fonction est un traitement *générique*. **On ne connait pas à l'avance la valeur précise qu'aura un argument**, et généralement on appelle la fonction pleins de fois avec des arguments différents...
- En **définissant** la fonction, on travaille donc avec un **argument** "abstrait" nommé `rayon`
- Le nom `rayon` en tant qu'argument de la fonction **n'a de sens qu'a l'intérieur de cette fonction** !
- En **utilisant** la fonction, on fourni la valeur pour `rayon`, par exemple: `aire_disque(6)`.


#### Les variables locales

```python
def aire_disque(rayon):
    rayon_carree = rayon ** 2
    # [ ... ]
```

- Les variables créées dans la fonction sont **locales**: elles n'ont de sens qu'a l'intérieur de la fonction
- Ceci dit, cela ne m'empêche pas d'avoir des variables aussi nommées `rayon` ou `rayon_carree` dans une autre fonction ou dans la portée globale (mais ce ne sont pas les mêmes entités)


### Le `return`

```python
def aire_disque(rayon):
    rayon_carree = rayon ** 2
    return 3.1415 * rayon_carree
```

- `return` permet de **récupérer le résultat de la fonction**
- C'est ce qui donne du sens à `A = aire_disque(6)` (il y a effectivement un résultat à mettre dans `A`)
- Si une fonction n'a pas de `return`, elle renvoie `None`
- `return` **quitte immédiatement la fonction**


#### ... regardons tout cela dans VSCode ...!

##### et discutons des erreurs classiques


### 4.4 Erreur classique:

#### utiliser `print` au lieu de `return`

##### Ce programme n'affiche rien

```python
def aire_disque(rayon):
    rayon_carree = rayon ** 2
    return 3.1415 * rayon_carree

A = aire_disque(6)      # A vaut bien quelque chose
                        # mais nous ne demandons pas de l'afficher ...
```

##### Solution naive : remplacer le `return` par un `print`

```python
def aire_disque(rayon):
    rayon_carree = rayon ** 2
    print(3.1415 * rayon_carree)    # Affiche le résultat dans la console

A = aire_disque(6)   # Mais maintenant A vaut None
                     # car la fonction n'a pas utilisé `return`
```


##### "Bonne" solution


```python
def aire_disque(rayon):
    rayon_carree = rayon ** 2
    return 3.1415 * rayon_carree

A = aire_disque(6)   # Stocker le résultat dans A
print(A)             # Demander d'afficher A dans la console
```


Ceci dit, **il peut être tout à fait légitime de mettre des `print`** dans une fonction, par exemple pour la débugger...!


### 4.5 Appel de fonction avec arguments explicites

```python
def aire_triangle(base, hauteur):
    return base * hauteur / 2

A1 = aire_triangle(3, 5)
A2 = aire_triangle(4, hauteur=8)
A3 = aire_triangle(hauteur=6, base=2)
A4 = aire_triangle(hauteur=3, 2)    # < Pas possible !
```

N.B. : cette écriture est aussi plus explicite / lisible / sémantique:

```python
aire_triangle(base=3, hauteur=5)
```

que juste

```python
aire_triangle(3, 5)
```

On peut se retrouver dans des situations comme:

```python
base = 3
hauteur = 5

A1 = aire_triangle(base=base, hauteur=hauteur)
```

Dans l'appel de la fonction :
- le premier `base` est **le nom de l'argument de la fonction `aire_triangle`**,
- le deuxième `base` corresponds au **contenu de la variable nommée `base`**.


### 4.6 Arguments optionnels

Les arguments peuvent être rendu optionnels si ils ont une valeur par défaut :

```python
def distance(dx, dy=0, dz=0):
    [...]
```

Dans ce cas, tous ces appels sont valides :

```python
distance(5)
distance(2, 4)
distance(5, 8, 2)
distance(9, dy=5)
distance(0, dz=4)
distance(1, dy=1, dz=9)
distance(2, dz=4, dy=7)
```

#### Exemple réaliste

```python
subprocess.Popen(args,
                 bufsize=0,
                 executable=None,
                 stdin=None,
                 stdout=None,
                 stderr=None,
                 preexec_fn=None,
                 close_fds=False,
                 shell=False,
                 cwd=None,
                 env=None,
                 universal_newlines=False,
                 startupinfo=None,
                 creationflags=0)
```

c.f. `https://docs.python.org/2/library/subprocess.html#subprocess.Popen`



# 7. Les boucles



Répéter plusieurs fois un même ensemble d'instruction

- pour plusieurs valeurs (`for`)
- tant qu'une condition est remplie (`while`)



## 7.1 Les boucles `for`

```python
for i in range(0,10):
    print("En ce moment, i vaut " + str(i))
```

affiche :
```python
En ce moment, i vaut 0
En ce moment, i vaut 1
En ce moment, i vaut 2
...
En ce moment, i vaut 9
```


## 7.2 `continue` et `break`

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


## 7.3 Boucle `while`

(un peu moins utilisé que `for`)

```python
x = 40
while x % 2 == 0:
    print(str(x) + " est pair !")
    x = x/2

print(str(x) + " est impair !")
```


## Posture de développeur et bonnes pratiques

- Lorsqu'on écrit du code, la partie "tester" et "debugger" fait partie du job.

**On écrit pas un programme qui marche au premier essai**

- Il faut tester et débugger **au fur et à mesure**, **pas tout d'un seul coup** !


### Écrire un programme ... pour qui ? pour quoi ?

- Le fait qu'un programme marche est "secondaire" !
- ... Mieux vaut un programme cassé mais lisible (donc débuggable)
- ... qu'un programme qui marche mais incompréhensible (donc fragile et/ou qu'on ne saura pas faire évoluer)

Autrement dit : **la lisibilité pour vous et vos collègues a énormément d'importance pour la maintenabilité et l'évolution du projet**

### Bonnes pratiques pour la lisibilité, maintenabilité

- **Keep It Simple**
- **Sémantique** : utiliser des noms de variables et de fonctions **qui ont du sens**
- **Architecture** : découper son programme en fonction qui chacune résolvent un sous-problème précis
- **Robustesse** : garder ses fonctions autant que possibles indépendantes, limiter les effets de bords
    - lorsque j'arose mes plantes, ça ne change pas la température du four


- Lorsque mon programme évolue, **je prends le temps de le refactoriser si nécessaire**
    - si je répète plusieurs fois les mémes opérations, il peut être intéressant d'introduire une nouvelle fonction
    - si le contenu d'une variable ou d'une fonction change, peut-être qu'il faut modifier son nom
    - si je fais pleins de petites opérations bizarre, peut-être qu'il faut créer une fonction

### Ne pas réinventer la roue

Il y a des tas de problème qui ont déjà été résolu par d'autres développeurs et ont créé des bibliothèques !

 Par exemple :
- fonctions mathématiques (cos, sqrt, ...)
- fonctions cryptographiques (hash de mot de passe, ...)
- lecture / parsing de fichier divers (JSON, YAML, CSV, HTML, XLS, ...)

Généralement lorsqu'on réinvente la roue:
- on perd du temps
- on le fais moins bien que les bibliothèques existantes
- on créé des risques de sécurité


### Quelques programmes réels utilisant Python 

#### Dropbox

![](../../images/dropbox.png)

#### Atom

![](../../images/atom.png)

#### Eve online

![](../../images/eveonline.jpg)

#### Matplotlib

![](../../images/matplotlib.png)

#### Blender

![](../../images/blender.jpg)

#### OpenERP / Odoo

![](../../images/odoo.jpg)

#### Tartiflette

![](../../images/tartiflette.png)
