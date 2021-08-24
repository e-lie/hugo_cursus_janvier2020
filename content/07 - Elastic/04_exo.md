---
title: "4 - Beats - Exercices"
draft: false
weight: 3040
---

<!-- https://logz.io/blog/docker-logging/ -->

## Filebeat avec Nginx

Nous allons suivre la **partie Filebeat** (il faut descendre jusqu'à environ la moitié de la page) du tutoriel officiel Elastic pour monitorer les logs `access.log` et `error.log` de Nginx :
https://www.elastic.co/fr/blog/how-to-monitor-nginx-web-servers-with-the-elastic-stack

Nous pouvons ensuite utiliser une commande spéciale pour ajouter des tableaux pré-configurés pour Nginx et Kibana avec la commande suivante :
`sudo ./filebeat setup --dashboards`

<!-- https://logz.io/blog/docker-stats-monitoring-dockbeat/ -->

<!-- https://raw.githubusercontent.com/elastic/beats/7.10/deploy/docker/filebeat.docker.yml -->

<!-- - TP Docker FIlebeat -->

<!-- Input container + TCP : https://www.youtube.com/watch?v=_eQWPGpJZ1k&list=PLn6POgpklwWrgJXXvbjlFPyHf8Q5a9n2b&index=12 -->

<!-- Input type log : https://www.youtube.com/watch?v=X3OXO4C0wR8&list=PLn6POgpklwWrgJXXvbjlFPyHf8Q5a9n2b&index=11 -->

<!-- Filebeats module nginx : https://www.youtube.com/watch?v=X0FY1XeHtmI&list=PLn6POgpklwWrgJXXvbjlFPyHf8Q5a9n2b&index=9 -->
<!-- https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-nginx.html -->

<!-- <http://localhost:5601/app/kibana#/home/tutorial/elasticsearchMetrics?_g=()> -->

## Optionnel : `auth.log`

Avec Filebeat, envoyez le contenu du fichier `auth.log` (logs de connexion des utilisateurs au système) à Elasticsearch grâce au module appelé `system` : https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-system.html

## Optionnel : Metricbeat pour Docker

Suivre ce tutoriel sur un host avec Docker d'installé : https://www.elastic.co/guide/en/beats/metricbeat/current/metricbeat-module-docker.html

## Optionnel : Metricbeat dans et pour Kubernetes

Avec un Kubernetes joignable (par exemple `k3s`), suivre ce guide : https://www.elastic.co/guide/en/beats/metricbeat/current/running-on-kubernetes.html
