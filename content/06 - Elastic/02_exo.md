---
title: "2 - Exercice"
draft: false
weight: 3021
---

## Mise en place d'un cluster multi-node

https://www.elastic.co/guide/en/elastic-stack-get-started/current/get-started-docker.html

# Use the Cluster Health API [http://localhost:9200/_cluster/health], the

curl -s localhost:9200/\_cluster/health | jq

<!-- # Node Info API [http://localhost:9200/_cluster/nodes] or GUI tools -->
<!-- curl -s localhost:9200/_nodes | jq -->
<!-- https://www.elastic.co/guide/en/elasticsearch/reference/current/cluster.html -->
<!-- https://www.elastic.co/guide/en/elasticsearch/reference/current/indices.html -->
<!-- https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-stats.html -->
<!-- https://www.elastic.co/guide/en/elasticsearch/reference/current/search.html -->

curl -s localhost:9200/\_cat/nodes

# such as <http://github.com/lukas-vlcek/bigdesk> and

# <http://mobz.github.com/elasticsearch-head> to inspect the cluster state.

## Recherche via l'API

## Pattern-matching

## Exploration du cluster (paramètres)

<!-- https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-xvi-full-text-search -->
