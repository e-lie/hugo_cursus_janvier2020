---
title: 15. Encapsulation et attributs statiques
draft: false
weight: 20
---


## Attributs 'statiques' (partagés par tous les objets d'une classe)

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


## Quelques astuces

- `dir(un_objet)` : listes tous les attributs / methodes d'un objet (ou module)
- Il existe aussi `un_objet.__dict__` 
- `MaClasse.__subclasses__()` : lister toutes les classes filles d'une classe



##  Des attributs "dynamiques" avec `@property`

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


## Attributs et méthodes privées

- Il est possible de définir des attributs et méthode privées si elles commencent par `__`
   - par exemple: `self.__toto`
- Un attribut / méthode privée de peut être appelé que depuis "l'intérieur de la classe"
   - attention : il ne s'agit pas de vraie "privacy" mais plutot de disuasion...

- On peut étre tenté de définir des getters et setters `get_toto()`, `set_toto()` pour interagir avec les attributs privés ... mais la façon pythonique est:

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

print(monobjet.toto)

monobjet.toto = 3
```