---
title: Correction - Exercice 2
weight: 5
draft: false
---
7.1
```python
def retourner_plus_grand(liste):
    max=liste[0]
    for nombre in liste:
        if nombre>=a:
            max=nombre
    return max
```
7.2
```python
def plus_grand_mot(liste):
    plus_grand_mot=liste[0]
    for mot in liste:
        if len(mot)>=len(a):
            plus_grand_mot=mot
    return plus_grand_mot
```

7.3
```python
def somme(liste):
    total=0
    for total in liste:
        total+=nombre
    return total
```
 7.4
 ```python
 def extraire_nom_fichier(path):
     liste=path.split("/") #['usr,'bin,'toto.py']
     nom_du_fichier=list[-1].split(".") #['toto','py']
     return liste[0]

# En un seule ligne
def extraire_nom_fichier_une_ligne(path):
  return path.split("/")[-1].split(".")[0]
```
7.5.1
```python
example_dict=[{'name': 'Sebastian', 'email': 'Donec.felis.orci@consectetueripsumnunc.edu', 'country': '1979'}, {'name': 'Barclay', 'email': 'aliquet.metus.urna@neceleifend.co.uk', 'country': '2000'}, {'name': 'Vivien', 'email': 'pharetra@a.com', 'country': '1955'}, {'name': 'Britanney', 'email': 'eu.tellus.Phasellus@arcuvelquam.ca', 'country': '1961'}, {'name': 'Reese', 'email': 'tortor.dictum.eu@egestasSed.ca', 'country': '1951'}, {'name': 'Keegan', 'email': 'libero.nec@cursuset.co.uk', 'country': '1998'}, {'name': 'Ezekiel', 'email': 'tempus.mauris.erat@aclibero.org', 'country': '1951'}, {'name': 'Odessa', 'email': 'massa.Quisque.porttitor@felis.net', 'country': '1925'}, {'name': 'Elijah', 'email': 'luctus.vulputate.nisi@nunc.com', 'country': '1963'}, {'name': 'Hilel', 'email': 'lectus.pede.et@aliquetsem.ca', 'country': '1982'}, {'name': 'Callie', 'email': 'et.euismod.et@aliquetmagnaa.net', 'country': '1984'}, {'name': 'India', 'email': 'Duis.sit.amet@Phaselluslibero.com', 'country': '1938'}, {'name': 'Lane', 'email': 'amet@turpis.ca', 'country': '1922'}, {'name': 'Alexis', 'email': 'sagittis.placerat@nibhdolor.net', 'country': '1927'}, {'name': 'Micah', 'email': 'lorem.eget.mollis@SeddictumProin.com', 'country': '1914'}, {'name': 'Rigel', 'email': 'sollicitudin@eratinconsectetuer.org', 'country': '1941'}, {'name': 'Avram', 'email': 'tincidunt.vehicula@vulputate.org', 'country': '1919'}, {'name': 'Dieter', 'email': 'ornare.lectus.justo@Integeridmagna.org', 'country': '1937'}, {'name': 'Sarah', 'email': 'cubilia.Curae.Phasellus@non.net', 'country': '1946'}, {'name': 'Graham', 'email': 'elit.Curabitur.sed@maurisIntegersem.edu', 'country': '1931'}, {'name': 'Daquan', 'email': 'fermentum.convallis.ligula@porttitorinterdum.co.uk', 'country': '1934'}, {'name': 'Nell', 'email': 'purus@lectusconvallisest.org', 'country': '1997'}, {'name': 'Ocean', 'email': 'ut@Nuncquisarcu.net', 'country': '2006'}, {'name': 'Cruz', 'email': 'Aenean.euismod.mauris@idmollisnec.edu', 'country': '1950'}, {'name': 'Hyacinth', 'email': 'amet@Nunc.edu', 'country': '1929'}]

def lire_dict(dict):
    for element in dict:
        print("{} est né.e en {}".format(element["name"],element["country"]))

lire_dico(exemple_dict)
```

7.5.2
```python
def lire_dict_edu(dict):
    for element in dict:
        if element["email"].split(".")[-1] == 'edu':
            print("{} a pour email {}".format(element["name"],element["email"]))

read_dict_edu(example_dict)
```

7.6
```python
def compte_lettres(phrase):
    dict={}
    # Pour chaque lettre:
    for lettre in phrase:
    # Si la clef existe (dans ce cas la lettre a déjà été rencontrée) alors on incremente sa valeur de 1.
        if lettre in dict:
            dict[lettre]+=1
    # Si la clef n'existe pas, on la crée et on initialise sa valeur à 1
        else:
            dict[lettre]=1
    return dict

phrase="Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt "
print(compte_lettres(phrase))
```

7.7
```python
def retourne_pair(liste):
    nouvel_liste=[]
    for nombre in liste:
        if nombre%2==0:
            nouvel_liste.append(element)
    return nouvel_liste

liste_paire=range(11)
print(retourne_pair(liste_paire))
```
7.8
Cette algorithme de tri classique s'appelle le tri à bulle. Ce n'est pas le plus rapide, mais il est facilement compréhensible.
```python
def tri_a_bulles(tableau):
    for i in range(len(tableau),0,-1):
        for j in range(i-1):
            if tableau[j+1]<tableau[j]:
                tableau[j+1], tableau[j]=tableau[j],tableau[j+1]
    return tableau
```

7.9 On utlise ici une imbrication de compréhension de lire:
```python
matrice=[[i+j for i in range(5)] for j in range(4)]
```
7.10
```python
def somme_2(liste):
    if liste:
        #Litéralement ma somme vaut le dernier élément plus la somme de tous les autres éléments moins le dernier
        return liste[-1]+somme_2(liste[:-1])
    else:
        return 0
```

7.11
```python
def carre():
    i=1
    while True:
        i+=1
        yield i*i

for i in carre():
    if i>200
        break
    print (i)
```
