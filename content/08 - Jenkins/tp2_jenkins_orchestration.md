---
title: 'TP2 Jenkins - Configurer Jenkins avec Docker et le déployer dans Swarm'
draft: true
---

## Découvrir swarm

Nous allons suivre le getting started docker swarm partie 3, 4 et 5.

- Cloner l'application exemple ici : https://gitlab.com/e-lie/getstarted_docker.git
- Commencez à suivre les instructions à partir de la partie 3: [https://docs.docker.com/get-started/part3/](https://docs.docker.com/get-started/part3/)


## Configurer un master Jenkins à l'aide de Docker (Infrastructure as Code)

La configuration manuelle de Jenkins est assez longue et fastidieuse. Une bonne façon de l'automatiser est d'étendre l'image Jenkins officielle à l'aide d'un `Dockerfile` personnalisé.

De plus, nous allons configurer un noeud esclave pour exécuter les jobs Jenkins. L'architecture master/slave de jenkins permet d'éviter que le serveur soit surchagé lorsque trop de builds sont lancés par une équipe de développeurs. Elle permet également de scaler Jenkins plus facilement. Jenkins peut également fonctionner avec de multiple noeud master (nous ne développerons pas cette configuration ici).

Enfin nous allons créer un `docker-compose.yml` pour lancer notre ensemble de master et slaves.

- Commencez par détruire le conteneur docker jenkins du tp1 s'il est toujours allumé.

- Dans cette section nous allons produire l'arborescence suivante:

```bash
JenkinsAsCode
    ├── docker-compose.swarm.yml
    ├── docker-compose.yml
    ├── jenkins-master
    │   ├── default-user.groovy
    │   ├── Dockerfile
    │   └── executors.groovy
    └── jenkins-slave
        ├── Dockerfile
        └── slave.py
```

- Clonez le dépôt modèle à l'adresse : [https://framagit.org/e-lie/jenkins_as_code_tp](https://framagit.org/e-lie/jenkins_as_code_tp)
- Ouvrez le projet avec VSCode.

### Dockerfile pour le master

- Dans le dossier `jenkins-master` éditez le `Dockerfile` pour compléter les éléments manquants.
  - Pour l'image de base nous allons utiliser la version lts de jenkins : `jenkins/jenkins:lts`
  - Ensuite la commande `RUN` de la ligne 3 utilise le script bash d'installation fournit par jenkins pour installer automatiquement des module à la construction de l'image. Complétez avec les plugins suivants:

```
    git \
    mstest \
    matrix-auth \
    workflow-aggregator \
    docker-workflow \
    blueocean \
    credentials-binding
```

  - Enfin nous allons utilisez deux scripts groovy (présent dans le dossier `jenkins-master`) pour configurer Jenkins : l'un sert à créer un utilisateur Jenkins, l'autre à configurer le nombre de pipelines executables sur le master (executors).
  - Complétez les trous avec les noms de ces scripts.

- Ouvrez le script `executors.groovy`. Nous allons configurer le master pour avoir `0` executors. De cette façon tous les pipelines seront lancés sur les jenkins agent (ou slaves). Complétez le nombre d'éxecutors.

- Ouvrez le script `default-user.groovy`: il contient plusieurs commandes Jenkins pour créer et configurer l'utilisateur par défaut de jenkins.

- Lancez un build du conteneur avec l'étiquette jenkins-master:0.1 pour tester sa configuration
- Taggez le avec le tag: `docker tag jenkins-master:0.1 <votre_user_docker_hub>/jenkins-master:0.1`
- Poussez l'image sur le docker hub comme dans le TP précédent avec `docker login` et `docker push`


## Construire un noeud agent/slave

- Allez dans le dossier `jenkins-slave` et éditez le `Dockerfile`

Nous allons construire une image "slave" pour notre Jenkins master. Le slave n'a besoin d'avoir Jenkins installé dessus. 

- Pour notre conteneur, partons d'une image `ubuntu:18.04`

Avec Jenkins les tâches du pipeline sont exécutées à l'intérieur du noeud slave. Il faut donc installer les dépendances nécessaires.

- Ajoutez les paquets suivants à la commande `apt install -y`:

```
        openjdk-8-jre \
        curl \
        python \
        python-pip \
        git
```

Commentons le reste du Dockerfile...

- Lancez un build du conteneur avec l'étiquette `jenkins-master:0.1` pour tester sa configuration
- Taggez le avec le tag: `<votre_user_docker_hub>/jenkins-master:0.1`
- Poussez le sur le docker hub comme dans le TP précédent avec `docker login` et `docker push`


## Déployer nos conteneurs en local

Maintenant que nous avons deux images disponibles pour construire une petite infra Jenkins, utilisons un fichier `docker-compose.yml` pour coordonner les deux conteneurs.

- Complétez les trous.

- Lancez `docker-compose up -d --build` la commande reconstruit les deux images avant de lancer les services.

- Visitez jenkins (localhost:8080) et vérifiez rapidement que les slaves sont bien connectés.


## Créer un swarm dans le cloud

- Créez un compte digital ocean.
- Connectez vous sur l'interface.
- Créez un token d'API (section API du menu de gauche). Copiez le immédiatement dans un fichier texte.

- Créez deux machines docker dans le cloud avec docker-machine:

```bash
docker-machine create  --driver digitalocean \                                                                                     
      --digitalocean-access-token <votre_token> \
     node1

docker-machine create  --driver digitalocean \                                                                                     
      --digitalocean-access-token <votre_token> \
     node2
```

- trouvez l'ip du node 1 avec `docker-machine ip node1`.

- Comme dans l'exercice précédent connectez vous au `node 1` avec `docker-machine ssh node1`

- Initialisez un swarm avec `docker swarm init --advertise-addr=<ip_node_1>`

- Copiez la commande de joins proposée.
- Connectez vous en ssh au node2 et lancez là.

- Déconnectez vous. Depuis l'hôte initialisons l'environnement swarm avec `eval $(docker-machine env node1)`


## Créons un fichier docker-stack.yml pour notre stack Jenkins

- Copiez le `docker-compose.yml` en `docker-stack.yml`.
- Ajoutez à l'intérieur:

```yml
version: '3.1'
services:
    jenkins-master:
        container_name: jenkins-master
        ports:
            - '8080:8080'
            - '50000:50000'
        image: tecpi/jenkins-master:0.1
        deploy:
            replicas: 1
            placement:
                constraints: [node.role == manager]
        environment:
            - 'JENKINS_USER=elie'
            - 'JENKINS_PASS=trucmuch442'
    jenkins-slave:
        container_name: jenkins-slave
        restart: always
        environment:
            - 'JENKINS_USER=elie'
            - 'JENKINS_PASS=trucmuch442'
            - 'JENKINS_URL=http://jenkins-master:8080'
        image: tecpi/jenkins-slave:0.1
        depends_on:
            - jenkins-master
        deploy:
            replicas: 3
            placement:
                constraints: [node.role == worker]
```

- Commentons ce fichier.

- Déployons notre stack avec `docker stack deploy -c docker-stack.yml jenkins`
- Visitez au bout de quelques secondes l'adresse ip de l'un des noeuds swarm sur le port 8080.
- Lancez également `docker stack ps jenkins` pour observer si vos conteneurs sont biens lancés.

- Ajoutez un nouveau pipeline à partir de l'application du tp1 (comme dans le tp1).


## Installons portainer

Depuis votre environnement swarm lancez:

```
docker service create \
      --name portainer \
      --publish 9000:9000 \
      --constraint 'node.role == manager' \
      --mount type=bind,src=/var/run/docker.sock,dst=/var/run/docker.sock \
      portainer/portainer \
      -H unix:///var/run/docker.sock
```

Portainer est une interface web simple et efficace pour gérer un cluster docker.

- Listez les services (`docker service ls`)
- Ouvrez la page portainer par exemple avec `firefox http://$(docker-machine ip <machine_manager>):9000`
