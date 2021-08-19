---
title: "4 - Beats"
draft: false
weight: 3040
---

https://www.youtube.com/watch?v=X3OXO4C0wR8&list=PLn6POgpklwWrgJXXvbjlFPyHf8Q5a9n2b&index=11

<!-- FRegrouper avec Logstash ? -->

## Beats

Beats est un programme designé pour être extrêmement léger et n'avoir qu'une seule mission : **récupérer et envoyer des logs** à un autre programme qui s'assurera du traitement de ceux-ci : soit Logstash, soit directement Elasticsearch.

- Les **Beats** pour lire les données depuis plusieurs machines. Les principales sont :

  - **FileBeat** : lire des fichiers de log pour les envoyer à **Logstash** ou directement à **Elasticsearch**
  - **MetricBeat** : récupérer des données d'usage, du CPU, de la mémoire, du nombre de process NGINX
  <!-- - etc -->

- Logstash : récupère les log pour les traiter avant de les envoyer dans Elasticsearch
  - formater des logs
  - transformer les données avant de les mettre dans Elasticsearch

## Logstash

Logstash est un couteau suisse puissant de récupération, de transformation et d'envoi de logs.
Contrairement à Kibana et Elasticsearch, Logstash peut être utilisé de façon **indépendante** à Elasticsearch ou à Kibana.

<!-- FIXME: Exemple filtres, principe des inputs/outputs -->

Il est un peu difficile de comprendre la différence fondamentale entre **Beats** et **Logstash** au début, on peut retenir :

- que **Beats** a beaucoup moins de fonctionnalités que Logstash,
- et qu'il n'a que quelques missions simples à remplir, là où Logstash est un outil très complet pour récupérer, transformer et renvoyer des logs.

<!-- FIXME: Filebeat -->
