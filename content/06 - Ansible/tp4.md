---
title: 'TP4 Ansible - Découvrir Jenkins et lancer des jobs Ansible'
draft: false
weight: 23
---


## Installer Jenkins avec Docker

Jenkins est un programme java qui peut soit être installé à la main, soit déployé avec docker simplement ou de façon plus avancée avec des conteneur docker dans kubernetes. Nous allons ici utiliser la méthode docker simple.

- Créer un dossier `tp4_jenkins`. Ouvrez le dossier avec VSCode.

Pour lancer jenkins nous allons utiliser docker compose.

- Cherchez sur [hub.docker.com](hub.docker.com) l'image `blueocean` de `jenkins`.

- Créez un fichier `docker-compose.yml` avec à l'intérieur:

```
version: "2"
services:
  jenkins:
    image: <image_blueocean>
    user: root
    ports:
      - "<port_jenkins_mapping>"
    volumes:
      - jenkins_data:/var/jenkins
      - /var/run/docker.sock:/var/run/docker.sock
volumes:
  jenkins_data:
```


- Pour le mapping de port choissez `8080` pour le port hote. Le port de jenkins est `8080`.
- Créez dans `tp4_jenkins` le dossier `jenkins_data`.
- La section volumes permet de monter des volumes docker :
  - les données de jenkins se retrouverons dans le dossier `jenkins_data` et survivrons à la destruction du conteneur. A l'intérieur du conteneur le dossier data est ``
  <!-- - Le dossier `./home` peut également être monté à l'emplacement `/home` pour persister les données de build. -->
  - pour que jenkins puisse utiliser Docker il doit pouvoir accéder au socket docker de l'hôte qui permet de controller la runtime docker. Il faut pour cela monter `/var/run/docker.sock` au même emplacement (/var/run/docker.sock) côté conteneur.
  
- Après avoir complété le fichier et ajouté les 3 volumes, lancez jenkins avec `docker-compose up -d`.

- Pour vérifier que le conteneur est correctement démarré utilisez la commande `docker-compose logs`

- Quand le logiciel est prêt la commande précédente affiche des triple lignes d'étoiles `*`. Entre les deux est affiché un token du type: `05992d0046434653bd253e85643bae12`. Copiez ce token.

- Visitez l'adresse http://localhost:8080. Vous devriez voir une page jenkins s'afficher. Activez le compte administrateur avec le token précédemment récupéré.

- Cliquez sur `Installer les plugins recommandés`

- Créez un utilisateur à votre convenance. Jenkins est prêt.


## Créer un premier pipeline

- Cliquez sur créer un nouvel item, sélectionnez le type `pipeline`.
- Dans le vaste formulaire qui s'ouvre remplissez la section nom avec `hello`
- Laissez tout le reste tel quel sauf la section `script` en bas ou il faut coller la description du pipeline:
  
```
pipeline {
    agent any
    stages {
        stage("Hello") {
            steps {
                echo 'Hello World'
            }
        }
    }
}
```

- Sauvegardez le pipeline. Retournez sur la page d'accueil de Jenkins et lancez votre tache.
- Cliquez le sur le job qui se lance `#1` ou `#2` pour suivre son déroulement puis cliquez sur `Console Output` dans le menu de gauche.
- Vous devriez voir quelque chose comme:

```
Started by user elie
Running in Durability level: MAX_SURVIVABILITY
[Pipeline] Start of Pipeline
[Pipeline] node
Running on docker-slave-41a6ab3a5327 in /home/jenkins/workspace/hello
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Hello)
[Pipeline] echo
Hello World
[Pipeline] }
[Pipeline] // stage
[Pipeline] }
[Pipeline] // node
[Pipeline] End of Pipeline
Finished: SUCCESS
```

