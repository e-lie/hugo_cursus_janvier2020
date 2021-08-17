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

## II.2.2) Gérer les mappings et les index

---

## Mapping implicite et Mapping explicite

Lorsque vous ajoutez un document sans avoir créé de Type de document et de mapping (=format) pour ce type, elasticsearch
créé *automatiquement* un format en devinant le type de chaque champ:
 - pour les champs texte il prend le type **text** + **keyword** (on verra pourquoi après)
 - pour champs numériques il prend le type **integer** ou **float**

 C'est le **mapping implicite**
Mais si on veux des champs spéciaux ou optimisés il faut créer le mapping soit même explicitement.

---

## Mapping explicite

Pour avoir plus de contrôle sur les types de champs il vaut mieux décrire manuellement le schéma de données.
Au moins les premiers champs qu'on connaît déjà.

(Il peut arriver qu'on ait pas au début d'un projet une idée de toutes les parties importantes. On peut raffiner le mapping au fur et à mesure)

---

## Afficher le(s) mapping(s)

```json
GET /<index>/_mapping
```
                                  
Exemple:
```json
GET /kibana_sample_data_flights/_mapping
```

---

## Les types de données (datatypes)

Un documents dans elasticsearch est une données complexe qui peut être composé de nombreux éléments hétérogènes

Les types les plus importants:
**text**, **keyword**, **integer**, **float**, **date**, **geo_point**

---

## Types texte

-  **text** : Pour stocker du texte de longueur arbitraire. Indexé en recherche **fulltext**. On y reviendra: ça veut dire que tous les mots du texte sont recherchables.
- **keyword** : Du texte généralement court pour décrire une caractéristique du document
  - Exemple: *OriginWeather* décrit la météo *cloudy*

---

## Types nombre

- **integer** : un nombre entier
- **float** : nombre à virgule

Il existe d'autres types de nombres plus courts ou plus longs (donc plus gourmand en espace).

---

## Type **date**

Comme on stocke souvent des évènements dans elasticsearch il y a presque toujours une ou plusieurs date dans un document.
En fait ce n'est pas vraiment une date mais ce qu'on appelle un *timestamp* qui va jusqu'à la milliseconde.

---

## Type **geo_point**

Pour stocker un point géographique. C'est une paire de nombres : *latitude* et *longitude*.
On verra ça un peu dans kibana plus tard. 
La stack Elk fournit plein d'outil pour stocker des données géolocalisés et les visualiser :
C'est un besoin courant. exemple: savoir d'où viennent les requêtes sur votre application pour connaître vos usagers.

---


## Exercice II.2.2)

1. supprimer votre index
- Cherchez dans la documentation comment ajouter un mapping
- Décrivez en JSON les propriétés suivantes pour ce mapping en choisissant les types
    title, description, author, price, ISBN/EAN, weight

- Ajouter le mapping. Indication : il faut un nouvel index d'abord (mettez 1 shard et 0 replicas)

- Recréez vos deux livres avec POST sans renseigner l'ISBN
- ajouter l'ISBN avec PUT problème
- ajouter un champ de type *long* pour régler le problème


