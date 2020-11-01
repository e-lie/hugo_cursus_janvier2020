---
title: 3. Les fonctions
draft: false
weight: 20
---


### Principe

Donner un nom à un ensemble d'instructions pour créer de la **modularité** et de la **sémantique**

```python
def ma_fonction(arg1, arg2):
    instruction1
    instruction2
    ...
    return resultat
```

![](../../../../images/python/fonction.png)

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

### Exemples concrets

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

### Écrire une fonction

#### Éléments de syntaxe

```python
def aire_disque(rayon):
    rayon_carree = rayon ** 2
    return 3.1415 * rayon_carree
```


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


### Erreur classique:

#### Utiliser `print` au lieu de `return`

#### Ce programme n'affiche rien

```python
def aire_disque(rayon):
    rayon_carree = rayon ** 2
    return 3.1415 * rayon_carree

A = aire_disque(6)      # A vaut bien quelque chose
                        # mais nous ne demandons pas de l'afficher ...
```

#### Solution naive : remplacer le `return` par un `print`

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


### Appel de fonction avec arguments explicites

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


### Arguments optionnels

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

### Exemple réaliste

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

