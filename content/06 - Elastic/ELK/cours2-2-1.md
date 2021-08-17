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

## II.2.1) Gérer les documents dans Elasticsearch.

---

## L'organisation basique de Elasticsearch

![](img/index.jpg)

---

## L'architecture basique de Elasticsearch

- **Index** :
    - comme une bibliothèque de documents
    - comme une *base de données* en SQL
    - on peut en créer plusieurs (bien sur)

---

## L'architecture basique de Elasticsearch

- **Type** avec son **Mapping**
    - le type des données stockées : livre
    - un peu comme une *table* en SQL
    - **mapping** = **format** c'est title+author+price+description

*mapping* signifie représenter/modéliser en anglais.

---

## L'architecture basique de Elasticsearch

- **Documents**
    - chaque entrée dans un index avec son *_id*
    - ici un *livre* ou un *vol*
    - un peu comme une *ligne* dans une table en sql

---

## Les opérations de base de l'API = CRUD

- "ajouter un index/mapping/document" (**C**reate)
- "récupérer/lire un index/mapping/document" (**R**ead)
- "mettre à jour un index/mapping/document" (**U**pdate)
- "supprimer un index/mapping/document" (**D**elete)

---

## CRUD et méthode HTTP

```
<METHODE> <URI>
<DATA>
```
METHOD en gros (il y a des exception sinon c'est pas drôle)
- GET = Read / récupérer
- POST = Créer
- PUT = Mettre à jour / Update
- DELETE = Supprimer

>>>

## Exercice II.2.1) Gérer les documents dans Elasticsearch.

Dans la vue *Devtools* et à l'aide de votre feuille de mémo de l'API : TODO bien ajouter toutes les fonctions requises dans le mémo

1. mettre à jour le livre que vous avez ajouté en changeant le prix
- ajouter deux nouveaux livre avec la méthode POST
- lister tous les livres de l'index
- lister les index présents sur le cluster
- supprimer le livre numéro 2 (avec son _id)


