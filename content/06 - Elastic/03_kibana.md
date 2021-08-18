---
title: "3 - Kibana"
draft: false
weight: 3030
---

## La recherche, le KQL

## Les dashboards

## Autres fonctionnalités

geoip

YT KIBANA :
https://www.youtube.com/watch?v=6bM5SPVIuDs

Lancer un scanner web pour faire clignoter le dashboard ? Gerne nikto

## II.0) La tête la première dans Kibana

- Accédez à http://81.65.166.101:15601/

---

## Vue Discover

Un exemple de données : des vols d'avions

- Des **évènements** très similaires à des **logs** mais plus facile à imaginer;
  - une date
  - un lieu
  - des informations spécifiques
- Avec des données de géolocalisation (ce qui est pas forcément le cas pour des logs)

---

## Les différents champs décrivant chaque vol

- _DestCityName_ et _OriginCityName_ - Ville de départ et d'arrivé
- _timestamp_ - Heure de départ
- _AvgTicketPrice_ - le prix moyen des places pour le vol
- _FlightTimeHour_ - Durée en Heure
- _DestWeather_, _OriginWeather_ - La météo au départ et à l'arrivée.

---

## Une première recherche

- Faites une petite recherche : "New York"

Résultat:

- Resultats exacts : New York
- Résultats partiels: New Chitose Airport, Louis Armstrong New Orleans International Airport

---

## Régler la Période

- Combien de vols ?
  - depuis 24h ?
  - depuis une semaine ?
  - depuis 30 jours ?

---

## Dashboard

- TODO _image d'effarement_
- C'est joli mais un peu complexe/flippant, n'est-ce pas ?

---

## Dev tools

- Pour exécuter directement des requêtes _REST_ (on y reviendra)
- taper: TODO même requête que la recherche au dessus
- Ctrl+Entrée ou "Play" pour lancer la commande selectionnée.

---

## Dev Tools 2

- C'est cette vue qu'on va principalement utiliser dans la partie II et aussi III :
- Elle montre mieux les dessous de Elasticsearch et
- Il faut que vous compreniez bien le principe d'une API REST JSON parce que c'est très répendu.

> > >

## II.1) Syntaxe de l'API et de JSON

---

## API : parler avec Elasticsearch

- API = _Application Programming Interface_ :
  "Une **liste de fonctions** qu'on peut appeler de l'**extérieur** d'un logiciel"
- Elasticsearch à une API REST JSON (on y reviendra plusieurs fois)

---

## Connaitre la version de Elasticsearch

Dans la vue dev tools tapez:

```json
GET /
```

---

## réponse:

```json
{
  "name": "ZEWiZLN",
  "cluster_name": "elk_formation",
  "cluster_uuid": "rGzTBgbXRyev62Ku4vTWFw",
  "version": {
    "number": "6.4.3",
    ...
  },
  "tagline": "You Know, for Search"
}
```

---

## Version de Elasticsearch

- Version 6.4. C'est important car entre chaque version majeure (3, 4, 5, 6) il y a des changements dans les fonctions (L'API)
- La référence c'est la documentation: https://www.elastic.co/guide/en/elasticsearch/reference/6.4/index.html
- Toutes les fonctions de elasticsearch y sont décrites et on peut choisir la version selon celle installée.

---

## Syntaxe d'un appel de fonction

```
<METHODE> <URI>
<DATA>
```

```json
PUT /bibliotheque/livre/1
{
    "title": "La Promesse de l'aube",
    "description": "[...] J'entendis une fois de plus la formule intolérable [...]",
    "author": "Romain Gary",
}
```

---

## Le BODY

- est _facultatif_
- est en JSON (JavaScript Objet Notation)
  - décrire des données complexes avec du texte
  - très répendu
  - pas trop dur à lire pour un humain

---

## Syntaxe du JSON

```json
{
    "champ1": "valeur1",
    "champ2_nombre": 3, // pas de guillemets
    "champ3_liste": [
        "item1",
        "item2",
        "item3",
    ],
    "champ4_objet": { // on ouvre un "nouveau json" imbriqué
        "souschamp1": "valeur1.1";
        ...
    },
    "champ5": "Pour échapper des \"guillemets\" et des \\n" // échappement pour " et \
}
```

> > >

## Exercice II.1) syntaxe API et JSON

1. Chercher un livre sur http://lalibrairie.com

- écrire un fichier JSON pour décrire le livre avec:

  - le titre (title)
  - l'auteur (author)
  - le prix (price)
  - la première phrase de la description à mettre entre guillemets (description)
  - d'autres infos si vous voulez

- Choisissez un nom simple pour votre bibliothèque.
- Ajoutez ce livre à votre bibliothèque dans Kibana :

```json
PUT /<votre_bibli>/livre/1
<DATA>
```

---

## IV.1) Rappels recherche

- Recherche fulltext

- Exemple:
  Destfull:"New" -> champ text -> OK
  New chitose airport
  Dest:"New" -> champ keyword -> 0 hits
- Recherche exacte:
  DestCityName: "New York" -> keyword pour la ville
  Dest:" John F Kennedy International Airport" -> keyword pour l'aéroport de destination

> > >

## IV.2) Recherche Complexe avec filtre et aggregations

---

## Des requêtes complexes pour l'analyse

Elasticsearch est puissant pour l'analyse car il permet. - de combiner un grande quantité de critères de recherche différent en même temps - de transformer et afficher les données récupérées pour les rendre significatives

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

- On récupère les documents renvoyés par une requête (ce qu'elastic appel des **hits**) et on ne va en garder qu'une partie\*.

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
  - On peut **combiner** les aggrégations

---

# Une métrique des données

- **Métrique** = caractéristique **chiffrée** des données.
  - _#_ dans liste des propriétés dans Kibana.
    (pour faire une moyenne il faut une quantité)

---

# Créer des visualisations

- Une fois qu'on sait croiser des critères de recherche on peut créer des **visualisations**.

- Permet de voir une proportion ou un changement en un coup d'oeil (quand on sait de quoi ça parle).

---

# Intérêts de la Dashboard

- Vue globale pour comprendre rapidement les données

- Tout est dynamique: vous pouvez ajouter un filtre et les informations se mettent à jour.
