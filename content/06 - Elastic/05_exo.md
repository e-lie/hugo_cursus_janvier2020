---
title: "5 - Logstash - Exercices"
draft: true
weight: 3051
---

## Dissect ou Grok avec Nginx

Sur votre ordinateur ou dans une VM créé par Vagrant, faire du Logstash en utilisant le filtre Dissect ou Grok et des logs Nginx récupérés par Filebeat en suivant ce tutoriel : https://blog.zwindler.fr/2017/10/10/elasticstack-collecter-et-exploiter-des-logs-nginx/

## L'input Twitter

Avec une clé d'API Twitter (à demander éventuellement au formateur), configurer l'input Twitter de Logstash pour archiver des tweets dans Elasticsearch.
https://www.elastic.co/guide/en/logstash/current/plugins-inputs-twitter.html

<!-- ## Optionnel : Le filtre Geoip sur des IP Nginx

https://www.elastic.co/guide/en/logstash/7.13/plugins-filters-geoip.html -->

<!--
https://logz.io/blog/docker-logging/


geoip

twitter : https://www.youtube.com/watch?v=PCvVCjC-wp0&list=PLn6POgpklwWrgJXXvbjlFPyHf8Q5a9n2b&index=27
https://www.elastic.co/guide/en/logstash/current/plugins-inputs-twitter.html

iptables trop stylé mais trop avancé : juste output exec c bi1

https://www.elastic.co/fr/blog/introducing-logstash-input-http-plugin -->

https://grokdebug.herokuapp.com/

https://www.elastic.co/guide/en/logstash/7.14/plugins-filters-dissect.html
