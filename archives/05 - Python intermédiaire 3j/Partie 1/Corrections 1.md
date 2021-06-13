---
title: Corrections 1
draft: false
weight: 20
---


### Exercice 1

```python
result_1 = 567 * 72
result_2 = 33**4
result_3 = 98.2 / 6
result_4 = (7 * 9)**4 / 6

print(result_1)
print(result_2)
print(result_3)
print(result_4)
```

### Exercice 2

```python
annee = int(input('Quelle est votre année de naissance ?\n'))
age = 2019 - annee + 2
print("Dans deux ans vous aurez {} ans.".format(age))
```

### 3.1 Compter les lettres

```python
mot = input("Donnez moi un mot.\n")
print("Ce mot fait {} caractères (espaces inclus).".format(len(mot)))
```

### 3.2 Encadrer le mot avec ##

```python
mot_encadre = '#### ' + mot + ' ####'
print("mot encadré: {}".format(mot_encadre))
```


### Exercice 4. fonctions de centrage

```python
def centrer(mot, largeur=80):
    
	nb_espaces = largeur - len(mot) - 2
    nb_espaces_gauche = nb_espaces // 2     # division entière:  25 // 2 -> 12
    nb_espaces_droite = nb_espaces - nb_espaces_gauche

    resultat = "|" + nb_espaces_gauche * " " + mot + nb_espaces_droite * " " + "|"

    return resultat
    
    
def encadrer(mot, largeur=80, caractere='@'):
	ligne1 = caractere * largeur
    ligne2 = centrer(mot, largeur)
    
    return "{}\n{}\n{}".format(ligne1, ligne2, ligne1)
	

print(centrer("Pikachu"))
print(len(centrer("Pikachu")) # 80
print(centrer("Pikachu", 40))

print(encadrer("Pikachu"))
print(encadrer("Pikachu", 37))
print(encadrer("Pikachu", 71, "#"))
```


### Exercice 5. conditions

```python
def centrer(mot, largeur=80):
	nb_espaces = largeur - len(mot) - 2
    nb_espaces_gauche = nb_espaces // 2     # division entière:  25 // 2 -> 12
    nb_espaces_droite = nb_espaces - nb_espaces_gauche

    resultat = "|" + nb_espaces_gauche * " " + mot + nb_espaces_droite * " " + "|"

    return resultat
    
    
def encadrer(mot, largeur=80, caractere='@'):
	if largeur == -1:
    	largeur = len(mot) + 4
	
    if caractere == '':
    	return centrer(mot, largeur)
    
    longueur_max = largeur - 4
    if len(mot) > longueur_max:
    	mot = mot[:longueur_max]
        
 	ligne1 = caractere * largeur
    ligne2 = centrer(mot, largeur).replace("|", caractere)
    
    return "{}\n{}\n{}".format(ligne1, ligne2, ligne1)
	

print(encadrer("Pikachu", -1))
print(encadrer("Pikachu", 34, ''))
print(encadrer("Pikachu", 8, '@'))
```

### Exercice 6

```python
from timeit import default_timer as timer
from functools import lru_cache as cache

def fib_rec_naive(n):
    """
    fib_rec_naive calcule le Ne terme de la suite de fibonacci
    En utilisant une approche récursive naive de complexité exponentielle
    """
    if n == 0:
        return 0
    elif n == 1:
        return 1
    else:
        return fib_rec_naive(n-1) + fib_rec_naive(n-2)

@cache()
def fib_rec_naive_cache(n):
    """
    fib_rec_naive calcule le Ne terme de la suite de fibonacci
    En utilisant une approche récursive naive, mais en ajoutant 
    un décorateur de memoïzation qui stocke l'état de la pile d'éxecution entre les appels
    """
    if n == 0:
        return 0
    elif n == 1:
        return 1
    else:
        return fib_rec_naive_cache(n-1) + fib_rec_naive_cache(n-2)

liste_termes_calculés = [0,1]
def fib_rec_liste(n):
    """
    fib_rec_liste calcule le Ne terme de la suite de fibonacci
    En utilisant une approche récursive correcte de complexité linéaire
    en utilisant une mémoire sous forme de liste
    """
    if n < len(liste_termes_calculés):
        return liste_termes_calculés[n]
    else:
        liste_termes_calculés.append(fib_rec_liste(n-1) + fib_rec_liste(n-2))
        return liste_termes_calculés[n]

def fib_iter(n):
    """
    fib_iter calcule le Ne terme de la suite de fibonacci
    En utilisant une approche itérative de complexité linéaire
    """
    ancien_terme, nouveau_terme = 0, 1
    if n == 0:
        return 0

    for i in range(n-1):
        ancien_terme, nouveau_terme = nouveau_terme, ancien_terme + nouveau_terme

    return nouveau_terme


if __name__ == "__main__":

    # Temps avec 35 termes

    start = timer()
    fib_rec_naive(35)
    stop = timer()
    print( "fib_rec_naive(35) execution time: ", stop - start )

    start = timer()
    fib_rec_naive_cache(35)
    stop = timer()
    print( "fib_rec_naive_cache(35) execution time: ", stop - start )

    start = timer()
    fib_rec_liste(35)
    stop = timer()
    print( "fib_rec_list(35) execution time: ", stop - start )

    start = timer()
    fib_iter(35)
    stop = timer()
    print( "fib_iter(35) execution time: ", stop - start )

    # Temps avec 38 termes

    start = timer()
    fib_rec_naive(38)
    stop = timer()
    print( "fib_rec_naive(38) execution time: ", stop - start )

    start = timer()
    fib_rec_naive_cache(38)
    stop = timer()
    print( "fib_rec_naive_cache(38) execution time: ", stop - start )

    start = timer()
    fib_rec_liste(38)
    stop = timer()
    print( "fib_rec_list(38) execution time: ", stop - start )

    start = timer()
    fib_iter(38)
    stop = timer()
    print( "fib_iter(38) execution time: ", stop - start )

```

#### Bonus 2

```python
    
def fibonacci_generator():
    a, b = 0, 1
    yield a
    yield b
    
    while True:
        a, b = (b, a+b)
        yield b

for n in fibonacci_generator():
    if n > 500:
        break
    print(n)

```