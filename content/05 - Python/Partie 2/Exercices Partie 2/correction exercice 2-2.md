---
title: Correction exercice 2.2 - Utilisation de la librairie XML intégrée `ElementTree`
weight: 5
draft: false
---

- En utilisant le module ElementTree de Python, charger le fichier `country.xml` fourni par le formateur. Boucler sur les différents éléments `country` et afficher pour chaque élément la valeur du `gdppc` et le nom des voisins.

- Ajouter un element `country` pour la France et l'Espagne en suivant la même structure.

- Sauvegarder la version modifiée en `country_extended.xml`

{{% expand "correction" %}}
```python
from xml.etree import ElementTree as ET

if __name__ == "__main__":
    root = ET.parse("countries.xml")

    data = root.find(".")

    for country in data.iter(tag="country"):
        print("\ncountry: " + country.attrib['name'])
        print("gdppc: " + country.find("gdppc").text)
        print("neighbors:")
        for neighbor in country.iter(tag="neighbor"):
            print("\t" + neighbor.attrib["name"])

    
    france = ET.SubElement(data, "country", attrib={"name": "France"})
    france_gdppc = ET.SubElement(france, "gdppc")
    france_gdppc.text = "42473"
    for name, direction in [
                ("Spain", "S"),
                ("Germany", "N-E"),
                ("Swiss", "E"),
                ("Belgium", "N"),
                ("Italy", "S-E")
            ]:
        ET.SubElement(france, "neighbor", attrib={'name': name, 'direction': direction})

    root.write("countries_extended.xml")
```
{{% /expand %}}