---
title: 15. Encapsulation et attributs statiques
draft: false
weight: 20
---


## D'abord quelques astuces

- `dir(un_objet)` : listes tous les attributs / methodes d'un objet (ou module)
- Il existe aussi `un_objet.__dict__` 
- `MaClasse.__subclasses__()` : lister toutes les classes filles d'une classe


## Attributs 'statiques' (partagés par tous les objets d'une classe)

Les attributs qui sont définits dans le corps de la classe et non dans le constructeurs sont statiques en python. C'est à dire que leur valeur est commune à toutes les instances de la classe en cours d'utilisation dans le programme. Cela peut être très pratique pour maintenir une vision globale de l'état du programme de façon sécurisée.

```python
class FormeGeometrique():

    nb_instances = 0

    def __init__(self):
        FormeGeometrique.nb_instances += 1

forme1 = FormeGeometrique()
forme2 = FormeGeometrique()
forme3 = FormeGeometrique()

print(FormeGeometrique.nb_instances)
# -> affiche 3
```


## Méthodes statiques et méthodes de classe

... Sont deux types de méthodes rattachées à une classe mais non à une instance de la classe (un objet). On les fabrique en ajoutant les décorateurs `@staticmethod` ou `@classmethod` sur une méthode de la classe.

La méthode statique est complètement indépendante de la classe même si rangée à l'intérieur alors que la méthode de classe récupère implicitement sa classe comme premier argument ce qui permet de construire des objet de la classe dans le corps de la méthode

### Exemple d'utilisation d'un méthode de classe

```python
class MaCollectionDeLettre: # Réimplétation de String

    def __init__(self, astring): # Build an object from a string
      self._string = astring

    @classmethod
    def build_from_list(cls, alist): # Alternative constructor to build from a list of lettres
      x = cls('') # L'argument implicite cls permet de construire un objet de la classe
      x._string = ','.join(str(s) for s in alist)
      return x
```


## L'encapsulation

Nous avons évoqué dans le cours 13 qu'un des intérêts de la POO est de sécuriser les variables dans un contexte isolé pour éviter qu'elles soient accédées à tort et à travers par différents programmeurs ce qui a tendance à créer des bugs mythiques.

Pour éviter cela on essaye au maximum d'encapsuler les attributs et les méthodes internes qui servent à faire fonctionner une classe pour éviter que les utilisateurs de la classe (ignorants son fonctionnement) puissent pas les appeler directement et "casser" le fonctionnement de la classe.

On parle d'attributs et méthodes `privés` quand ils sont internes et inaccessibles.

En Python les attributs et méthodes d'un objet sont "publiques" par défaut : on peut y accéder quand on veut et donc il faut donc une façon de pouvoir interdire leur usage:

- On utilise un underscore `_` devant le nom de l'attribut ou méthode pout indiquer qu'il est privé et ne doit pas être utilisé.

Exemple: `self._valeurinterne = 50` ou `def _mamethodeprivee(self, arg): ...`

En réalité l'attribut/méthode est toujours accessible, il s'agit d'une convention mais il faut la respecter !! Par défaut les editeurs de code vous masqueront les elements privés lors de l'autocomplétion par exemple.

## Accesseurs (getters) et mutateurs (setters)

Même lorsque qu'un attribut d'objet devrait être accessible à l'utilisateur (par exemple le rayon d'un cercle), on voudrait pouvoir contrôler l'accès à cet attribut pour que tout ce passe bien. 

Par exemple éviter que l'utilisateur puisse définir un rayon négatif !!

Pour cela on créé des attributs privés et on définit des méthodes "publique"

On veut donc généralement pouvoir y donner accès à l'utilisateur de la classe **selon certaines conditions**.

Pour cela un définit une méthode d'accès (getter/accesseur) qui décrit comment récupérer la valeur ou une méthode de modification (setter/mutateur) qui contrôle comment on peut modifier la valeur (et qui vous envoie balader si vous définissez un rayon négatif).

## Exemple (non pythonique !)

```python
class Cercle:

   def __init__(self, centre, rayon, couleur="black", epaisseur=0.1):
       self.centre = centre
       self._rayon = rayon
       self._couleur = couleur

    def get_couleur(self):
        print("on accède à la couleur")
        return self._couleur

    def set_rayon(self, rayon)
        assert rayon > 0, "Le rayon doit être supérieur à 0 !"
        self._rayon = rayon



cercle1 = Cercle((3, 5), 2, "red")
cercle1.get_couleur()
cercle1.set_rayon(1)
cercle1.set_rayon(-1) # Erreur
```

Cependant en Python on ne fait généralement pas directement comme dans cet exemple !

##  Des attributs "dynamiques" avec `@property`

Le décorateur @property ajouté à une méthode permet de l'appeler comme un attribut (sans parenthèses)

```python
class Carre(FigureGeometrique):

    # [ ... ]

    @property
    def aire(self):
        return self.cote * self.cote


carre_vert  = Carre((5, -1), 3, "green", epaisseur=0.2)
print(carre_vert.aire) # N.B. : plus besoin de mettre de parenthèse ! Se comporte comme un attribut
```

### Autre exemple avec `@property`

```python
class Facture():

    def __init__(self, total):
        self.montant_total = total
        self.montant_deja_paye = 0

    @property
    def montant_restant_a_payer(self):
        return montant_total - montant_deja_paye


ma_facture = Facture(45)
ma_facture.montant_deja_paye += 7

print("Il reste %s à payer" % ma_facture.montant_restant_a_payer)
# -> Il reste 38 à payer
```

La façon pythonique de faire des getters et setters en python est donc la suivante:

```python
        @property
        def toto(self):
            return self.__toto

        @toto.setter
        def toto(self, value):
            self.__toto = value   # ... ou tout autre traitement
```

On peut ensuite accéder et modifier l'attribut `toto` de manière transparente : 

```python
monobjet = Objet()

monobjet.toto = "nouvelle_valeur"
print(monobjet.toto)