- L'interface un peu vieillissante que vous venez de visiter est celle de jenkins traditionnelle. Nous allons maintenant voir BlueOcean qui est plus simple et élégante mais plus limitée.
- Cliquez sur `Open Blue Ocean`.
- Affichez simplement les logs de notre pipeline précédent.
- Pour accéder directement à la page d'accueil visitez `http://localhost:8080/blue`.
- Cliquez sur le job hello est relancez le. Un nouveau pipeline démarre qui s'exécute en une seconde.

Passons maintenant à un vrai pipeline de test. Pour cela nous devons d'abord avoir une application à tester et un jeu de tests à appliquer. Nous allons comme dans les TPs précédent utiliser une application python flask.

## Exécuter Ansible dans Jenkins à l'aide d'un agent Ansible.

- Jenkins fonctionne avec des serveurs agents qui doivent être connectés au master (via SSH ou le protocole JNLP de Jenkins) pour exécuter des tâches. Nous utiliserons SSH.

- Le serveur agent doit avoir installé en local tous les outils nécessaires pour exécuter la tâche/pipeline requise. Par exemple il faut Python installé sur l'agent pour exécuter des tests en langage python ou dans notre cas Ansible pour exécuter des playbooks avec Jenkins.

- Traditionnellement les agents Jenkins sont des serveurs complets et fixes qu'on créé indépendamment de Jenkins puis qu'on connecte au master Jenkins. C'est la méthode que nous utiliserons ici.

Cependant, si Jenkins a été créé bien avant Docker et Kubernetes, il s'intègre bien depuis des années avec les environnement conteneurisés. Ainsi, **plutôt que d'installer à la main un serveur linux** pour être notre agent on peut demander à Jenkins (grâce à ses plugin docker ou kubernetes) de **lancer automatiquement des conteneurs agents** pour exécuter notre tâche/pipeline et les détruire à la fin du job. Pour cette méthode, voir le TP Jenkins dans Kubernetes.


### Créer le serveur agent avec Vagrant

Nous allons donc créer un serveur agent manuellement pour Jenkins à l'aide de Vagrant. Nous dirons ensuite à Jenkins de s'y connecter en SSH avec l'utilisateur Vagrant pour exécuter son job à l'intérieur.

- Ajoutez au projet le `Vagrantfile` suivant:

```ruby
Vagrant.configure("2") do |config|
  config.ssh.insert_key = false
  config.vm.synced_folder ".", "/vagrant", disabled: true
  config.vm.provider :virtualbox do |v|
    v.memory = 512
    v.cpus = 1
  end

  config.vm.define :jenkinsagent do |jenkinsagent|
    # Vagrant va récupérer une machine de base ubuntu 20.04 (focal) depuis cette plateforme https://app.vagrantup.com/boxes/search
    jenkinsagent.vm.box = "ubuntu/focal64"
    jenkinsagent.vm.hostname = "jenkinsagent"
    jenkinsagent.vm.network :private_network, ip: "10.10.10.9"
    jenkinsagent.vm.provision :shell, privileged: true, inline: <<-SHELL
      <étapes d'installation de nos outils>
    SHELL
  end
end
```

Il nous faut en plus nous assurer un minimum de configuration de ce serveur pour que Jenkins puisse fonctionner et que Ansible soit disponible.

- Complétez les étapes d'installation dans le `Vagrantfile` pour:
  - `apt-get update`
  - s'assurer que le dossier `/home/vagrant/jenkins_agent` existe avec `mkdir -p`. Jenkins a besoin d'un dossier de travail accessible par l'utilisateur de connexion (ici `vagrant`)
  - installer `openjdk-13-jdk` nécessaire au fonctionnement de l'agent Jenkins. (`apt-get install -y` l'option -y permet l'installation automatique)
  - installer `python3` et `python3-pip` avec `apt` puis `ansible` à l'aide de `pip3`.


<!-- ```sh
      apt-get update
      apt-get install -y python3 python3-pip openjdk-13-jdk
      pip3 install ansible
      mkdir -p /home/vagrant/jenkins_agent
``` -->

- Créer le serveur avec `vagrant up`. Pour exécuter plusieurs fois les étapes d'installation on pourra utiliser `vagrant provision`.

