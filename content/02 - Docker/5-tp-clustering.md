---
title: TP - Orchestration et clustering
---

- Cloner l'application exemple ici : [https://gitlab.com/e-lie/getstarted_docker.git](https://gitlab.com/e-lie/getstarted_docker.git)
- En suivant le [guide Docker de découverte de Swarm à partir de la partie 4](https://docs.docker.com/get-started/part4/), créez un fichier docker-compose qui package l'application exemple avec un container redis joignable via le hostname `redis` et le port 6379.
- Une fois le tutoriel terminé, scalez la stack en ajoutant des _replicas_.
- Trouvez comment promouvoir (promote) un worker en manager
- Puis déchoir (demote) le manager pour le sortir du cluster (drain) : `docker node update --availability drain <node-name>`
- Comment ne pas exposer les ports de tous nos hôtes à tout l'internet ?

<!--
## Installons Portainer

Portainer est une interface web de base pour gérer un cluster docker.

```bash
docker service create \
      --name portainer \
      --publish 9000:9000 \
      --constraint 'node.role == manager' \
      --mount type=bind,src=/var/run/docker.sock,dst=/var/run/docker.sock \
      portainer/portainer \
      -H unix:///var/run/docker.sock
```

- Listez les services
- Inspectez le service portainer avec l'option --pretty
- Ouvrez la page avec `firefox http://$(docker-machine ip <machine_manager>):9000` -->

# Installer un loadbalancer HAProxy

- [https://github.com/docker/dockercloud-haproxy/tree/master](https://github.com/docker/dockercloud-haproxy/tree/master)
