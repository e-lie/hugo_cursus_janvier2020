---
title: "5 - Logstash"
draft: false
weight: 3050
---

## Beats

Beats est un programme designé pour être extrêmement léger et n'avoir qu'une seule mission : **récupérer et envoyer des logs** à un autre programme qui s'assurera du traitement de ceux-ci : soit Logstash, soit directement Elasticsearch.

## Logstash

Logstash est un couteau suisse puissant de récupération, de transformation et d'envoi de logs.
Contrairement à Kibana et Elasticsearch, Logstash peut être utilisé de façon **indépendante** à Elasticsearch ou à Kibana.

- Logstash : récupère les log pour les traiter avant de les envoyer dans Elasticsearch
  - formater des logs
  - transformer les données avant de les mettre dans Elasticsearch

<!-- FIXME: Exemple filtres, principe des inputs/outputs -->

Il est un peu difficile de comprendre la différence fondamentale entre **Beats** et **Logstash** au début, on peut retenir :

- que **Beats** a beaucoup moins de fonctionnalités que Logstash,
- et qu'il n'a que **quelques missions simples** à remplir, là où Logstash est un outil très complet pour récupérer, transformer et renvoyer des logs.

<!-- FIXME: Filebeat -->

diff logstash / filebeat ? https://www.elastic.co/guide/en/beats/filebeat/current/diff-logstash-beats.html

geoip

twitter ?
iptables trop stylé mais trop avancé : juste output exec c bi1

https://www.elastic.co/fr/blog/introducing-logstash-input-http-plugin

LOGSTASH fait du filebeat alors ? https://www.youtube.com/watch?v=RSOC9XqlusQ&list=PLn6POgpklwWrgJXXvbjlFPyHf8Q5a9n2b&index=8
