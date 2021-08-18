---
title: "6 - Conclusion"
draft: false
weight: 3060
---

https://github.com/Yelp/elastalert
https://github.com/opendistro-for-elasticsearch/alerting

## IV) Architecture d'un cluster ELK

TODO demain

---

## Qu'est-ce qu'un cluster ?

- Un ensemble de machines qu'on appelle des **noeuds (nodes)**. reliés par un réseau **fiable** et **rapide**.

  _schema_

---

## Application distribuéé

- Une application avec plusieurs instances (identiques ou non). qui communiquent entre elle.
- Par exemple des noeuds elastics qui contiennent chacun une partie des données.

---

## Haute disponibilité

L'application

- avoir un health check automatique
- auto-balancing
- déploiement progressif TODO

---

## Passage à l'échelle (**Scalability**)

- scale in ou verticalement : machine plus puissante "grosse"
- scale out horizontalement : plus de machines
  - distribution.

---

## Elasticsearch est elastic

on peut ajouter des noeuds et un indexe va automatiquement se répartir

---

# Formule pour calculer

le nombre de noeuds pour avoir du vert

---

# règle pour construire un cluster

---

# Exercice IV.1) Conçevons un cluster Elastic

- Avec 6 noeuds, 3 DC
- haute disponibilité

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
