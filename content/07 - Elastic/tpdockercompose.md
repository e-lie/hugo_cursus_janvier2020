---
title: "Exercice bonus - ELK et Docker Compose"
draft: true
---

## Une stack Elastic

### Centraliser les logs

L'utilité d'Elasticsearch est que, grâce à une configuration très simple de son module Filebeat, nous allons pouvoir centraliser les logs de tous nos conteneurs Docker.
Pour ce faire, il suffit d'abord de télécharger une configuration de Filebeat prévue à cet effet :

```bash
curl -L -O https://raw.githubusercontent.com/elastic/beats/7.10/deploy/docker/filebeat.docker.yml
```

Renommons cette configuration et rectifions qui possède ce fichier pour satisfaire une contrainte de sécurité de Filebeat :

```bash
mv filebeat.docker.yml filebeat.yml
sudo chown root filebeat.yml
```

Enfin, créons un fichier `docker-compose.yml` pour lancer une stack Elasticsearch :

```yaml
version: "3"

services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.5.0
    environment:
      - discovery.type=single-node
      - xpack.security.enabled=false
    networks:
      - logging-network

  filebeat:
    image: docker.elastic.co/beats/filebeat:7.5.0
    user: root
    depends_on:
      - elasticsearch
    volumes:
      - ./filebeat.yml:/usr/share/filebeat/filebeat.yml:ro
      - /var/lib/docker/containers:/var/lib/docker/containers:ro
      - /var/run/docker.sock:/var/run/docker.sock:ro
    networks:
      - logging-network
    environment:
      - -strict.perms=false

  kibana:
    image: docker.elastic.co/kibana/kibana:7.5.0
    depends_on:
      - elasticsearch
    ports:
      - 5601:5601
    networks:
      - logging-network

networks:
  logging-network:
    driver: bridge
```

Il suffit ensuite de se rendre sur Kibana (port `5601`) et de configurer l'index en tapant `*` dans le champ indiqué, de valider et de sélectionner le champ `@timestamp`, puis de valider. L'index nécessaire à Kibana est créé, vous pouvez vous rendre dans la partie Discover à gauche (l'icône boussole 🧭) pour lire vos logs.

<!-- ### _Facultatif :_ Ajouter un nœud Elasticsearch

Puis, à l'aide de la documentation Elasticsearch et/ou en adaptant de bouts de code Docker Compose trouvés sur internet, ajoutez et configurez un nœud Elastic. Toujours à l'aide de la documentation Elasticsearch, vérifiez que ce nouveau nœud communique bien avec le premier. -->

<!-- ### _Facultatif_ : ajouter une stack ELK à `microblog` -->
<!-- TODO: Fiare avec ma version de l'app et du docker compose -->
<!-- Dans la dernière version de l'app `microblog`, Elasticsearch est utilisé pour fournir une fonctionnalité de recherche puissante dans les posts de l'app.
Avec l'aide du [tutoriel de Miguel Grinberg](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-xix-deployment-on-docker-containers), écrivez le `docker-compose.yml` qui permet de lancer une stack entière pour `microblog`. Elle devra contenir un conteneur `microblog`, un conteneur `mysql`, un conteneur `elasticsearch` et un conteneur `kibana`. -->

<!-- ### _Facultatif / avancé_ : centraliser les logs de microblog sur ELK

Avec la [documentation de Filebeat](https://www.elastic.co/guide/en/beats/filebeat/current/configuration-autodiscover.html) et des [hints Filebeat](https://www.elastic.co/guide/en/beats/filebeat/current/configuration-autodiscover-hints.html) ainsi que grâce à [cette page](https://discuss.elastic.co/t/nginx-filebeat-elk-docker-swarm-help/130512/2), trouvez comment centraliser les logs Flask de l'app `microblog` grâce au système de labels Docker de Filebeat.

Tentons de centraliser les logs de
de ces services dans ELK. -->

---

```
version: "3.8"
# FIXME: aplatir réseau ou exposer ports logstash / elasticsearch pour pouvoir envoyer logs type ceux d'un nginx local (mais en même temps un nginx local c'est une histoire de filebeat)
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

```
