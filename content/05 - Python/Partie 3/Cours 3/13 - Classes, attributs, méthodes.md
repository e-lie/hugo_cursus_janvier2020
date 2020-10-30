---
title: Cours 3 - Classes, attributs et méthodes
draft: false
weight: 20
---


- L'orienté objet est un paradigme de programmation
- Regrouper les variables et fonctions en entités ("objets") cohérentes qui appartiennent à des classes
   - **attributs** (les variables décrivant l'état de l'objet)
   - **méthodes** (les fonctions appliqubles à l'objet)
- Critique pour garder un code structuré et compréhensible quand la complexité d'un projet augmente
- Possibilité d'interfaçage avec les bases de données (c.f. ORM)

## Exemple

### **Les voitures** (classe)

ont une couleur, une marque, un nombre de place et un kilométrage : ce sont des attributs.

Elles peuvent embarquer des passager, rouler, activer un clignotant : ce sont des méthodes.

### **Ma voiture** (objet, ou instance)

est une citroên rouge, 3 places et a parcouru 15672 km.

### **Celle de mon voisin** (autre objet, instance)

est une peugeot bleue, 5 places et a parcouru 3450 km.


## Autre exemple

### **Les cercles** (classe)

ont un centre, un rayon, une couleur, une épaisseur de trait : ce sont des attributs.

On peut : déplacer le cercle, l'agrandir, calculer son aire, le dessiner sur l'écran  : ce sont des méthodes.

### **Un petit cercle rouge** (objet, ou instance)

centre = (3, 5), rayon = 2, couleur = "red", épaisseur = 0.1

### **Un grand cercle bleu** (autre objet, instance)

centre = (-4, 2), rayon = 6, couleur = "blue", épaisseur = 1


## Exemple en Python

```python
class Cercle:

   def __init__(self, centre, rayon, couleur="black", epaisseur=0.1):
       self.centre = centre
       self.rayon = rayon
       self.couleur = couleur
       self.epaisseur = epaisseur

   def deplacer(self, dx=0, dy=0):
       self.centre = (self.centre[0]+dx, self.centre[1]+dy)

########################################################

cercle1 = Cercle((3, 5), 2, "red")
cercle2 = Cercle((-4, 2), 6, "blue", epaisseur=1)

cercle1.deplacer(dy=2)
print(cercle1.centre)
```

- `__init__` est le constructeur
- `__init__` et `deplacer` sont des méthodes
- `self` correspond à l'objet en train d'être manipulé
- Toutes les méthodes ont au moins `self` comme premier argument
- On utilise les methodes en faisant `un_objet.la_methode(...)`
- `self.centre`, `self.rayon`, `self.couleur`, `self.epaisseur` sont des attributs
- On instancie un objet en faissant `mon_objet = Classe(...)`


