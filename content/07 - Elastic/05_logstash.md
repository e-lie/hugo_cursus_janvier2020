---
title: "5 - Logstash"
draft: false
weight: 3050
---

## Logstash

Logstash est un couteau suisse puissant de récupération, de transformation et d'envoi de logs.
Contrairement à Kibana et Elasticsearch, Logstash peut être utilisé de façon **indépendante** à Elasticsearch ou à Kibana.

- Logstash : récupère les logs pour les traiter avant de les envoyer dans Elasticsearch
  - formater des logs
  - transformer les données avant de les mettre dans Elasticsearch

Logstash a trois grandes parties :

- les inputs, là où Logstash récupère ou reçoit ses données, (en général, c'est Beats)
- les filters, la partie importante, celle où les données reçues sont transformées avant envoi
- les outputs, là où on indique à Logstash où envoyer ses données (en général : vers Elasticsearch)

<!-- FIXME: Exemple filtres, principe des inputs/outputs -->

### Exemples

#### Inputs

- des tweets issus de l'API Twitter
- des packets HTTP

#### Filters

- Dissect (basique, en fonction d'un séparateur) et Grok (avancé, expressions régulières) : découper des messages de logs non structurés en plusieurs entrées différentes (par exemple découper chaque info d'une ligne de logs de Nginx dans des entrées différentes)

  - Pour Grok, il existe un site indispensable pour débugger ses filtres Grok : https://grokdebug.herokuapp.com/

- Geoip : ajouter des infos géographiques à partir d'une adresse IP

#### Outputs

- Elasticsearch
- l'exécution d'une ligne de commande (par exemple `iptables` pour couper l'accès firewall à une IP après avoir détecté une attaque)

### Rappel : Beats et Logstash

Il est un peu difficile de comprendre la différence fondamentale entre **Beats** et **Logstash** au début, on peut retenir :

- que **Beats** a beaucoup moins de fonctionnalités que Logstash et est designé pour être très léger, et n'avoir que **quelques missions simples** à remplir
- là où Logstash est un outil très complet pour récupérer, transformer et renvoyer des logs.

Pour plus de détails : https://www.elastic.co/guide/en/beats/filebeat/current/diff-logstash-beats.html

### Beats avec Logstash

Souvent, parce que leurs missions sont complémentaires, on associe des Beats qui envoient leurs logs bruts à Logstash, qui s'assure du traitement et de la transformation des logs

<!--
geoip

twitter ?
iptables trop stylé mais trop avancé : juste output exec c bi1

https://www.elastic.co/fr/blog/introducing-logstash-input-http-plugin

LOGSTASH fait du filebeat alors ? https://www.youtube.com/watch?v=RSOC9XqlusQ&list=PLn6POgpklwWrgJXXvbjlFPyHf8Q5a9n2b&index=8 --> -->
