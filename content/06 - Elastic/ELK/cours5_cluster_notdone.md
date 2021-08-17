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

# Plan de la formation

- *Intro)*
- *I)* **Les évènement d'un système et le logging**
- *II)* **Elasticsearch, stocker une masse de données texte**
- *III)* **Elasticsearch et Kibana pour la recherche et l'analyse**
- *IV)* **Architecture d'un cluster ELK**
- *Conclusion)* **Elk et devops**

---

## IV) Architecture d'un cluster ELK
TODO demain

---

## Qu'est-ce qu'un cluster ? 

- Un ensemble de machines qu'on appelle des **noeuds (nodes)**. reliés par un réseau **fiable** et **rapide**.

 *schema*

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