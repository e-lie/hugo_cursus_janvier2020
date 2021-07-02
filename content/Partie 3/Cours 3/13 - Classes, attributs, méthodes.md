---
title: 13. POO - Classes, attributs et méthodes
draft: false
weight: 20
---


L'orienté objet est un paradigme de programmation inventé dans les années 80 et popularisé dans les années 90. Aujourd'hui il est incontournable bien qu'il commence aussi à être critiqué.

Il permet d'organiser un programme de façon standard et ainsi d'éviter des erreurs d'architectures comme le `spaghetti code`

## Principe de base

Regrouper les variables et fonctions en entités ("objets") cohérentes qui appartiennent à des classes

   - **attributs** (les variables décrivant l'état de l'objet)
   - **méthodes** (les fonctions appliqubles à l'objet)

De cette façon on fabrique des sorte **types de données** spécifique à notre programme utilisables de façon pratique et consistante.

## Exemple

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


cercle1 = Cercle((3, 5), 2, "red")
cercle2 = Cercle((-4, 2), 6, "blue", epaisseur=1)

cercle1.deplacer(dy=2)
print(cercle1.centre)
```

- `__init__` est le constructeur C'est la fonction qui est appelée à la création de l'objet.

- On instancie un objet en faissant `mon_objet = Classe(...)` ce qui appelle `__init__`


- `self` correspond à l'objet en train d'être manipulé. Il soit être passé en paramètre de toutes les fonctions de la classe (les méthodes)

Les attributs sont les variables internes qui décrivent l'état et régisse le fonctionnement de l'objet.

- `self.centre`, `self.rayon`, `self.couleur`, `self.epaisseur` sont ici les attributs. Si on lit littéralement la syntaxe python on comprend `self.centre` comme "le centre de l'objet en cours(le cercle en cours)"

Toutes les fonctions incluses dans la classe sont appelées des méthodes.

- `__init__` et `deplacer` sont des méthodes. Elles agissent généralement sur les attributs de l'objet mais pas nécessairement.

Les attributs et méthodes de la classe sont **"dans"** chaque instance d'objet (ici chaque cercle). On dit que la classe est un namespace (ou espace de nom). Chaque variable `centre` est isolée dans son cercle et on peut donc réutiliser plusieurs fois le nom `centre` pour chaque cercle. Par contre pour y accéder on doit préciser le cercle concerné avec la syntaxe `cercle1.centre`.

- De même on utilise les methodes en faisant `un_objet.la_methode(...)`

#### Attention à l'indentation !!


## Spaghetti code, variables globales et refactoring

Lorsqu'on enchaine simplement des instructions sans trop de structure dans un programme on arrive vite à quelque chose d'assez imprévisible et ingérable.

On commence généralement à définir des variables globales accessibles partout pour maintenir l'état de notre programme. Plusieurs fonctions viennent modifier de façon concurrente ces variables globales (pensez au score dans un jeu par exemple) pouvant mener à des bugs complexes.

On arrive aussi à beaucoup de code dupliqué et il devient très difficile dans ce contexte de refactorer un programme:

- dès qu'on tire un spaghetti tout casse
- dès qu'on veut changer un endroit il faut modifier beaucoup de choses
- la compréhension du programme devient difficile pour le développeur initial et encore plus pour ses collègues.

On peut voir la programmation orientée objet comme une façon d'éviter le code spaghetti.

## Intérets de la POO

La POO est critique pour garder un code structuré et compréhensible quand la complexité d'un projet augmente.

- Rassembler ce qui va ensemble pour s'y retrouver
- Maintenir les variables isolées à l'intérieur d'un "scope" pour évitées qu'elles ne soient modifiée n'importe quand et n'importe comment et qu'il y ai des conflits de nom.
- Fournir une façon d'architecturer un programme que tout le monde connait à peu près
- Fournir un moyen efficace de programmer en évitant la répétition et favorisant la réutilisation
- Créer des "boîtes noires" utilisables sans connaître leur fonctionnement interne (bien et pas bien à la fois). C'est à dire une façon de se répartir le travail entre développeurs (chacun sa boîte qu'on maîtrise).


## DRY don't repeat yourself et couplage

La POO permet d'appliquer le principe DRY -> identifier ce qui se ressemble et le rassembler dans une méthode ou une classe.

Cela permet ensuite de modifier le code à un seul endroit pour tout changer -> puissant.

Il s'agit plus d'un ideal que d'un principe. Il ne faut pas l'appliquer à outrance parfois un peu de répétition est mieux car plus simple.

Si on factorise tout en POO on arrive souvent à un code fortement coupler qui empêche le refactoring et le programme finit par devenir fragile.

# À retenir

- `__init__` est le constructeur
- `__init__` et `deplacer` sont des méthodes
- `self` correspond à l'objet en train d'être manipulé
- Toutes les méthodes ont au moins `self` comme premier argument
- On utilise les methodes en faisant `un_objet.la_methode(...)`
- `self.centre`, `self.rayon`, `self.couleur`, `self.epaisseur` sont des attributs
- On instancie un objet en faissant `mon_objet = Classe(...)`
