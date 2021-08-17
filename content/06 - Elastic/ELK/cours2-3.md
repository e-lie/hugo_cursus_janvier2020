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

## Plan 

# Plan de la formation

- *Intro)*
- *I)* **Les évènement d'un système et le logging**
- *II)* **Elasticsearch, stocker une masse de données texte**
- *III)* **Installer un cluster virtuel Elasticsearch et Kibana**
- *IV)* **Elasticsearch et Kibana pour la recherche et l'analyse**

---

## II.3) API REST et JSON ?

---

## Revenons sur le format de l'API

C'est quoi ce format d'appel de fonction:
METHOD URI DATA ?

---

## HTTP - 1

- Le protocole le plus connu pour la communication d'applications
- protocole = requêtes et réponses formalisées entre deux logiciels
- exemples:
    - navigateur <-> serveur apache
    - kibana <-> elasticsearch
    - application web <-> mongoDB

---

## HTTP - 2

#### En requête
- url: http://192.168.0.34:4561 ou http://monelastic.net/catalog/product/3/_update
- method: GET, POST, PUT, DELETE, HEAD, ... autres moins connues
- data: données du message falcultatif

---

## HTTP - 3

#### En réponse
- un fichier avec
 - un en tête nommé *HEAD* qui gère décrit la réponse  avec des métadonnées
 - le *HEAD* contient notamment un **code de réponse** :
   - 200 = OK
   - 404 = non trouvé
 - un contenu nommé *BODY*

---

## API REST - 1

- API = *Application Programming Interface* :
"Une **liste de fonctions** qu'on peut appeler de l'**extérieur** d'un logiciel"

---

#### API REST - 2

- REST signifie *REpresentational State Transfer*.
- C'est un format standard (le plus répendu) pour une API.
- C'est-à-dire une façon de décrire la liste des fonctions et leurs paramètres.

---

## Curl, l'outil HTTP

- `GET / ` devient : `curl -XGET  http://localhost:9200/`

```json
PUT /catalog/product/1
{
    "sku": "SP000001",
    "title": "Elasticsearch for Hadoop",
    "description": "Elasticsearch for Hadoop",
    "author": "Vishal Shukla",
    "ISBN": "1785288997",
    "price": 26.99
}
```
Devient :
```bash
$ curl -XPUT http://localhost:9200/catalog/product/1 -d '{ "sku": "SP000001",
"title": "Elasticsearch for Hadoop", "description": "Elasticsearch for
Hadoop", "author": "Vishal Shukla", "ISBN": "1785288997", "price": 26.99}'
```

>>>



## Exercice II.3) Utiliser curl

1. connectez vous à l'infra en ssh:
```bash
ssh -p 12223 formation@ptych.net
```
l'adresse de elasticsearch est 0.0.0.0:9200

- taper `curl --help`, cherchez le nom de l'option longue correspondant à `-d` (un petit grep ?)
- ajouter une suite à l'un de vos livre avec curl.
- ajoutez une entrée *genre* de type keyword dans votre mapping et mettez à jours vos livre pour ajouter leur genre
- utilisez curl pour télécharger une page de la documentation dans votre dossier personnel.

>>>

