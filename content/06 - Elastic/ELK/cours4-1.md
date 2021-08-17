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

---

## III) Rechercher et analyzer dans Elasticsearch

### III.1) Index et recherche de texte

---

## Comme dans une bibliothèque

**Indexer** des documents c'est comme les **ranger dans une bibliothèque**.
Si on range c'est pour retrouver. Mais on veut vouloir trouver de deux types de façon.
 - **Recherche exacte**: On veut pouvoir trouver les documents rangés dans la catégorie *litterature anglaise* ou *bandes déssinées SF*.
 - **Recherche en texte intégral** ou **fulltext** : On veut pouvoir trouver les documents qui ont *Lanfeust* ou *éthique* dans leur titre.

---

## Recherche exacte

- Quand je cherche *littérature anglaise* je ne veux pas trouver les documents de *littérature espagnole* bien qu'il y ai le mot "littérature" en commun.
- Je veux que les termes correspondent précisément ou dit autrement je veux que *littérature anglaise* soit comme une seule étiquette, pas un texte.
- C'est le fonctionnement d'une recherche classique dans une base de données SQL:
```SQL
SELECT * FROM bibliothèque WHERE genre = "littérature anglaise";
```

---

## Recherche exacte 2

On utilise **_search**, **query** et **term**.

```json
GET /<index>/<type>/_search
{
    "query": {
        "term": {
            "<field>": "<value>"
        }
    }
}
```

---

## Recherche fulltext

- Retrouver non pas l'ensemble des livres d'un genre mais un livre *à partir d'une citation*.
- Pour cela on fait un **index inversé** qui permet  une **recherche fulltext**.
- Elasticsearch est spécialement fait pour ce type de recherche. Il le fait très efficacement et sur des milliards de lignes de texte.
  - exemple: github utilise elasticsearch pour indexer des milliers de dépôts de code. 

---

## Recherche fulltext 2

On utilise **_search**, **query** et **match**.

```json
GET /<index>/<type>/_search
{
    "query": {
        "match": {
            "<field>": "<value>"
        }
    }
}
```

---

## Différence entre les champs *keyword* et *text*

- Un champ **keyword** n'est pas indexé en mode fulltext : la méthode **match** ne fonctionne pas en mode partiel
- Un champ **text** est automatiquement indexé en mode fulltext: la méthode **match** fonctionne

(- un champ textuel créé implicitement est double le champ principal en **text** plus un sous champ **keyword**:
    - exemple: *title* est un champ **text**,  *title.keyword* est un champ **keyword**)

>>>

## Exercice III.1)

Avec la vue Devtools:
1. chercher le nombre d'avion *ES-Air* (champ *Carrier*) en tout
- chercher le nombre d'avion ou New apparaît dans l'aéroport de destination (champ Destfull)
- faire une recherche des avions où New apparaît dans le champ Dest. Que remarquez vous ?



