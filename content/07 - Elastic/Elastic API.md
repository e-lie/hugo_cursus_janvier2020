---
title: API Elasticsearch memento - Version 7.14 de l'API
---

# Gérer les documents

Source : https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-index_.html

### Créer un document

```json
PUT /<index>/_doc/1
{
  "champ1": "value1",
  "champ2": "value2"
}

```

ou

```json
POST /<index>/_doc
{
  "champ1": "value1",
  "champ2": "value2"
}

```

### Afficher un document:

```json
GET /<index>/_doc/<num>/
```

### Lister tous les documents:

```json
GET /<index>/_search
```

### Mettre à jour un document (ajouter/modifier un champ)

```json
POST /<index>/_update/<num>/
{
    "doc": {
        "field": "value"
    }
}
```

### Supprimer un document

```json
DELETE /<index>/_doc/<_id>
```

# Gérer les index

### List Indices

```
GET /_cat/indices
```

- avec le nom des colonnes

```
GET /_cat/indices?v
```

### Create index

```json
PUT /<index>
{
    "settings": {
        "number_of_shards": 1, // default 5
        "number_of_replicas": 0 // default 1
    }
}
```

#### Avec un mapping directement

```json
PUT /<index>
{
  "settings": {
    "index": {
      "number_of_shards": 1,
      "number_of_replicas": 0
    }
  },
  "mappings": {
      "properties": {
        "<property>": {
          "type": "<datatype>"
        }
      }
    }
  }
}
```

### Supprimer un index

```json
DELETE /<index>
```

# Gérer les mappings

#### Lister les mappings

```
GET /<index>/_mapping
```

#### Ajouter un champ à un mapping:

```json
PUT /<index>/_mapping
{
  "properties": {
    "<new_fieldname>": {
      "type": "<datatype>"
    }
  }
}
```

# Réindexer des données

#### Dupliquer un champ et réindexer

```json
POST /<index>/_update_by_query
{
  "script": {
    "inline": "ctx._source.<fieldname> = ctx._source.<fieldname>"
  }
}
```
