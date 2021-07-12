---
title: Corrections 1
draft: false
weight: 20
---


### Exercice 1.1

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

### Exercice 1.2

```python
annee = int(input('Quelle est votre année de naissance ?\n'))
age = 2021 - annee + 2
print("Dans deux ans vous aurez {} ans.".format(age))
```

### 2.1 Compter les lettres

```python
mot = input("Donnez moi un mot.\n")
print("Ce mot fait {} caractères (espaces inclus).".format(len(mot)))
```

### 2.2 Encadrer le mot avec ##

```python
mot_encadre = '#### ' + mot + ' ####'
print("mot encadré: {}".format(mot_encadre))
```


### Exercice 3: Fonctions

3.1
```python
def annee_naissance(age):
  return 2021 - age
print(annee_naissance(32))
```

3.2
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

### Exercice 4: Conditions
4.1
```python
def annee_naissance(age):
  if isinstance(age,int) and 0 < age < 130:
    return 2021-age
```

4.2
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


### Exercice 5
5.1.1
```python
def table_du_7():
  print("Table du 7")
  print("----------")
  for i in range(1,11):
    print("7 x {} = {}".format(i,7*i))
```

5.1.2
```python
def table_multiplication(nombre):
  print("Table du {}".format(nombre))
  print("----------")
  for i in range(1,11):
    print("{} x {} = {}".format(nombre,i,nombre*i))
```

5.1.3
```python
for i in range(1,11):
  table_multiplication(i)
```

5.1.4
```python
mot_de_passe=input("Mot de passe?")
while not mot_de_passe=="123soleil":
        print("Accès non autorisé")
        mot_de_passe=input("Mot de passe?")
for i in range(1,11):
  table_multiplication(i)
```

5.2
```python
def isprime(nombre):
  for i in range(nombre-1,1,-1):
    if nombre%i==0:
      return False
  return True
```

5.3
```python
def afficher_allumettes(nombre_allumettes):
    for i in range(nombre_allumettes):
        print("|",end='')
    print("")

def choisir_nombre():
    correct=False
    while not correct:
        choix=int(input("Combien d'allumettes prend-tu?"))
        if choix in [1,2,3]:
            correct=True
    return choix

def jeu(allumettes):
    joueur=1
    while allumettes > 1:
        afficher_allumettes(allumettes)
        print("Joueur {}:".format(joueur))
        choix=choisir_nombre()
        while allumettes-choix <= 0:
            print("Choose again")
            print("Joueur {}:".format(joueur))
            choix=choisir_nombre()
        if allumettes-choix== 1:
            print("Joueur {} gagne".format(joueur))
            allumettes-=choix
            afficher_allumettes(allumettes)
        else:
            allumettes-=choix
        joueur=3-joueur

def jeu_avec_ia(allumettes):
    joueur=1
    while allumettes > 1:
        afficher_allumettes(allumettes)
        if joueur==1:
            print("Joueur {}:".format(joueur))
            choix=choisir_nombre()
            while allumettes-choix <= 0:
                print("Choose again")
                print("Joueur {}:".format(joueur))
                choix=choisir_nombre()
            if allumettes-choix== 1:
                print("Joueur {} gagne".format(joueur))
                allumettes-=choix
                afficher_allumettes(allumettes)
            else:
                allumettes-=choix
        if joueur==2:
            if allumettes== 2:
                print("IA gagne")
                afficher_allumettes(allumettes-1)
                return
            else:
                print("IA prend une allumette")
                allumettes-=1
        joueur=3-joueur
jeu(10)
jeu_avec_ia(10)
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
