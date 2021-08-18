---
title: "2 - Exercice"
draft: false
weight: 3021
---

## Mise en place d'un cluster multi-node

https://www.elastic.co/guide/en/elastic-stack-get-started/current/get-started-docker.html

# Use the Cluster Health API [http://localhost:9200/_cluster/health], the

curl -s localhost:9200/\_cluster/health | jq

<!-- # Node Info API [http://localhost:9200/_cluster/nodes] or GUI tools -->
<!-- curl -s localhost:9200/_nodes | jq -->
<!-- https://www.elastic.co/guide/en/elasticsearch/reference/current/cluster.html -->
<!-- https://www.elastic.co/guide/en/elasticsearch/reference/current/indices.html -->
<!-- https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-stats.html -->
<!-- https://www.elastic.co/guide/en/elasticsearch/reference/current/search.html -->

curl -s localhost:9200/\_cat/nodes

# such as <http://github.com/lukas-vlcek/bigdesk> and

# <http://mobz.github.com/elasticsearch-head> to inspect the cluster state.

## Recherche via l'API

## Pattern-matching

## Exploration du cluster (paramètres)

<!-- https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-xvi-full-text-search -->

---

---

title: Corrections des exercices
theme: moon
separator: "\n>>>\n"
verticalSeparator: "\n---\n"
revealOptions:
transition: 'none'

---

## I.1) Analyse de logs

Objectif: - analyser des logs pour retrouver une information - être attentif au format des logs

Sur le serveur ptych.net le titre d'une page web a été changé. On veut savoir qui des trois administrateurs Alice, Bob ou Jack a fait cette modification.

- connectez vous en ssh :
  `ssh -p 12222 enqueteur@ptych.net`
  passwd: `enqueteur`
- en utilisant cat et grep par exemple:
  - Pour savoir qui s'est connecté consultez le fichier /var/log/auth.log
  - Pour connaître le titre du site au fil du temps consultez le fichier /var/log/nginx/access.log
- utilisez `| grep MyWebSite` pour savoir quand le titre a été modifié
- utilisez `| grep` et l'heure pour savoir qui s'est connecté à cette heure ci
- bilan ?

## I.2) Calculer une quantité de logs

- entre 100 et 150 caractères, disons 120.
- un octet
- on vide le fichier on laisse tourner une semaine et on compte les lignes (`wc -l`)

- ( 12 _ (200 + 120) + 60 ) 120 _ 60 _ 24 _ 30 = 20217600000 = 20.2176 GB

C'est une application moyenne. Un infra pour 600.000 utilisateurs produit par exemple 10 TB en 3 mois.

## II.1) API JSON

Dans la vue devtools:

```json
PUT /mabibli/livre/1
{
    "title": "Awesome Thriller",
    "author": "Stephen King", // pas de guillemets
    "description": "\"Un roman haletant\"",
    "price": 9.80
}
```

# II.2.1

- Afficher votre livre pour se souvenir du prix

```json
GET /mabibli/livre/1/
```

- mettre à jour le prix

```json
POST /mabibli/livre/1/_update
{
    "doc": {
        "price": 19.80
    }
}
```

- ajouter 2 nouveau livres

```json
POST /mabibli/livre/
{
    "title": "Awesome Thriller 2",
    "author": "Stephen King", // pas de guillemets
    "description": "\"Un roman succulent\"",
    "price": 9.80
}
```

POST /mabibli/livre/
{
"title": "Awesome Thriller 3",
"author": "Stephen Boring", // pas de guillemets
"description": "\"Un roman ennuyant\"",
"price": 11.80
}

