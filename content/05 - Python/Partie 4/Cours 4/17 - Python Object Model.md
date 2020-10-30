


# Python Object Model 

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

