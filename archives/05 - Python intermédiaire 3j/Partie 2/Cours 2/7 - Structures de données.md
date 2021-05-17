---
title: 7. Structures de données
draft: false
weight: 20
---

Les structures de données permettent de stocker des séries d'information et d'y accéder (plus ou moins) facilement et rapidement.



## Les listes
Une collection d'éléments **ordonnés** référencé par un indice

```python
animaux_favoris = [ "girafe", "chenille", "lynx" ]
fibonnaci = [ 1, 1, 2, 3, 5, 8 ]
stuff = [ 3.14, 42, "bidule", ["a", "b", "c"] ]
```

### Accès à element particulier ou a une "tranche"

```python
animaux_favoris[1]      ->  "chenille"
animaux_favoris[-2:]    ->  ["chenille", "lynx"]
```


### Longueur

```python
len(animaux_favoris)    -> 3
```


### Tester qu'un élément est (ou n'est pas) dans une liste

```python
"lynx" in animaux_favoris   # -> True
"Mewtwo" not in animaux_favoris   # -> True
```


```python
animaux_favoris = [ "girafe", "chenille", "lynx" ]
```


#### Iteration

```python
for animal in animaux_favoris:
    print(animal + " est un de mes animaux préférés !")
```


#### Iteration avec index

```python
print("Voici la liste de mes animaux préférés:")
for i, animal in enumerate(animaux_favoris):
    print(str(i+1) + " : " + animal)
```


```python
animaux_favoris = [ "girafe", "chenille", "lynx" ]
```


#### Modification d'un élément

```python
animaux_favoris[1] = "papillon"
```

#### Ajout à la suite, contatenation

```python
animaux_favoris.append("coyote")
```

#### Insertion, concatenation

```python
animaux_favoris.insert(1, "sanglier")
animaux_favoris += ["lion", "moineau"]
```


### Exemple de manip classique : filtrer une liste pour en construire une nouvelle

```python
animaux_favoris = [ "girafe", "chenille", "lynx" ]

# Création d'une liste vide
animaux_starting_with_c = []

# J'itère sur la liste de pokémons favoris
for animal in animaux_favoris:

   # Si le nom de l'animal actuel commence par c
   if animal.startswith("c"):

      # Je l'ajoute à la liste
      animaux_starting_with_B.append(animal)
```


À la fin, `animaux_starting_with_c` contient:

```python
["girafe"]
```


#### Transformation de string en liste

```python
"Hello World".split()    -> ["Hello", "World"]
```

#### Transformation de liste en string

```python
' | '.join(["a", "b", "c"])      -> "a | b | c"
```



## Les dictionnaires

Une collection **non-ordonnée** (apriori) de **clefs** a qui sont associées des **valeurs**

```python
phone_numbers = { "Alice":   "06 93 28 14 03",
                  "Bob":     "06 84 19 37 47",
                  "Charlie": "04 92 84 92 03"  }
```

### Accès à une valeur

```python
phone_numbers["Charlie"]        -> "04 92 84 92 03"
phone_numbers["Marius"]           -> KeyError !
phone_numbers.get("Marius", None) -> None
```

### Modification d'une entrée, ajout d'une nouvelle entrée

```python
phone_numbers["Charlie"] = "06 25 65 92 83"
phone_numbers["Deborah"] = "07 02 93 84 21"
```


### Tester qu'une clef est dans le dictionnaire

```python
"Marius" in phone_numbers    # -> False
"Bob" not in phone_numbers # -> False
```

```python
phone_numbers = { "Alice":   "06 93 28 14 03",
                  "Bob":     "06 84 19 37 47",
                  "Charlie": "04 92 84 92 03"  }
```

### Iteration sur les clefs

```python
for prenom in phone_numbers:     # Ou plus explicitement: phone_numbers.keys()
    print("Je connais le numéro de "+prenom)
```


### Iteration sur les valeurs

```python
for phone_number in phone_numbers.values():
    print("Quelqu'un a comme numéro " + phone_number)
```


### Iterations sur les clefs et valeurs

```python
for prenom, phone_number in phone_numbers.items():
    print("Le numéro de " + prenom + " est " + phone_number)
```


## Construction plus complexes

Liste de liste, liste de dict, dict de liste, dict de liste, ...