### Connecter l'agent au master

- Allons voir la configuration des agents Jenkins : `Administrer Jenkins > Gérer les noeuds`. Remarques:
  - Le master Jenkins est lui même un noeud d'exécution. C'est sur lui que s'est exécuté notre Job hello world.
  - On veut ici ajouter un noeud permanent qui sera toujours disponible mais consommera toujours des resources. Avec les docker ou kubernetes les noeuds sont temporaires et créé dans un "cloud" Jenkins.

Nous avons besoin de nous connecter en SSH au serveur agent. Pour cela il faut créer dans Jenkins un `credential` (identifiant) qui lui permettra de se connecter. Les `credentials` peuvent être de pleins de type différents:

- user/password pour du ssh ou une API
- user/clé ssh pour du ssh
- identité kubernetes pour connexion à un cluster
- identité AWS pour connexion à un compte de cloud
- etc.

Nous allons crée un `credential` de type user / clé ssh avec `vagrant` et sa clé privée unsecure (c'est une configuration de test, car cette clé et publiquement disponible. En production, il faudrait ajouter un utilisateur et une nouvelle clé ssh "originale" au serveur agent)

- Allez voir la configuration des credentials Jenkins : `Administrer Jenkins > Manage Credentials > Jenkins > Identifiants globaux > Ajouter des identifiants`.

- Complétez le formulaire comme suit (dans `Private key > enter directly` collez le texte de la clé privée présent dans `~/.vagrant.d/unsecure_private_key`):

![](../../images/jenkins/jenkins-sshkey-credential-creation.png)


Maintenant nous pouvons ajouter l'agent ssh.

- Retournez dans la configuration des agents Jenkins : `Administrer Jenkins > Gérer les noeuds`.

- Créez un nouvel agent comme suit : 

![](../../images/jenkins/jenkins-ssh-agent-configuration.png)

- Sauvegardez et vérifiez grace aux logs de Jenkins si tout s'est bien passé ou quelle partie corriger.

Nous pouvons maintenant exécuter du code Ansible avec Jenkins


### Créer un pileline de vérification Ansible

- Dans tableau de bord, créez un job `ansible test` de type `Pipeline` et ajoutez le code suivant comme description du pipeline:

```groovy
pipeline {
    agent { label "ansible-agent" }
    stages {
        stage('Test Ansible installation') {
            steps {
               sh "ansible --version"
            }
        }
    }
}
```

Grace à l'indication du `label` qui est le même que dans la configuration de notre agent, Jenkins saura ou il doit exécuter ce job. En effet on a vite de nombreux agents avec des configurations et des ressources différents qui faut pouvoir désigner.

- Lancez le job (`Lancer un build`) et allez voir dans les logs si la version de Ansible est bien affichée.

### Créer un pipeline d'exécution Ansible plus réaliste grâce au plugin Ansible de Jenkins

- Allez dans `Gestion des plugins > Disponibles` puis cherchez et installez les plugins `ansible` et `ansicolor`.

- Créez un nouveau pipeline `ansible-ping` et utilisez le code suivant pour le configurer:

```groovy
pipeline {
    agent { label "ansible-agent" }
    stages {
        stage('Test') {
            steps {
                git url: "https://github.com/e-lie/ansible_basic.git", branch: "ansible_tp4_jenkins"
                // dont forget to install ansicolor plugin and activate it at in jenkins system parameters
                ansiblePlaybook playbook: 'ping.yml', credentialsId: 'vagrant-global-insecure-sshkey', colorized: true
            }
        }
    }
}
```

Ce pipeline récupère un petit projet de code sur github avec un inventaire contenant les app1 et app2 du TP2 avec leurs ips 10.10.10.11-12 et un playbook `ping.yml` qui ping les deux machines.

- Lancez le pipeline et observez les logs.

### Bonus

Essayez de créer de nouveaux pipelines pour cloner et lancer le code du TP2 ou du TP3.