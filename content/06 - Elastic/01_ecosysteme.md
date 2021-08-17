---
title: "1 - Découverte de l'écosystème Elastic"
draft: false
weight: 3010
---

# La suite Elastic

La suite Elastic, historiquement appelée "ELK", est une combinaison de plusieurs produits de la société Elastic, qui développe des logiciels :

- de base de données distribuée (Elasticsearch)
- de dashboard / d'interface graphique pour explorer des données (Kibana)
- de logging / monitoring (Logstash et Beats)

## Elasticsearch

Elasticsearch est à la fois :

- une base de données distribuée (plusieurs instances de la base de données sont connectées entre elles de manière à assurer de la **redondance** si un des nœuds en vient à avoir des problèmes)
- un moteur de recherche puissant, basé sur un autre logiciel appelé Apache Lucene

Elle fait partie des bases de données de type NoSQL :

<!-- FIXME: NoSQL -->
<!-- FIXME: vocab nœuds et cluster -->
<!-- FIXME: Lucene et KQL -->

## Kibana

Kibana est un outil très complet de visualisation et d'administration des données dans une base de données Elasticsearch.
Elle est toujours connectée à un cluster (un ou plusieurs nœuds) Elasticsearch.

<!-- FIXME: différentes parties Kibana -->
<!-- FIXME: KQL -->

## Logstash

Logstash est un couteau suisse puissant de récupération, de transformation et d'envoi de logs.
Contrairement à Kibana et Elasticsearch, Logstash peut être utilisé de façon **indépendante** à Elasticsearch ou à Kibana.

<!-- FIXME: Exemple filtres, principe des inputs/outputs -->

## Beats

Beats est un ajout récent à la suite Elastic. C'est un programme designé pour être extrêmement léger et n'avoir qu'une seule mission : envoyer des logs à un autre programme qui s'assurera du traitement de ceux-ci.

Il est un peu difficile de comprendre la différence fondamentale entre **Beats** et **Logstash** au début, on peut retenir :

- que **Beats** a beaucoup moins de fonctionnalités que Logstash,
- et qu'il n'a que quelques missions simples à remplir, là où Logstash est un outil très complet pour récupérer, transformer et renvoyer des logs.

<!-- FIXME: Filebeat -->

## Elastic APM

APM est le petit dernier d'Elastic.

<!-- Monitoring ? -->

## L'écosystème Elastic

<!-- FIXME: ecosysteme, open core premium pas premium, truc de security truc, ElastAlert -->

## <!-- FIXME: lien vers site de stacks monitoring pour dire que c compliké -->

![](../../images/elastic/elastic_stack.png)
