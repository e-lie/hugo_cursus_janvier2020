---
title: Corrections 2
draft: false
weight: 20
---


## 2.1 - Fichiers, JSON et dictionnaires

#### Exercice 2.1.1

```python

def ouvrir_fichier(chemin):
    with open(chemin, 'r') as fichier:
        toute_les_lignes = fichier.readlines()

    result = "".join(toute_les_lignes)

    return result


if __name__ == '__main__':
    print(ouvrir_fichier("libraries.py"))
```

#### Exercice 2.1.2

```python
def remplacer_dans_fichier(chemin, mot_a_remplacer, nouveau_mot):
    with open(chemin, 'r') as fichier:
        toute_les_lignes = fichier.readlines()

    lignes_remplacees = [ ligne.replace(mot_a_remplacer, nouveau_mot) for ligne in toute_les_lignes ]
    result = "".join(lignes_remplacees)

    with open(chemin, 'w') as fichier:
        fichier.write(result)

if __name__ == '__main__':
    remplacer_dans_fichier("monfichier.py", "mon_mot", "nouveau_mot")
```