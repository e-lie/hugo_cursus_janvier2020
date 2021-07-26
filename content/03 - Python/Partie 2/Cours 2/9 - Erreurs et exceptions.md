---
title: 9. Erreurs et exceptions
draft: false
weight: 20
---


En Python, lorsqu'une erreur se produit ou qu'un cas particulier empêche (a priori) la suite du déroulement normal d'un programme ou d'une fonction, une *exception* est déclenchée

Attention : différent des erreurs de syntaxe

### Exemple d'exceptions

- Utiliser une variable qui n'existe pas
- Utiliser `int()` sur quelque chose qui ne peut pas être converti en entier
- Diviser un nombre par zero
- Diviser un nombre par une chaine de caractère
- Tenter d'accéder à un élément d'une liste qui n'existe pas
- Tenter d'ouvrir un fichier qui n'existe pas ou qu'on ne peut pas lire
- Tenter de télêcharger des données sans être connecté à internet
- etc...

- Une exception a un *type* (c'est un objet d'un classe d'exception -> cf. Partie 3):
    - `Exception`, `ValueError`, `IndexError`, `TypeError`, `ZeroDivisionError`, ...
- Lorsqu'une exception interrompt le programme, l'interpréteur affiche la *stacktrace* (TraceBack) qui contient des informations pour comprendre quand et pourquoi l'exception s'est produite.

```python
Traceback (most recent call last):
  File "coucou.py", line 3, in <module>
    print(coucou)
NameError: name 'coucou' is not defined
```

```python
# python3 test_int.py

Tapez un entier entre 1 et 3: truc

Traceback (most recent call last):
  File "test_int.py", line 8, in <module>
    demander_nombre()
  File "test_int.py", line 4, in demander_nombre
    r = int(input("Tape un entier entre 1 et 3: "))
ValueError: invalid literal for int() with base 10: 'truc'
```

Souvent une exception est due à une entrée utilisateur incorrecte (comme ici) mais pas toujours.

## `raise`

Il est possible de déclencher ses propres exceptions à l'aide de `raise`

```python
def max(liste_entiers):
    if liste_entiers == []:
        raise Exception("max() ne peut pas fonctionner sur une liste vide!")
```

(Ici, le type utilisé est le type générique `Exception`)



Autre exemple:

```python
def envoyer_mail(destinataire, sujet, contenu):
    if '@' not in destinataire:
        raise Exception('Une adresse mail doit comporter un @ !')
```

(Ici, le type utilisé est le type générique `Exception`)



## `try`/`except`

De manière générale dans un programme, il peut y'avoir beaucoup de manipulation dont on sait qu'elles peuvent échouer pour un nombre de raisons trop grandes à lister ...

Par exemple : écrire dans un fichier

- Est-ce que le programme a la permission d'écrire dans ce fichier ?
- Est-ce qu'aucun autre programme n'est en train d'écrire dans ce fichier ?
- Est-ce qu'il y a assez d'espace disque libre ?
- Si je commence à écrire, peut-être vais-je tomber sur un secteur disque deffectueux
- ...

Autre exemple : aller chercher une information sur internet

- Est-ce que je suis connecté à Internet ?
- Est-ce que la connection est suffisament stable et rapide ?
- Est-ce que le programme a le droit d'effectuer d'envoyer des requêtes ?
- Est-ce qu'un firewall va bloquer ma requête ?
- Est-ce que le site que je veux contacter est disponible actuellement ?
- Est-ce que le certificat SSL du site est à jour ?
- Quid de si la connexion est perdue en plein milieu de l'échange ?
- ...


En Python, il est courant d'« essayer » des opérations puis de gérer les
exceptions si elles surviennent.

On utilise pour cela des `try: ... except: ...`.

### Exemple

```python
reponse = input("Entrez un entier svp !")

try:
    n = int(reponse)
except:
    raise Exception("Ce n'est pas un entier !")
```


### Utilisation différente

```python
reponse = input("Entrez un entier svp !")

try:
    n = int(reponse)
except:
    n = -1
```


```python
while True:
    reponse = input("Entrez un entier svp !")

    try:
        n = int(reponse)
        break
    except:
        # Faire en sorte de boucler pour reposer la question à l'utilisateur ...
        print("Ce n'est pas un entier !")
        continue
```

## Autre exemple (inhabituel):

#### On peut utiliser les exception comme une sorte de if ou inversement

```python
def can_be_converted_to_int(stuff):
    try:
        int(stuff)
    except:
        return False

    return True

can_be_converted_to_int("3")    # -> True
can_be_converted_to_int("abcd") # -> False
```



## The "python way"
### « Better to ask forgiveness than permissions »

Traduction "on essaye et puis on voit et on gère les dégats".
(ça se discute)

## Assertions

Il est possible d'utiliser des `assert`ions pour **expliciter certaines hypothèses**
faites pendant l'écriture du code. Si elles ne sont pas remplies, une exception est déclenchée.

Un peu comme un `"if not condition raise error"`.

```python
def max(liste_entiers):
    assert liste_entiers != [], "max() ne peut pas fonctionner sur une liste vide!"
```

(`assert toto` est équivalent à `if not toto: raise Exception()`)


```python
def distance(x=0, y=0):
    assert isinstance(x, (int, float)), "Cette fonction ne prends que des int ou float en argument !"
    assert isinstance(y, (int, float)), "Cette fonction ne prends que des int ou float en argument !"

    return racine_carree(x*x + y*y)
```


```python
def some_function(n):
    assert n, "Cette fonction n'accepte pas 0 ou None comme argument !"
    assert n % 2 == 0, "Cette fonction ne prends que des entiers pairs en argument !"

    [...]
```


## Assertions et tests unitaires

En pratique, l'une des utilisations les plus courantes de `assert` est l'écriture de tests unitaires qui permettent de valider qu'une fonction marche dans tous les cas (et continue à marcher si on la modifie)

Dans votre application:

```python
def trier(liste_entiers):
    # on définie le comportement de la fonction
```

Dans les tests (fichier à part):

```python
assert trier([15, 8, 4, 42, 23, 16]) == [4, 8, 15, 16, 23, 42]
assert trier([0, 82, 4, -21, 2]) == [-21, 0, 2, 4, 82]
assert trier([-7, -3, 0]) == [-7, -3, 0]
assert trier([]) == []
```
Cf. Chapitre 19

## Calcul du max d'un liste d'entier : plusieurs approches !

Attention : dans les exemples suivant je dois penser au cas où `resultat` peut valoir `None`

> Je **soupçonne fortemment** que `ma_liste` puisse ne pas être une liste ou puisse être vide

#### Soit je teste explicitement avant pour être sur (moins pythonique) !

```python
if not isinstance(ma_liste, list) or ma_liste == []:
    resultat = None
else:
    resultat = max(ma_liste)
```

> Ça devrait marcher, mais j'ai un doute ... 

#### Soit j'essaye et je gère les cas d'erreur (plus pythonique)!


```python
try:
    resultat = max(ma_liste)
except ValueError as e:
    print("Warning : peut-etre que ma_liste n'etait pas une liste non-vide ?")
    resultat = None
```

#### Soit j'assert le test pour laisser la fonction appelante le soin de gérer l'entrée correctement 

> Normalement `ma_liste` est une liste non-vide, sinon il y a un très gros problème avant dans le programme...

```python
assert isinstance(ma_liste, list) and ma_liste != []

resultat = max(ma_liste)
```

Dans ce cas la fonction 