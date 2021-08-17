---
title: Elastic stack et le logging
theme: moon
separator: "\n>>>\n"
verticalSeparator: "\n---\n"
revealOptions:
    transition: 'none'
---

# Elastic stack et le logging
*Apprendre à nager dans un océan d'évènements et de texte!*

---

# Plan de la formation

- *Intro)*
- *I)* **Les évènement d'un système et le logging**
- *II)* **Elasticsearch, stocker une masse de données texte**
- *III)* **Installer un cluster virtuel Elasticsearch et Kibana**
- *IV)* **Elasticsearch et Kibana pour la recherche et l'analyse**

---

## II) Elasticsearch, stocker une masse de données texte

---

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

- *DestCityName* et *OriginCityName* - Ville de départ et d'arrivé
- *timestamp* - Heure de départ
- *AvgTicketPrice* - le prix moyen des places pour le vol
- *FlightTimeHour* - Durée en Heure
- *DestWeather*, *OriginWeather* - La météo  au départ et à l'arrivée.

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

- TODO *image d'effarement*
- C'est joli mais un peu complexe/flippant, n'est-ce pas ?

---

## Dev tools

- Pour exécuter directement des requêtes *REST* (on y reviendra)
- taper: TODO même requête que la recherche au dessus
- Ctrl+Entrée ou "Play" pour lancer la commande selectionnée.

---

## Dev Tools 2

- C'est cette vue qu'on va principalement utiliser dans la partie II et aussi III :
- Elle montre mieux les dessous de Elasticsearch et
- Il faut que vous compreniez bien le principe d'une API REST JSON parce que c'est très répendu.

>>>






## II.1) Syntaxe de l'API et de JSON

---

## API : parler avec Elasticsearch

- API = *Application Programming Interface* :
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
- La référence c'est la documentation:  https://www.elastic.co/guide/en/elasticsearch/reference/6.4/index.html
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

- est *facultatif*
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




>>>

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

