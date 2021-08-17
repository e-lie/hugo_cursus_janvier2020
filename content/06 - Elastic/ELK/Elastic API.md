API Elasticsearch memento - Version 6.4 de l'API

[]{#anchor}Gérer les documents
==============================

### []{#anchor}Créer un document:

PUT /\<index\>/\<type\>/1

{

\"champ1\": \"value1\",

\"champ2\": \"value2\"

}

ou

POST /\<index\>/\<type\>

{

\"champ1\": \"value1\",

\"champ2\": \"value2\"

}

### []{#anchor}Afficher un document:

GET /\<index\>/\<type\>/\<num\>/

### []{#anchor}Lister tous les documents:

GET /\<index\>/\<type\>/\_search

### []{#anchor}Mettre à jour un document (ajouter modifier un champ)

POST /\<index\>/\<type\>/\<num\>/\_update

{

\"doc\": {

\"field\": \"value\"

}

}

### []{#anchor}Supprimer un document

DELETE /\<index\>/\<type\>/\<\_id\>

[]{#anchor}Gérer les index
==========================

### []{#anchor}List Indices

GET /\_cat/indices

-   avec le nom des colonnes

GET /\_cat/indices?v

### []{#anchor}Create index

PUT /\<index\>

{

\"settings\": {

\"number\_of\_shards\": 1, // default 5

\"number\_of\_replicas\": 0 // default 1

}

}

#### []{#anchor}Avec un mapping directement

PUT /\<index\>

{

\"settings\": {

\"index\": {

\"number\_of\_shards\": 1,

\"number\_of\_replicas\": 0

}

},

\"mappings\": {

\"\<mapping\>\": {

\"properties\": {

\"\<property\>\": {

\"type\": \"\<datatype\>\"

},

\...

}

}

}

}

### []{#anchor}Supprimer un index

DELETE /\<index\>

[]{#anchor}Gérer les mappings
=============================

#### []{#anchor}Lister les mappings

GET /\<index\>/\_mapping

#### []{#anchor}Ajouter un champ à un mapping:

PUT /\<index\>/\_mapping/\<type\>

{

\"properties\": {

\"\<new\_fieldname\>\": {

\"type\": \"\<datatype\>\"

}

}

}

[]{#anchor}Réindexer des données
================================

#### []{#anchor}Dupliquer un champ et réindexer

POST /\<index\>/\_update\_by\_query

{

\"script\": {

\"inline\": \"ctx.\_source.\<fieldname\> = ctx.\_source.\<fieldname\>\"

}

}
