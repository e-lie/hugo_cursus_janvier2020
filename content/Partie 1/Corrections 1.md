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
print("#"*len(mot)+2+"\n"+)
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
