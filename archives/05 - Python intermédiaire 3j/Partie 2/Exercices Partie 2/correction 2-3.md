---
title: Correction exercice 2.3 - Lecture itérative avec la library externe lxml
draft: false
weight: 20
---

- Installez `lxml` grâce à `pip3`, et récupérez le "gros" fichier XML, `copyright.xml` à l'adresse `https://dl.google.com/rights/books/renewals/google-renewals-20080516.zip`. Attention à ne pas tenter d'ouvrir "brutalement" ce fichier avec un éditeur ou avec la méthode utilisée en 1 : cela consommera beaucoup trop de RAM !

- En utilisant des commandes comme `head -n 50 copyright.xml`, analyser visuellement la structure du fichier d'après ses premières lignes.

- Initialiser un itérateur destiné à itérer sur ce fichier, et en particulier sur les tags `Title`. Créer une boucle à partir de cet itérateur et afficher tous les titres qui contiennent la chaîne `"Pyth"`. **On prendra soin de nettoyer les éléments trouvés avant de passer à chaque nouvelle itération sous peine de remplir la RAM très vite !**

- Pour chaque titre trouvé, remonter au parent 'Record' pour trouver le 'Holder Name' correspondant à ce titre. S'aider du debug VSCode, `ipython` et/ou `ipdb` pour tester et expérimenter en interactif.


{{% expand "correction" %}}
```python
from lxml import etree

def clear_elem_and_ancestors(elem):
    elem.clear()
    for ancestor in elem.xpath('ancestor-or-self::*'):
        while ancestor.getprevious() is not None:
            del ancestor.getparent()[0]

iterator = etree.iterparse("google-renewals-all-20080624.xml", tag="Record")

for event, element in iterator:
    title = element.find('./Title').text

    holder_name_element = element.find('./Holder/Name')
    if holder_name_element is not None:
        holder_name = holder_name_element.text
    else:
        holder_name = "No Holder"

    if "Pyth" in title:
        print("Title: {}\nHolder: {}\n".format(title, holder_name))
    clear_elem_and_ancestors(element)
```
{{% /expand %}}