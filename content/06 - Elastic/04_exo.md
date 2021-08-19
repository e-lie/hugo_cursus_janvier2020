---
title: "4 - Exercices"
draft: false
weight: 3040
---

<!-- https://logz.io/blog/docker-logging/ -->

https://logz.io/blog/docker-stats-monitoring-dockbeat/

https://raw.githubusercontent.com/elastic/beats/7.10/deploy/docker/filebeat.docker.yml

- TP Docker FIlebeat

Input container + TCP : https://www.youtube.com/watch?v=_eQWPGpJZ1k&list=PLn6POgpklwWrgJXXvbjlFPyHf8Q5a9n2b&index=12

Input type log : https://www.youtube.com/watch?v=X3OXO4C0wR8&list=PLn6POgpklwWrgJXXvbjlFPyHf8Q5a9n2b&index=11

Filebeats module nginx : https://www.youtube.com/watch?v=X0FY1XeHtmI&list=PLn6POgpklwWrgJXXvbjlFPyHf8Q5a9n2b&index=9
https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-nginx.html

<http://localhost:5601/app/kibana#/home/tutorial/elasticsearchMetrics?_g=()>

Exemples de fichier de logs

    auth.log : connexion des utilisateurs au système
    httpd.log : connexion au serveur web apache
    mail.log : (aussi bien envoi que réception donc plusieurs applications)
    nginx/access.log : connexion au serveur web nginx
