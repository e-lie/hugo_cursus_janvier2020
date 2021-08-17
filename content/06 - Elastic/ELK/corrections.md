Corrections des exercices

[]{#anchor}I.1) Analyse de logs
-------------------------------

Objectif:

-   analyser des logs pour retrouver une information
-   être attentif au format des logs

Sur le serveur ptych.net le titre d'une page web a été changé. On veut
savoir qui des trois administrateurs Alice, Bob ou Jack a fait cette
modification.

-   connectez vous en ssh :

1.  *ssh -p 12222 *[*enqueteur\@ptych.net*](mailto:enqueteur@ptych.net)
2.  passwd: *enqueteur*

-   en utilisant cat et grep par exemple:

    -   Pour savoir qui s'est connecté consultez le fichier
        /var/log/auth.log
    -   Pour connaître le titre du site au fil du temps consultez le
        fichier /var/log/nginx/access.log

-   utilisez *\| grep MyWebSite* pour savoir quand le titre a été
    modifié
-   utilisez *\| grep* et l'heure pour savoir qui s'est connecté à cette
    heure ci
-   Bilan : c'est fastidieux

[]{#anchor}I.2) Calculer une quantité de logs
---------------------------------------------

-   entre 100 et 150 caractères, disons 120.
-   un octet
-   on vide le fichier on laisse tourner une semaine et on compte les
    lignes (*wc -l*)
-   ( 12 \* (200 + 120) + 60 ) 120 \* 60 \* 24 \* 30 = 20217600000 =
    20.2176 GB

C'est une application moyenne. Un infra pour 600.000 utilisateurs
produit par exemple 10 TB en 3 mois.

[]{#anchor}II.1) API JSON
-------------------------

Dans la vue devtools:

PUT /mabibli/livre/1

{

\"title\": \"Awesome Thriller\",

\"author\": \"Stephen King\", // pas de guillemets

\"description\": \"\\\"Un roman haletant\\\"\",

\"price\": 9.80

}

[]{#anchor}II.2.1
=================

-   Afficher votre livre pour se souvenir du prix

GET /mabibli/livre/1/

-   mettre à jour le prix

POST /mabibli/livre/1/\_update

{

\"doc\": {

\"price\": 19.80

}

}

-   ajouter 2 nouveau livres

POST /mabibli/livre/

{

\"title\": \"Awesome Thriller 2\",

\"author\": \"Stephen King\", // pas de guillemets

\"description\": \"\\\"Un roman succulent\\\"\",

\"price\": 9.80

}

POST /mabibli/livre/

{

\"title\": \"Awesome Thriller 3\",

\"author\": \"Stephen Boring\", // pas de guillemets

\"description\": \"\\\"Un roman ennuyant\\\"\",

\"price\": 11.80

}

-   lister tous les livres de l'index

GET /mabibli/livre/\_search

-   lister les index

GET /\_cat/indices

-   Supprimer livre 2 avec l'id récupéré précédemment dans la liste

DELETE /mabibli/livre/Ekd8E2cBVH8Nz7YD6zUt

[]{#anchor}Exercice II.2.2)
---------------------------

-   *DELETE /mabibli*
-   utilisez un moteur de recherche :
    https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-put-mapping.html
-   description du mapping

{

\"properties\": {

\"title\": {

\"type\": \"text\"

},

\"author\": {

\"type\": \"text\"

},

\...

}

-   recréer l'index:

PUT /mabibli

{

\"settings\": {

\"index\": {

\"number\_of\_shards\": 1,

\"number\_of\_replicas\": 0

}

}

}

-   ajouter le mapping

PUT /mabibli/\_mapping/livre

{

\"properties\": {

\"title\": {

\"type\": \"text\"

},

\"author\": {

\"type\": \"text\"

},

\...

}

}

-   créer un livre

POST /mabibli/livre/

{

\"title\": \"Awesome Thriller\",

\"author\": \"Stephen King\",

\"description\": \"\\\"Un roman alletant\\\"\",

\...

}

-   ajouter l'ISBN

POST /mabibli/livre/\<id\>

{

\"doc\": {

\"ISBN\": 9782369350804

}

}

-   problème: l'ISBN est trop grand pour rentrer dans un integer normal.
    Il faut un type long
-   Ajoutons un nouveau car sinon il faudrait réindexer

PUT /mabibli/\_mapping/livre

{

\"properties\": {

\"ISBN2\": {

\"type\": \"long\"

}

}

}

[]{#anchor}Exercice II.2.3)
---------------------------

-   -d c'est --data
-   *curl -X\<METHOD\> http://0.0.0.0:9200 -d \'\<JSON\>\'*

[]{#anchor}Exercice III.1)
--------------------------

-   Cherchez le nombre d'avion *ES-Air* (champ *Carrier*) en tout

GET /kibana\_sample\_data\_flights/\_doc/\_search

{

\"query\" : {

\"term\": {

\"Carrier\": \"ES-Air\"

}

}

}

-   Cherchez le nombre d'avion ou New apparaît dans l'aéroport de
    destination (champ Destfull) *json GET
    /kibana\_sample\_data\_flights/\_doc/\_search { \"query\" : {
    \"match\": { \"Destfull\": \"New\" } } }*json
-   Faire une recherche des avions où *New* apparaît dans le champ
    *Dest*. Que remarquez vous ?

GET /kibana\_sample\_data\_flights/\_doc/\_search

{

\"query\" : {

\"match\": {

\"Dest\": \"New\"

}

}

}

-   Il n'y a pas de résultat car Dest est un champ keyword qui n'est pas
    indexé en mode fulltext
-   Le champ .raw est une version non fulltext d'un champ "text"
    normalement indexé en fulltext

