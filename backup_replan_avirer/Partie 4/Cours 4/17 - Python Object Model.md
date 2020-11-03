---
title: 17. Python Object Model
draft: false
weight: 20
---


# Python Object Model 

Si on regarde un autre langage orienté objet avant Python il paraît étrange de mettre `len(collection)` au lieu de `collection.len()` (faire comme s'il s'agissait d'un fonction plutôt que d'une méthode). Cette apparente bizarrerie est la partie émergée d'un iceberg qui, lorsqu'il est bien compris, est la clé de ce qui est pythonique. L'iceberg est appelé le Python Object (/Data) Model, et il décrit l'API que vous pouvez utiliser pour faire jouer vos propres objets avec le langage le plus idiomatique
des fonctionnalités. (traduction d'nu paragraphe du livre Fluent Python)

En gros plutôt que d'utiliser 


## Méthodes spéciales / "magiques"

- `__repr__` et `__str__` : génère une représentation de l'objet sous forme de chaîne de caractère

```python
   def __repr__(self):
      return "Cercle de couleur " + self.color + " et de rayon " + self.rayon
```

- `__add__` : définir l'addition de deux objets

- `__eq__` : définir l'égalité entre deux objets

- `__iter__` : définir comment itérer sur un objet

- ... [et plein d'autres](https://docs.python.org/3/reference/datamodel.html) ...

