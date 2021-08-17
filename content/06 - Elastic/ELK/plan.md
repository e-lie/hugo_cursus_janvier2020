---
title: Elastic Stack et le logging
theme: moon
separator: "\n>>>\n"
verticalSeparator: "\n---\n"
revealOptions:
    transition: 'none'
---

# Plan

## 0) Petit bilan

0.1 Où en êtes-vous ? Ça va globalement ce cursus super dense ?

- Linux ? on va reparler de tail, grep et des logs
- HTTP ? on va utiliser une API REST (basé sur HTTP)
- JSON ? on va utiliser des documents formatés en JSON
-  Machines virtuelles ? on va parler un peu à la fin de cluster dans le cloud
  (compliqué mais on va surtout voir ça théoriquement)

0.2 Disclaimer

---

## Intro) La stack elastic : une base de données pour rechercher dans des logs
  - gérer une GRANDE quantité de Logs : sur une infrastructure (entre 5 et 1000... machines)
  - les explorer efficacement : un problème difficile vu la quantité
  - En résumé : on va voir trois choses durant ces deux jours qui peuvent résumer ELk
    - les logs pourquoi? comment ?
    - Découvrir elasticsearch et comment chercher dans du texte ?
    - qu'est-ce qu'une infra distribuée moderne ? pourquoi ELK c'est du devops ?
  - ce qu'on va pas faire : installer la stack ELK à la main
  - La hype elasticsearch.

>>>

## I) Les évènements d'un système et le logging

### I.1) Rappel - pourquoi des Logs ?
- comprendre ce qui se trame, prendre connaissance et analyser les évènements d'un système
  -> suivre son comportement (anticiper -> pas que lorsque problèmes)
- afficher les erreurs et les risques potentiels (warning) -> voir les problèmes
- archiver pour consulter a posteriori : analyser sur la longue durée

### exercice:
- analyse de situation (qui a changé le titre ?) et d'usage des outils unix (révision)

---

### I.2) Le problème avec les logs d'une infrastructure
    - quantité

### exercice de calcul : 10 machines, 5 logiciels qui produisent entre 5 et 100 évènements par minute
    -> taille d'une ligne de log ? combien d'octets ?

    - Distribué

    - scalability (passage à l'échelle)
      -facilité à rajouter une machine

  Solution : Elasticsearch + Logstash + Kibana
  récupérer les logs distribués: Logstash

>>>

## II) Elasticsearch, une base de donnée pour la recherche de texte

### II.0) Kibana tête la première

### II.1) Structure de Elasticsearch

schémas !!!

syntaxe de l'API et de JSON rapidement

### CRUD exercices - Partie 1

### II.2) API REST ?
   - HTTP, head & body
   - REST : GET, POST, PUT, DELETE, HEAD endpoint





>>>

## III) Recherche avec Elasticsearch et Kibana
    - Deux types de recherche : exacte et fulltext

### Exercices

### II.2) Comparaison avec les base de données classiques
    - le schéma est facultatif et moins important
    - fait pour chercher plutôt que supporter le modèle des données.





Rappel kibana est une interface pour elastic (soit on attaque direct elastic soit on utilise les trucs pratiques de Kibana)

  *utiliser les données d'aviation*
  expliquer l'interface
  expliquer les graphiques

  idées d'exercices:

  trouver les correspondances possible pour aller d'une ville A à une ville B entre telle et telle heure ?

## Exercice:
    requête pour analyser une erreur dans le code
    graphique sur le volume de connexion au cours de la journée
    corréler des évènements comme l'exemple du début
    ajouter des exemples de plus en plus compliqués
>>>

## III) Life inside a cluster
    - scale In = vertical , scale Out = horizontal
    - shard
    - dimensionner un cluster
    - haute disponibilité
      - endpoint switching déclarer plusieurs
      - fallback automatique

### Exercice:
    - calculer le nombre de noeuds pour que ça marche encore
    - répartir les noeuds sur une infrastructure
    # - configurer et constater qu'on a le bon nombre de noeuds
    - vérifier que quand il y en a un qui tombe ça marche toujours



>>>

## Conclusion) Elasticsearch et le Devops

Qu'est ce que le devops ?
Analyse et automatisation
Elk dans le devops !






>>>

# Logistique

Note: comment ça va se Passer ? #TODO
- feuilles d'exercices
- évaluation
- pauses
- installer la machine virt
# Découvrir Elastic stack et le logging d'infrastructure
*Apprendre à nager dans un océan d'évènements et de texte!*

>>>

# Logistique

Note: comment ça va se Passer ? #TODO 
- feuilles d'exercices
- évaluation
- pauses
- installer la machine virtuelle
- TP

---

## Points en vracs


