---
title: Bonus. ArgParse, utiliser des arguments en ligne de commande
draft: false
weight: 24
---

argparse est une librairie python qui va notre permettre de créer simplement des interfaces en ligne de commandes.

```python
import argparse

parser = argparse.ArgumentParser(description="This script does something.")
parser.add_argument("who", help="Who are you ?")
parser.add_argument("many", type=int)
args = parser.parse_args()
for i in range(args.many):
  print("Hello " + args.who)
```

On crée d'abord un parser avec:
```python
parser = argparse.ArgumentParser(description="This script does something.")
```

Puis on le remplit avec les informations sur les arguments avec `add_argument`.
On peut indiquer un argument positionnel, en le nommant juste, comme optionnel, avec - ou --
```python
parser.add_argument("who", help="Who are you ?")
parser.add_argument("many", type=int)
```
argparse traite les données en entrée comme des chaines de caractère si un type n'est pas précisé. On peut le préciser tout simplement
avec l'option `type=(nom_du_type)`
On prend ensuite les arguments en entrée et on les parses avec parse_args:
```python
args=parser.parse_args()
```

On a ainsi nos différents arguments. Ici, `args.many`, `args.who`

Reprendre l'exemple précédent et ajouter - devant le nom des argument. Que se passe t'il?
Reprendre l'exemple, sauf que cette fois si l'utilisateur ne rentre rien, le programme affiche 3 fois Hello john.

Notes: notre parser est en fait un objet, tout comme ici args. args.many et args.who sont ainsi les attributs de l'objet args.
Nous reviendrons sur la notion d'objet plus tard.
///// A supprimer ////
```python
import argparse
parser = argparse.ArgumentParser(description="This script does something.")
parser.add_argument("--who", help="Who are you ?")
parser.add_argument("--many", type=int)
args = parser.parse_args()
for i in range(args.many):
  print("Hello " + args.who)
```