````
- lister tous les livres de l'index
```json
GET /mabibli/livre/_search
````

- lister les index

```json
GET /_cat/indices
```

- Supprimer livre 2 avec l'id récupéré précédemment dans la liste

```json
DELETE /mabibli/livre/Ekd8E2cBVH8Nz7YD6zUt
```

## Exercice II.2.2)

- `DELETE /mabibli`
- utilisez un moteur de recherche : https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-put-mapping.html

- description du mapping

```json
{
  "properties": {
    "title": {
      "type": "text"
    },
    "author": {
      "type": "text"
    },
   ...
}
```

- recréer l'index:

```json
PUT /mabibli
{
  "settings": {
    "index": {
      "number_of_shards": 1,
      "number_of_replicas": 0
    }
  }
}
```

- ajouter le mapping

```json
PUT /mabibli/_mapping/livre
{
  "properties": {
    "title": {
      "type": "text"
    },
    "author": {
      "type": "text"
    },
    ...
  }
}
```

- créer un livre

```json
POST /mabibli/livre/
{
    "title": "Awesome Thriller",
    "author": "Stephen King",
    "description": "\"Un roman alletant\"",
    ...
}
```

- ajouter l'ISBN

```json
POST /mabibli/livre/<id>
{
    "doc": {
        "ISBN": 9782369350804
    }
}
```

- problème: l'ISBN est trop grand pour rentrer dans un integer normal. Il faut un type long

- Ajoutons un nouveau car sinon il faudrait réindexer

```json
PUT /mabibli/_mapping/livre
{
  "properties": {
    "ISBN2": {
      "type": "long"
    }
  }
}
```

## Exercice II.2.3)

- -d c'est --data
- `curl -X<METHOD> http://0.0.0.0:9200 -d '<JSON>'`

## Exercice III.1)

- Cherchez le nombre d'avion _ES-Air_ (champ _Carrier_) en tout

```json
GET /kibana_sample_data_flights/_doc/_search
{
  "query" : {
    "term": {
      "Carrier": "ES-Air"
    }
  }
}
```

- Cherchez le nombre d'avion ou New apparaît dans l'aéroport de destination (champ Destfull)

````json
GET /kibana_sample_data_flights/_doc/_search
{
  "query" : {
    "match": {
      "Destfull": "New"
    }
  }
}
```json

- Faire une recherche des avions où *New* apparaît dans le champ *Dest*. Que remarquez vous ?
```json
GET /kibana_sample_data_flights/_doc/_search
{
  "query" : {
    "match": {
      "Dest": "New"
    }
  }
}
````

- Il n'y a pas de résultat car Dest est un champ keyword qui n'est pas indexé en mode fulltext

- Le champ .raw est une version non fulltext d'un champ "text" normalement indexé en fulltext

---

## Exercice I.1)

Objectif: - analyser des logs pour retrouver une information - être attentif au format des logs

Sur le serveur ptych.net le titre d'une page web a été changé. On veut savoir qui des trois administrateurs Alice, Bob ou Jack a fait cette modification.

1. connectez vous en ssh :
   - `ssh -p 12222 enqueteur@ptych.net`
   - passwd: `enqueteur`
1. en utilisant cat et grep par exemple:
   - Pour savoir qui s'est connecté consultez le fichier /var/log/auth.log
   - Pour connaître le titre du site au fil du temps consultez le fichier /var/log/nginx/access.log
1. utilisez `| grep MyWebSite` pour savoir quand le titre a été modifié
1. utilisez `| grep` et l'heure pour savoir qui s'est connecté à cette heure ci

## Exercice I.2)

Calculons la quantité de log que produisent 12 instances d'une application pendant un mois.
Chaque instance = Un serveur web, une application python + une base de données pour toutes les instances

1. Chercher la taille d'un ligne de log ?
1. Combien pèse un caractère ?
1. Comment mesurer la quantité de ligne produite par une application ?
   - on va retenir 200 lignes par minute en moyenne pour le serveur web
   - 120 pour l'application python
   - 60 pour la DB -> c'est très variable
1. Calcul
1. Conclusions...

## Exercice II.1) syntaxe API et JSON

1. Chercher un livre sur http://lalibrairie.com
1. écrire un fichier JSON pour décrire le livre avec:

   - le titre (title)
   - l'auteur (author)
   - le prix (price)
   - la première phrase de la description à mettre entre guillemets (description)
   - d'autres infos si vous voulez

1. Choisissez un nom simple pour votre bibliothèque.
1. Ajoutez ce livre à votre bibliothèque dans Kibana :

```json
PUT /<votre_bibli>/livre/1
<DATA>
```

## Exercice II.2.1) Gérer les documents dans Elasticsearch.

Dans la vue _Devtools_ et à l'aide de votre feuille de mémo de l'API :

1. mettre à jour le livre que vous avez ajouté en changeant le prix
1. ajouter deux nouveaux livre avec la méthode POST
1. lister tous les livres de l'index
1. lister les index présents sur le cluster
1. supprimer le livre numéro 2 (avec son \_id)

## Exercice II.2.2)

1. supprimer votre index
1. Cherchez dans la documentation comment ajouter un mapping
1. Décrivez en JSON les propriétés suivantes pour ce mapping en choisissant les types: title, description, author, price, ISBN/EAN, weight

1. Ajoutez le mapping. Indication : il faut un nouvel index d'abord (mettez 1 shard et 0 replicas)

1. Recréez vos deux livres avec POST sans renseigner l'ISBN
1. ajoutez l'ISBN avec PUT problème
1. ajoutez un champ de type _long_ pour régler le problème

## Exercice II.3) Utiliser curl

1. connectez vous à l'infra en ssh:

```bash
ssh -p 12223 formation@ptych.net
```

l'adresse de elasticsearch est 0.0.0.0:9200

1. taper `curl --help`, cherchez le nom de l'option longue correspondant à `-d` (un petit grep ?)
1. ajouter une suite à l'un de vos livre avec curl.
1. ajoutez une entrée _genre_ de type keyword dans votre mapping et mettez à jours vos livre pour ajouter leur genre
1. utilisez curl pour télécharger une page de la documentation dans votre dossier personnel.

## Exercice III.1)

Avec la vue Devtools:

1. Cherchez le nombre d'avion _ES-Air_ (champ _Carrier_) en tout
1. Cherchez le nombre d'avion ou New apparaît dans l'aéroport de destination (champ Destfull)
1. Faire une recherche des avions où _New_ apparaît dans le champ _Dest_. Que remarquez vous ?
1. Faire une recherche des avions où _New_ apparaît dans le champ _Destfull.raw_. Que remarquez vous ?

---

## 1. Les logs

## 2. La recherche dans elastic search

### 0. Accéder à Kibana

- aller à l'adresse http://81.65.166.101:15601
- section Discover: taper
- Dev tools

### 1. Mettre quelques données dans la base de données

- PUT gnagna

### 2. Requêtes

```json
POST /mabibli/livre/
{
    "<fieldname>": "<value>",
    ...
}
```
