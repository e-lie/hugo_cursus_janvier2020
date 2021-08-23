---
title: "6 - Conclusion"
draft: false
weight: 3060
---

<!-- https://github.com/Yelp/elastalert
https://github.com/opendistro-for-elasticsearch/alerting

## IV) Architecture d'un cluster ELK

TODO demain -->

---

## Qu'est-ce qu'un cluster ?

- Un ensemble de machines qu'on appelle des **noeuds (nodes)** reliés par un réseau **fiable** et **rapide**.

  <!-- _schema_ -->

<!-- ---

---

---

## Passage à l'échelle (**Scalability**)

- scale-in ou verticalement : machine plus puissante, "grosse"
- scale-out horizontalement : plus de machines, distribution.

--- -->

## Haute disponibilité

Une application en _haute disponibilité_ signifie qu'elle continue à fonctionner quand une partie arrête de fonctionner (dans le cadre d'Elasticsearch : quand un nœud devient injoignable par exemple).

<!-- Pour cela elle doit avoir :
- un healthcheck automatique
- de l'auto-balancing -->
  <!-- - déploiement progressif TODO -->
  <!--
  **Les mécanismes de haute disponibilité d'un cluster commencent réellement à partir de 3 nœuds : il faut 2 nœuds restants pour continuer leur service en conditions optimales sans le 3e nœud défectueux.** -->

## Santé d'un cluster / d'un indice

La santé d'un cluster ou d'un index est déterminée par trois couleurs dans Elasticsearch :

- vert, tout va bien, la haute disponibilité fonctionne
- jaune, il n'y a pas de redondance : si un nœud devient injoignable ou par exemple son disque casse, il y a un risque de perdre des données ou de perdre un accès à des données
- rouge, des données sont introuvables/perdues

## Elasticsearch est élastique/distribué

- Une application distribuée a plusieurs instances (identiques ou non) qui communiquent entre elles.
- Par exemple des noeuds Elastc contiennent chacun une partie des données :
  - On peut ajouter des noeuds et un index va automatiquement répartir les données entre les nœuds : dans Elasticsearch, on appelle ça le _sharding_ (partition en français), les données sont copiées en plusieurs _replicas_.

---

## Le scripting dans Elasticsearch

- Dans Elasticsearch, il est possible d'utiliser un langage de programmation spécial pour faire des opérations sur les données et les réindexer (ou non).
- Les principaux langages pour scripter sont `painless`, un langage fait exprès par Elasticsearch, et `Java` (avancé).
- Plus d'infos : https://www.elastic.co/guide/en/elasticsearch/reference/7.14/modules-scripting.html

## Elastic Common Schema (ECS)

- Quand on veut optimiser le fait de donner des infos à Elasticsearch avec notre application, on exporte nos logs en JSON. ECS est simplement une façon de standardiser certains champs JSON utiles à fournir à Elasticsearch.

<!--
# Formule pour calculer

le nombre de noeuds pour avoir du vert -->

<!-- ---

# règle pour construire un cluster -->

<!-- ---

# Exercice IV.1) Conçevons un cluster Elastic

- Avec 6 noeuds, 3 DC
- haute disponibilité -->

<!--
# QCM Module Elastic stack

mode de notation :
    + 1 par bonne réponse
    - 1 par mauvaise réponse cochée


le type "keyword" est utilisé
    pour les recherche exactes
    pour les recherches partielles (fulltext)
    pour filtrer
    pour aggréger
    pour classer

le type "text" est utilisé
    ...


Le programme curl permet de ?
    http
    ftp
    appeler les fonctions d'une API REST
    ssh -->
