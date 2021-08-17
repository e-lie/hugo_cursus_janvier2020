Feuille d'exercices - Partie 1

[]{#anchor}Exercice I.1)
------------------------

Objectif: - analyser des logs pour retrouver une information - être
attentif au format des logs

Sur le serveur ptych.net le titre d'une page web a été changé. On veut
savoir qui des trois administrateurs Alice, Bob ou Jack a fait cette
modification.

1.  connectez vous en ssh :

    -   *ssh -p 12222 enqueteur\@ptych.net*
    -   passwd: *enqueteur*

2.  en utilisant cat et grep par exemple:

    -   Pour savoir qui s'est connecté consultez le fichier
        /var/log/auth.log
    -   Pour connaître le titre du site au fil du temps consultez le
        fichier /var/log/nginx/access.log

3.  utilisez *\| grep MyWebSite* pour savoir quand le titre a été
    modifié
4.  utilisez *\| grep* et l'heure pour savoir qui s'est connecté à cette
    heure ci

[]{#anchor}Exercice I.2)
------------------------

Calculons la quantité de log que produisent 12 instances d'une
application pendant un mois. Chaque instance = Un serveur web, une
application python + une base de données pour toutes les instances

1.  Chercher la taille d'un ligne de log ?
2.  Combien pèse un caractère ?
3.  Comment mesurer la quantité de ligne produite par une application ?

    -   on va retenir 200 lignes par minute en moyenne pour le serveur
        web
    -   120 pour l'application python
    -   60 pour la DB -\> c'est très variable

4.  Calcul
5.  Conclusions...

[]{#anchor}Exercice II.1) syntaxe API et JSON
---------------------------------------------

1.  Chercher un livre sur http://lalibrairie.com
2.  écrire un fichier JSON pour décrire le livre avec:

    -   le titre (title)
    -   l'auteur (author)
    -   le prix (price)
    -   la première phrase de la description à mettre entre guillemets
        (description)
    -   d'autres infos si vous voulez

3.  Choisissez un nom simple pour votre bibliothèque.
4.  Ajoutez ce livre à votre bibliothèque dans Kibana :

PUT /\<votre\_bibli\>/livre/1

\<DATA\>

[]{#anchor}Exercice II.2.1) Gérer les documents dans Elasticsearch.
-------------------------------------------------------------------

Dans la vue *Devtools* et à l'aide de votre feuille de mémo de l'API :

1.  mettre à jour le livre que vous avez ajouté en changeant le prix
2.  ajouter deux nouveaux livre avec la méthode POST
3.  lister tous les livres de l'index
4.  lister les index présents sur le cluster
5.  supprimer le livre numéro 2 (avec son \_id)

[]{#anchor}Exercice II.2.2)
---------------------------

1.  supprimer votre index
2.  Cherchez dans la documentation comment ajouter un mapping
3.  Décrivez en JSON les propriétés suivantes pour ce mapping en
    choisissant les types: title, description, author, price, ISBN/EAN,
    weight
4.  Ajoutez le mapping. Indication : il faut un nouvel index d'abord
    (mettez 1 shard et 0 replicas)
5.  Recréez vos deux livres avec POST sans renseigner l'ISBN
6.  ajoutez l'ISBN avec PUT problème
7.  ajoutez un champ de type *long* pour régler le problème

[]{#anchor}Exercice II.3) Utiliser curl
---------------------------------------

1.  connectez vous à l'infra en ssh:

ssh -p 12223 formation\@ptych.net

l'adresse de elasticsearch est 0.0.0.0:9200

1.  taper *curl \--help*, cherchez le nom de l'option longue
    correspondant à *-d* (un petit grep ?)
2.  ajouter une suite à l'un de vos livre avec curl.
3.  ajoutez une entrée *genre* de type keyword dans votre mapping et
    mettez à jours vos livre pour ajouter leur genre
4.  utilisez curl pour télécharger une page de la documentation dans
    votre dossier personnel.

[]{#anchor}Exercice III.1)
--------------------------

Avec la vue Devtools: 1. cherchez le nombre d'avion *ES-Air* (champ
*Carrier*) en tout 1. cherchez le nombre d'avion ou New apparaît dans
l'aéroport de destination (champ Destfull) 1. faire une recherche des
avions où *New* apparaît dans le champ *Dest*. Que remarquez vous ?
