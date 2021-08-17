---
title: Elastic stack et le logging
theme: moon
separator: "\n>>>\n"
verticalSeparator: "\n---\n"
revealOptions:
    transition: 'none'
---

# Plan de la formation

- *Intro)*
- *I)* **Les évènement d'un système et le logging**
- *II)* **Elasticsearch, stocker une masse de données texte**
- *III)* **Installer un cluster virtuel Elasticsearch et Kibana**
- *IV)* **Elasticsearch et Kibana pour la recherche et l'analyse**

>>>

# III) Installer un cluster elasticsearch

voir feuille d'instructions

>>>

## IV.1) Rappels recherche


- Recherche fulltext

- Exemple: 
    Destfull:"New" -> champ text -> OK
        New chitose airport
    Dest:"New" -> champ keyword -> 0   hits
- Recherche exacte:
    DestCityName: "New York" -> keyword pour la ville
    Dest:" John F Kennedy International Airport" -> keyword pour l'aéroport de destination


>>>


## IV.2) Recherche Complexe avec filtre et aggregations

---

## Des requêtes complexes pour l'analyse

Elasticsearch est puissant pour l'analyse car il permet.
    - de combiner un grande quantité de critères de recherche différent en même temps
    - de transformer et afficher les données récupérées pour les rendre significatives

---

## Exemple
outer un filtre avec le bouton "+ Add a Filter"

Imaginons qu'on veuille chercher tous les avions qui ont décollé de New York sous la pluie depuis un mois et qui ont un prix moyen supérieur à 800$. Par exemple pour créer une mesure du risque économique que le dérèglement climatique fait peser sur une companie ?

On va devoir écrire une requête complexe.

---

# requêtes composées

- Des requête avec des ET des OU et des NON :

- Tous les vols qui concernent tel aéroport **et** qui contiennent le nom airways.

- Exemple: 

---

# Filtres de requêtes

- En partant des résultats d'une recherche **fulltext** : 

- On récupère les documents renvoyés par une requête (ce qu'elastic appel des **hits**) et on ne va en garder qu'une partie*.

- Garder que les vols dont le prix est entre 300 et 1000 €:
   - FlightDelayMin:[30 TO 50]
   - rajouter un filtre avec le bouton "+ Add a Filter"

- La période de temps en haut à droite de kibana est aussi un filtre

---


# Aggrégations des résultats de requêtes

- Très proche d'un **group by** en SQL.

- Grouper les documents/évènements par thème et faire des calculs transformations sur ces groupes.

- Pour calculer le prix moyen d'un ticket par compagnie par exemple :
    On va aggréger les vols de chaque compagnie et calculer la moyenne des prix des billets.

---

# 3 type d'aggrégations:

- **Bucket** (faire des groupes)
    - Grouper les vols par prix.

- **Metric** (travailler sur une dimension des données)
    - calculer la moyenne des prix, ou du retard des vols

- **Geographique** (grouper par zone géographique)
    - On peut **combiner** les  aggrégations

---

# Une métrique des données

- **Métrique** = caractéristique **chiffrée** des données.
   - *#* dans liste des propriétés dans Kibana.
   (pour faire une moyenne il faut une quantité)

---

# Créer des visualisations

- Une fois qu'on sait croiser des critères de recherche on peut créer des **visualisations**.

- Permet de voir une proportion ou un changement en un coup d'oeil (quand on sait de quoi ça parle).

---

# Intérêts de la Dashboard

- Vue globale pour comprendre rapidement les données

- Tout est dynamique: vous pouvez ajouter un filtre et les informations se mettent à jour.


