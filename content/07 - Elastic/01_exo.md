---
title: "1 - Installation"
draft: false
weight: 3011
---

# TP 1 : installation d'Elasticsearch, Kibana et Filebeat

## Installer Elasticsearch avec Ansible

1. Clonez le dépôt situé à cette adresse : <https://github.com/Uptime-Formation/vagrant-ansible-elk>
2. Créez les VM avec `vagrant up` (il faut installer Vagrant et VirtualBox avant si ce n'est pas fait)
3. Vagrant a de lui-même lancé `ansible-playbook ping.yml`, il teste donc qu'Ansible est bien configuré.
4. Lancez `ansible-playbook setup_elastic.yml`. Les requirements
   sont installés ! Voyez les _ok_ et _changed_ apparaissant lorsque vous
   lancez le playbook : Ansible est verbeux, il informe de sa réussite.

### Rappels Ansible :

- Ansible peut être rejoué plusieurs fois (il est idempotent)
- Ansible garantit l'état de certains éléments du système lorsqu'on le
  (re)joue
- Ansible est (dès qu'on est un peu habitué-e) plus limpide que du bash

### Configurer Elastic en cluster

1.  Observez le fichier `templates/elasticsearch.yml.j2` : c'est modèle de
    fichier de configuration. Il contient des trous `{{ ma_variable }}` qui doivent être remplis par les variables du playbook

1.  Jouer le playbook complet.
1.  Lancez les commandes de diagnostic

- `curl http://192.168.2.2:9200/_cat/nodes?pretty`
- `curl -XGET http://192.168.2.2:9200/_cluster/state?pretty`
- `curl -XGET http://192.168.2.2:9200/_cluster/health?pretty`

Si tout est bien configuré vous devriez voir une liste de deux nœuds
signifiant que les deux elastic se « connaissent »

- Pour ajouter un nouveau nœud !

  - ajoutez une nouvelle machine dans Vagrant
  - l'ajouter au fichier `hosts.cfg` dans le groupe `elastic_nodes`
  - ajoutez la nouvelle IP dans la variable `elk_node_ips`

- relancer le playbook : **\#magic**

### Installer Kibana

- Lancer : **ansible-playbook setup_kibana.yml**

- Accéder à `192.168.2.2:5601` dans Firefox 😃

## Installer Elasticsearch avec Docker Compose

{{% expand "`docker-compose.yml` :" %}}

```
version: "3.8"
services:
  es01:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.14.0
    container_name: es01
    environment:
      - node.name=es01
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es02,es03
      - cluster.initial_master_nodes=es01,es02
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
      - xpack.security.enabled=false
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data01:/usr/share/elasticsearch/data
    ports:
      # - 9200:9200
      - 9201:9200
    networks:
      - elastic

  es02:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.14.0
    container_name: es02
    ports:
      - 9202:9200
    environment:
      - node.name=es02
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es03
      - cluster.initial_master_nodes=es01,es02
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
      - xpack.security.enabled=false
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data02:/usr/share/elasticsearch/data
    networks:
      - elastic

  es03:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.14.0
    container_name: es03
    ports:
      - 9203:9200
    environment:
      - node.name=es03
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es02
      - cluster.initial_master_nodes=es01,es02
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
      - xpack.security.enabled=false
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data03:/usr/share/elasticsearch/data
    networks:
      - elastic

  kibana:
    image: docker.elastic.co/kibana/kibana:7.14.0
    ports:
      - 5601:5601
    networks:
      - elastic
    environment:
          ELASTICSEARCH_HOSTS: '["http://es01:9200","http://es02:9200","http://es03:9200"]'


volumes:
  data01:
    driver: local
  data02:
    driver: local
  data03:
    driver: local

networks:
  elastic:
    driver: bridge

```

<!-- FIXME: aplatir réseau ou exposer ports logstash / elasticsearch pour pouvoir envoyer logs type ceux d'un nginx local (mais en même temps un nginx local c'est une histoire de filebeat) -->
<!--
```
version: "3.8"
services:
  es01:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.14.0
    container_name: es01
    labels:
      co.elastic.logs/json.keys_under_root: "false"
      co.elastic.logs/json.add_error_key: "true"
      co.elastic.logs/json.message_key: "message"
    environment:
      - node.name=es01
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es02,es03
      - cluster.initial_master_nodes=es01,es02
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
      - xpack.security.enabled=false
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data01:/usr/share/elasticsearch/data
    ports:
      # - 9200:9200
      - 9201:9200
    networks:
      - elastic

  es02:
    labels:
      co.elastic.logs/json.keys_under_root: "false"
      co.elastic.logs/json.add_error_key: "true"
      co.elastic.logs/json.message_key: "message"
    image: docker.elastic.co/elasticsearch/elasticsearch:7.14.0
    container_name: es02
    ports:
      - 9202:9200
    environment:
      - node.name=es02
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es03
      - cluster.initial_master_nodes=es01,es02
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
      - xpack.security.enabled=false
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data02:/usr/share/elasticsearch/data
    networks:
      - elastic

  es03:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.14.0
    container_name: es03
    ports:
      - 9203:9200
    environment:
      - node.name=es03
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es02
      - cluster.initial_master_nodes=es01,es02
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
      - xpack.security.enabled=false
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data03:/usr/share/elasticsearch/data
    networks:
      - elastic
    labels:
      co.elastic.logs/json.keys_under_root: "false"
      co.elastic.logs/json.add_error_key: "true"
      co.elastic.logs/json.message_key: "message"

  # logstash:
  #   image: docker.elastic.co/logstash/logstash:7.14.0
  #   depends_on:
  #     - elasticsearch
  #   ports:
  #     - 12201:12201/udp
  #   volumes:
  #     - ./logstash.conf:/usr/share/logstash/pipeline/logstash.conf:ro
  #   networks:
  #     - logging-network

  filebeat:
    image: docker.elastic.co/beats/filebeat:7.14.0
    user: root
    volumes:
      - ./filebeat.yml:/usr/share/filebeat/filebeat.yml:ro
      - /var/lib/docker/containers:/var/lib/docker/containers:ro
      - /var/run/docker.sock:/var/run/docker.sock:ro
    networks:
      - elastic
    environment:
      - -strict.perms=false

  kibana:
    image: docker.elastic.co/kibana/kibana:7.14.0
    ports:
      - 5601:5601
    networks:
      - elastic
    environment:
          ELASTICSEARCH_HOSTS: '["http://es01:9200","http://es02:9200","http://es03:9200"]'
    labels:
      co.elastic.logs/json.keys_under_root: "true"
      co.elastic.logs/json.add_error_key: "true"
      co.elastic.logs/json.message_key: "message"
      co.elastic.logs/json. expand_keys: "true"

  # httpd:
  #   image: httpd:latest
  #   depends_on:
  #     - logstash
  #   ports:
  #     - 80:80
  #   logging:
  #     driver: gelf
  #     options:
  #       # Use udp://host.docker.internal:12201 when you are using Docker Desktop for Mac
  #       # docs: https://docs.docker.com/docker-for-mac/networking/#i-want-to-connect-from-a-container-to-a-service-on-the-host
  #       # issue: https://github.com/lvthillo/docker-elk/issues/1
  #       gelf-address: "udp://localhost:12201"


volumes:
  data01:
    driver: local
  data02:
    driver: local
  data03:
    driver: local

networks:
  elastic:
    driver: bridge

``` -->

{{% /expand %}}

<!--
https://raw.githubusercontent.com/elastic/beats/7.10/deploy/docker/filebeat.docker.yml

=> TP Docker compose FIlebeat
=> TP Vagrant ELK multinode ? => avec ansible ? ==> mini sondage en methode pref ? : docker compose / vagrant avec ansible / vagrant vide +ansible sans provisioner / vagrant k3s ou k3s simple / vagrant ou cloud + bash ? et/ou à la main ? (bash sans script)
=> TP compose/k8S ELK multinode ?
https://discuss.elastic.co/t/nginx-filebeat-elk-docker-swarm-help/130512 -->

<!-- FIXME:
    connectez vous en ssh : ssh -p 12222 enqueteur@ptych.net passwd: enqueteur
 -->

<!-- https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery-bootstrap-cluster.html#modules-discovery-bootstrap-cluster-joining -->

<!--
## Mise en place d'un cluster multi-node

https://www.elastic.co/guide/en/elastic-stack-get-started/current/get-started-docker.html

# Use the Cluster Health API [http://localhost:9200/_cluster/health], the

curl -s localhost:9200/\_cluster/health | jq -->

<!-- # Node Info API [http://localhost:9200/_cluster/nodes] or GUI tools -->
<!-- curl -s localhost:9200/_nodes | jq -->
<!-- https://www.elastic.co/guide/en/elasticsearch/reference/current/cluster.html -->
<!-- https://www.elastic.co/guide/en/elasticsearch/reference/current/indices.html -->
<!-- https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-stats.html -->
<!-- https://www.elastic.co/guide/en/elasticsearch/reference/current/search.html -->
<!--
curl -s localhost:9200/\_cat/nodes

# such as <http://github.com/lukas-vlcek/bigdesk> and

# <http://mobz.github.com/elasticsearch-head> to inspect the cluster state.

## Recherche via l'API -->

<!-- https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery.html -->
