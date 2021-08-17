



## III.2) Recherche avec requête multiple et filtre

---

## Des requêtes complexes pour l'analyse

Elasticsearch est puissant pour l'analyse car il permet de combiner un grande quantité de critères de recherche
différent en même temps et de transformer les données récupérer pour les rendre significatives.

Imaginons qu'on veuille chercher tous les avions qui ont décollé de New York sous la pluie depuis un mois et qui ont un prix moyen supérieur à 800$.
Par exemple pour créer une mesure du risque économique que le dérèglement climatique fait peser sur une companie ?

On va devoir écrire une requête complexe.

## Plusieurs outils

- des **requêtes composées**
    tous les vols qui vérifie condition A ET condition B ET PAS condition C
- des **filtres** de requêtes
    garder que les vols dont le prix est entre 300 et 1000 €
- des **aggrégations** de requêtes (somme, aggrégation géographique)
    chercher en gros le chiffre d'affaire d'une companie : faire la somme des trafifs de ses vols.

## Repasser à  Kibana

On pourrait tout faire avec l'API mais ce serait pas très fun et on s'arracherait vite les cheveux.
Donc on 







## III.3) Recherche et analyse 
## VS
## stocker des données d'application

#### Comparaison avec les BDD SQL et NoSQL.

---

## Deux types de BDD d'applications
 - SQL : *des tableaux qu'on peut croiser* = **Jointures**

 exp MySQL, PostgreSQL

 - NoSQL: *des documents qu'on peut filtrer et aggréger*

 exp MongoDB, CouchDB

---

## Le point commun des deux : Stocker des données de base pour une application.

Exp: un Site ou web ou un utilisateur a acheté une liste de produit
- **utilisateur**: login, email, mdp, présentation, age, image de profil
- **produit**: ref, description, prix, photo
- **facture et garantie**: documents complexes mais créés une fois pour toute.

---

**SQL**: On veut avoir un historique des achats et les documents afférents : on relie formellement 
utilisateurs et les produits à travers un historique d'achat.

**NoSQL**: On stocke les factures comme des documents JSON.

---

## Côté SQL:

*ça donne trois tables*

-- schema données liées en SQL

---

## Côté NoSQL:

*des documents JSON qu'on va récupérer avec une référence et un type*

---

## Avec des BDD SQL et NoSQL
- Penser et créer le schéma pour structurer les données d'une application.

- Concevoir correctement pour pas être coincé : Il faut que les donneés soient reliées aux bons endroits et efficacement.

- Combinaison de SQL (données *homogènes*, *cohérentes* et fortement *changeantes*) et NoSQL(données *complexes* mais *moins de cohérence*)

- Effectuer une recherche de texte n'est pas simple.

---

## Elasticsearch : une *sorte* de BDD mais pour la recherche de texte

- Assez proche de MongoDB : on met des documents JSON dedans en HTTP.
- On jette des trucs dedans qu'on voudrais analyser plus tard
- On explore ces éléments en faisant des recherche et des graphiques


## A chaque tache son outil

- Elasticsearch n'est pas conçu pour supporter l'application, seulement la partie recherche / analyse.
- Dans notre cas elasticsearch sert pour travailler sur les logs