```python
contacts = { "Alice":  { "phone": "06 93 28 14 03",
                         "email": "alice@megacorp.eu" },

             "Bob":    { "phone": "06 84 19 37 47",
                         "email": "bob.peterson@havard.edu.uk" },

             "Charlie": { "phone": "04 92 84 92 03" } }
```


```python
contacts = { "Alice":  { "phone": "06 93 28 14 03",
                         "email": "alice@megacorp.eu" },

             "Bob":    { "phone": "06 84 19 37 47",
                         "email": "bob.peterson@harvard.edu.uk" },

             "Charlie": { "phone": "04 92 84 92 03" } }
```


### Recuperer le numero de Bob

```python
contacts["Bob"]["phone"]   # -> "06 84 19 37 47"
```


### Ajouter l'email de Charlie

```python
contacts["Charlie"]["email"] = "charlie@orange.fr"
```


### Ajouter Deborah avec juste une adresse mail

```python
contacts["Deborah"] = {"email": "deb@hotmail.fr"}
```



## Les sets

Les `set`s sont des collections d'éléments **unique** et **non-ordonnée**

```python
chat = set(["c", "h", "a", "t"])        # -> {'h', 'c', 'a', 't'}
chien = set(["c", "h", "i", "e", "n")   # -> {'c', 'e', 'i', 'n', 'h'}
chat - chien                            # -> {'a', 't'}
chien - chat                            # -> {'i', 'n', 'e'}
chat & chien                            # -> {'h', 'c'}
chat | chien                            # -> {'c', 't', 'e', 'a', 'i', 'n', 'h'}
chat.add("z")                           # ajoute `z` à `chat`
```



## Les tuples

Les tuples permettent de stocker des données de manière similaire à une liste, mais de manière **non-mutable**.
Generalement itérer sur un tuple n'a pas vraiment de sens...

Les tuples permettent de **grouper des informations ensembles**.
Typiquement : des coordonnées de point.

```python
xyz = (2,3,5)
xyz[0]        # -> 2
xyz[1]        # -> 3
xyz[0] = 5    # -> Erreur!
```

Autre exemple `dictionnaire.items()` renvoie une liste de tuple `(clef, valeur)` :

```python
[ (clef1, valeur1), (clef2, valeur2), ... ]
```



## List/dict comprehensions

Les "list/dict comprehensions" sont des syntaxes particulière permettant de rapidement construire des listes (ou dictionnaires) à partir d'autres structures.

### Syntaxe (list comprehension)

```python
[ new_e for e in liste if condition(e) ]
```

### Exemple (list comprehension)

Carré des entiers impairs d'une liste

```python
[ e**2 for e in liste if e % 2 == 1 ]
```


## List/dict comprehensions

Les "list/dict comprehensions" sont des syntaxes particulière permettant de rapidement construire des listes (ou dictionnaires) à partir d'autres structures.

### Syntaxe (dict comprehension)

```python
{ new_k:new_v for k, v in d.items() if condition(k, v) }
```

### Exemple (dict comprehension)
 

```python
{ nom: age-20 for nom, age in ages.items() if age >= 20 }
```

## Générateurs

(Pas vraiment une structure de données, mais c'est lié aux boucles ...)

- Une fonction qui renvoie **des** résultats "au fur et à mesure" qu'ils sont demandés ...
- Se comporte comme un itérateur
- Peut ne jamais s'arrêter ...!
- Typiquement, évite de créer des listes intermédiaires


### exemple SANS generateur

```python
mes_animaux = { "girafe": 300,    "coyote": 50,
                 "chenille": 2,       "cobra": 45
                 # [...]
               }

def au_moins_un_metre(animaux):

    output = []
    for animal, taille in animaux.items():
        if taille >= 100:
            output.append(animal)

    return output

for animal in au_moins_un_metre(mes_animaux):
   ...
```

### exemple AVEC generateur

```python
mes_animaux = { "girafe": 300,    "coyote": 50,
                 "chenille": 2,       "cobra": 45
                 # [...]
               }

def au_moins_un_metre(animaux):

    for animal, taille in animaux.items():
        if taille >= 100:
            yield animal

for animal in au_moins_un_metre(mes_animaux):
   ...
```

Il n'est pas nécessaire de créer la liste intermédiaire `output`


### Un autre exemple

```python
def factorielle():

   n = 1
   acc = 1

   while True:
       acc *= n
       n += 1

       yield acc
```
