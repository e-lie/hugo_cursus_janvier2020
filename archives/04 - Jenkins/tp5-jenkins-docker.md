---
title: 'TP5 - Pipeline de test avec Jenkins et Docker'
draft: true
---

## Créer une machine pour le Jenkins Master

- Importez avec virtualbox trois noeud ubuntu `elk_node_py2` (à récupérer par exemple dans le partage réseau)

- Utilisez le nom `Jenkins Master`

- Configurer les machines avec `2048 Mo` de RAM

- Créez un nouveau réseau NAT JenkinsNat et mettez votre machine ubuntu ainsi le le noeud jenkins à l'intérieur.

- Démarrez les machines

- Récupérez l'ip des noeud jenkins

- Connectez vous en ssh à l'aide du login: `elk-admin`, mdp: `el4stic`

## Installer Docker et Jenkins avec Ansible

### Setup Ansible

- Créez un nouveau dossier de projet `JenkinsAsCode` avec à l'intérieur un dossier `ansible_provision`
- Initialisez le dépôt git
- Ouvrez le dossier avec VSCode
- Dans `ansible_provision` ajoutez un fichier `ansible.cfg` avec à l'intérieur:
```cfg
[defaults]
inventory = hosts.cfg
host_key_checking = False
```
- Créez le fichier d'inventaire correspondant avec à l'intérieur:

```cfg
[jenkins_nodes]
jenkins_master ansible_host=<ip>
runner1 ansible_host=<ip2>

[jenkins_nodes:vars]
ansible_user=<login>
ansible_password=<password>
ansible_become_password=<password>
```

- Créez un playbook `bootstrap_docker_compose.yml`:

```yaml
---
- hosts: <nom_groupe_jenkins>
  become: yes
  become_method: sudo
  tasks:
    - ping:
```

- Lancez le playbook pour tester le setup d'ansible (`ansible-playbook <monplaybook>`)

### Installer Docker et Java

- Ajoutez les tâches suivantes à compléter au playbook (complétez les valeurs en vous aidant des instructions d'installation de docker : https://docs.docker.com/install/linux/docker-ce/ubuntu/)

```yaml
   - name: install basic requirements
      apt: 
        name: "{{ pkgs }}"
        update_cache: yes
      vars:
        pkgs:
          <liste des packages preinstall>
          
    - name: Add docker developers key
      apt_key:
        url: <url key docker>
        state: present

    - name: Add docker apt repository
      apt_repository:
        repo: <dépot_docker_ubuntu_bionic>
        state: present
      
    - name: install Docker and Java
      apt: 
        name: "{{ pkgs }}"
        update_cache: yes
      vars:
        pkgs:
          <docker_packages>
          - docker-compose
```

- Lancez à nouveau le playbook pour installer les requirements de base.

## Installer Jenkins avec Docker (version simple)

- Cherchez sur [hub.docker.com](hub.docker.com) l'identifiant de l'image officielle jenkins latest (demander confirmation au formateur)

- Créez un dossier `jenkins_home` dans le dossier personnel de elk-admin.

- Créez un conteneur en mode daemon grâce à la commande :
- 
```
docker run -d -p <port_hote>:<port_jenkins> -v "<path_host>:<path_container>" --name jenkins <image_id>
```
	- le paramètre `-d` indique de démarrer le conteneur en mode daemon.
	- `-p` sert pour le mapping de port choissez `80` pour le port hote (le port http par défaut voir cours sur le réseau). Le port de jenkins est `8080`.
	- `-v` permet de monter un volume docker : les données de jenkins se retrouverons dans le dossier `~/jenkins_home` et survivrons à la destruction du conteneur.
	- notre conteneur aura le nom `jenkins` dans docker (plus facile lorsqu'on a pas de risque de collision de nom).

- Lancez la commande. Il a un problème de permission sur le volume. Ajoutez une tâche Ansible (module `user`, avec le param `group` et `append: yes`) (voir les exemple très parlant de la doc ansible)
  
- Relancez le playbook.

- Pour vérifier que le conteneur est correctement démarré utilisez la commande `docker logs <nom_conteneur>`

- Qu'est ce qu'il manque ? les volumes docker prennent par défaut les permissions du dossier ou ils sont montés. Par contre si le dossier n'est pas présent il sera créé en root par le processus du daemon et donc non accessible.

- Ajoutez une tâche Ansible pour précréer le dossier `jenkins_home` (ansible module `file` avec le `state: directory`). relancez le playbook.

- Quand le logiciel est prêt la commande précédente affiche des triple lignes d'étoiles `*`. Entre les deux est affiché un token du type: `05992d0046434653bd253e85643bae12`. Copiez ce token.

- Coté ubuntu (pas en ssh) éditez le fichier `/etc/hosts` pour ajouter un nom de domaine local à votre machine Jenkins : `jenkins.server`.

- Sur ubuntu visitez l'adresse http://jenkins.server. Vous devriez voir une page jenkins s'afficher. Activez le compte administrateur avec le token précédemment récupéré

- Cliquez sur `Installer les plugins recommandés`

- Créez un utilisateur à votre convenance, mettez l'url précédente pour Jenkins.

## Créer un premier pipeline

- Cliquez sur créer un nouvel item, sélectionnez le type `pipeline`
- donnez lui le nom `hello` puis collez dans la section `script`
  
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

- Sauvegardez. Retournez sur la page d'accueil de Jenkins et lancez votre tache.
- Cliquez le sur le job qui se lance pour voir les logs de la tâche.

## Configurer un master Jenkins à l'aide de Docker (Infrastructure as Code)

La configuration manuelle de Jenkins est assez longue et fastidieuse. Une bonne façon de l'automatiser est d'étendre l'image Jenkins officielle à l'aide d'un `Dockerfile` personnalisé.

De plus, nous allons configurer un noeud esclave pour exécuter les jobs Jenkins. L'architecture master/slave de jenkins permet d'éviter que le serveur soit surchagé lorsque trop de builds sont lancés par une équipe de développeurs. Elle permet également de scaler Jenkins plus facilement. Jenkins peut également fonctionner avec de multiple noeud master (bien que nous ne développerons pas cette configuration ici).

- En ssh sur le noeud master, détruisez le conteneur Jenkins précédent.

Dans cette section nous allons produire l'arborescence suivante:
```bash
JenkinsAsCode
├── ansible_provision
│   ├── ansible.cfg
│   ├── bootstrap_docker_compose.yml
│   ├── docker-compose.yml
│   ├── hosts.cfg
│   └── launch_jenkins.yml
├── jenkins-master
│   ├── default-user.groovy
│   ├── Dockerfile
│   └── executors.groovy
└── jenkins-slave
    ├── Dockerfile
    └── slave.py
```

### Dockerfile pour le master

- Créez un nouveau dossier `jenkins-master`  dans `JenkinsAsCode` avec à l'intérieur un nouveau `Dockerfile`
  
- Comment faire pour démarrer à partir de l'image jenkins utilisé précédemment ? Ajoutez cette ligne au fichier

- Jenkins utilise des outils CLI spécifiques pour être manipulé de façon automatique. Pour installer des plugins, il s'agit du script `/usr/local/bin/install-plugins.sh`.
  
- Ajoutez à la suite une instruction docker qui lance ce script pour installer : `git matrix-auth workflow-aggregator docker-workflow blueocean credentials-binding`

Comme souvent pour les application lancées avec Docker, Jenkins peut être configuré grâce à des variables d'environnement, soit à la volée soit au moment de la construction de l'image.

- Ajoutez à la suite du `Dockerfile`, grâce à la commande docker `ENV` deux variables d'environnements `JENKINS_USER` et `JENKINS_PASS` pour configurer l'utilisateur par défaut. 

Vous pouvez bien sur choisir le login et le mot de passe de votre choix mais je vous propose de conserver `admin:admin` pour ne pas oublier les surcharger à runtime une fois en production. En effet, stocker des secrets dans les images est une mauvaise idée car les images et Dockerfiles sont régulièrement publiée sur des dépôts collectifs. Passer les secrets en dans l'environnement est déjà plus acceptable et assez répendu.


- Nous allons également désactiver le wizard d'installation de Jenkins en passant dans l'environnement un paramètre java : `ENV JAVA_OPTS -Djenkins.install.runSetupWizard=false`

- Pour configurer l'utilisateur par défaut nous allons ajouter deux scripts de configuration Jenkins en groovy:
  - Créez un fichier `executors.groovy` avec à l'intérieur:
```groovy
import jenkins.model.*
Jenkins.instance.setNumExecutors(0)
```
Ce code permet de configurer le noeud master Jenkins pour qu'il n'est aucun processus d'exécution de pipeline.

- Créez également un fichier `default-user.groovy`

```groovy
import jenkins.model.*
import hudson.security.*

def env = System.getenv()

def jenkins = Jenkins.getInstance()
jenkins.setSecurityRealm(new HudsonPrivateSecurityRealm(false))
jenkins.setAuthorizationStrategy(new GlobalMatrixAuthorizationStrategy())

def user = jenkins.getSecurityRealm().createAccount(env.JENKINS_USER, env.JENKINS_PASS)
user.save()

jenkins.getAuthorizationStrategy().add(Jenkins.ADMINISTER, env.JENKINS_USER)
jenkins.save()
```

Ce fichier utilise les variables d'environnement précédemment définies pour configurer un utilisateur par défaut.

- Copions ces fichiers groovy dans le répertoire d'initialisation de Jenkins:
```Dockerfile
# Start-up scripts to set number of executors and creating the admin user
COPY executors.groovy /usr/share/jenkins/ref/init.groovy.d/
COPY default-user.groovy /usr/share/jenkins/ref/init.groovy.d/
```

- Lancez un build du conteneur avec l'étiquette jenkins-master:0.1 pour tester sa configuration
- Taggez le avec le tag: `<votre_user_docker_hub>/jenkins-master:0.1`
- Poussez le sur le docker hub comme dans le TP précédent avec `docker login` et `docker push`

## Construire un noeud worker

- Créez un nouveau dossier `jenkins-slave` dans `JenkinsAsCode`.
  
- Ajoutez un `Dockerfile` à l'intérieur:

Nous allons construire un conteneur "slave" pour notre Jenkins master. Le slave n'a besoin d'avoir Jenkins installé dessus. Nous allons utiliser un script lancé au démarrage de du conteneur pour qu'il se rattache au master lorsque celui-ci est près.

- Pour notre conteneur, partons d'une image `ubuntu:16.04`

Avec Jenkins les tâches du pipeline sont exécutées à l'intérieur du noeud slave. Comme notre application à tester est basé sur docker nous devons pouvoir lancer du Docker Docker du Docker ! Ce n'est pas du tout un problème. Docker supporte l'inception.

- Ajoutez une série d'instructions RUN pour installer docker dans notre image ubuntu.:
```Dockerfile
RUN apt-get update && apt-get install -y apt-transport-https ca-certificates
RUN apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
RUN echo "deb [arch=amd64] https://download.docker.com/linux/ubuntu xenial stable" > /etc/apt/sources.list.d/docker.list
RUN apt-get update && apt-get install -y docker-ce --allow-unauthenticated
```

- Installons également les dépendances de notre runtime Jenkins slave et de notre script de rattachement (voir la suite):
```Dockerfile
RUN apt-get update && apt-get install -y openjdk-8-jre curl python python-pip git
RUN easy_install jenkins-webapi
```

- Enfin, installons docker-compose car notre application se lance grâce à cet outil:
```Dockerfile
RUN curl -L https://github.com/docker/compose/releases/download/1.21.2/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose && chmod +x /usr/local/bin/docker-compose
```

- Ajoutons les dossiers de travail de notre slave:
```
RUN mkdir -p /home/jenkins
RUN mkdir -p /var/lib/jenkins
```

- Téléchargez le script de rattachement à cette adresse: 
  `wget https://raw.githubusercontent.com/e-lie/docker-series/docker-series-continuous-integration-jenkins-end/jenkins-docker/slave/slave.py`

- Puis ajoutez à l'image à l'emplacement `/var/lib/jenkins/slave.py`

- Changeons le répertoire de travail (WORKDIR) pour le positionner dans `/home/jenkins`

- Enfin ajoutons des variables d'environnement pour configurer notre script de rattachement (potentiellement à la volée en le surchargeant):
```
ENV JENKINS_URL "http://jenkins"
ENV JENKINS_SLAVE_ADDRESS ""
ENV JENKINS_USER "admin"
ENV JENKINS_PASS "admin"
ENV SLAVE_NAME ""
ENV SLAVE_SECRET ""
ENV SLAVE_EXECUTORS "1"
ENV SLAVE_LABELS "docker"
ENV SLAVE_WORING_DIR ""
ENV CLEAN_WORKING_DIR "true"
```

- Nous voulons lancer le script `slave.py` pour dire au worker d'initier la connexion avec le master. Ajoutez à la fin du fichier:

```Dockerfile
CMD [ "python", "-u", "/var/lib/jenkins/slave.py" ]
```

- Lancez un build du conteneur avec l'étiquette jenkins-master:0.1 pour tester sa configuration
- Taggez le avec le tag: `<votre_user_docker_hub>/jenkins-master:0.1`
- Poussez le sur le docker hub comme dans le TP précédent avec `docker login` et `docker push`

## Déployer nos conteneurs

Maintenant que nous avons deux images disponibles pour construire une petite infra Jenkins, créons un fichier `docker-compose.yml` pour coordonner les deux conteneurs.

- Comme nous allons provisionner le serveur à l'aide d'Ansible, mettez le dans le dossier `ansible_provision`.

```yaml
version: '3.1'
services:
    jenkins-master:
        container_name: jenkins-master
        ports:
            ...
        image: <image_master>
    jenkins-slave:
        container_name: jenkins-slave
        restart: always
        environment:
            - <surcharge_jenkins_url> # utiliser l'ip de jenkins 
        image: <image_slave>
        volumes:
            - /var/run/docker.sock:/var/run/docker.sock  # Expose the docker daemon in the container pour l'inception.
        depends_on:
            - jenkins-master
```

- Pour l'image master et slave nous allons récupérer celles que nous venons de pousser sur le docker hub.

- Pour connecter le slave au master nous allons surcharger la variable d'environnement idoine définie dans le Dockerfile précédent, pour indiquer l'adresse locale. 
  
- Pour les ports de notre container master, nous avons vu précédement que le port par défaut de Jenkins est 8080. Nous allons le mapper sur 8080.

- Pour se connecter au master le slave va utiliser le protocole JNLP (Java Network Launch Protocol) proposé par Jenkins. Il communique sur le port 50000 par défaut. Il fau donc le mapper également.


## Test en local et déploiement avec Ansible

- A ce stade vérifiez que vous avez bien l'arborescence suivante:

```bash
JenkinsAsCode
├── ansible_provision
│   ├── ansible.cfg
│   ├── bootstrap_docker_compose.yml
│   ├── docker-compose.yml
│   ├── hosts.cfg
│   └── launch_jenkins.yml
├── jenkins-master
│   ├── default-user.groovy
│   ├── Dockerfile
│   └── executors.groovy
└── jenkins-slave
    ├── Dockerfile
    └── slave.py
```

- Testez en local en le lançant avec docker-compose.

- Pour le déployer sur notre serveur nous allons utiliser Ansible. Ajoutez le playbook `launch_jenkins.yml` suivant dans `Ansible_provisionning:

```yaml
---
- hosts: jenkins
  # no become, we want to run jenkins as elk-admin
  tasks:
    - name: copy compose file to hosts
      copy:
        src: <src>
        dest: <dest>

    - name: launch compose
      shell: <commande shell docker-compose>
```

Complétez le, sachant qu'il sera lancé par l'utilisateur elk-admin (utilisez son home ?)

- Modifiez également le docker-compose.yml pour mapper le port hôte 80 sur 8080 dans le conteneur. 

- Lancez le playbook et testez votre configuration en visitant `http://jenkins.server`

- Normalement votre Jenkins est déjà configuré correctement avec ses plugin et son utilisateur.

- Il devrait également avoir un worker docker connecté.

- Pour debuggez éventuellement connectez vous en SSH au serveur et consultez les logs de nos conteneur (`docker logs ...`)
  
Avec cette configuration automatisée il est aisé de configurer de nouveaux masters et slaves à la volée avec quelques adaptations.

## Pipeline as code

- Reprenons le code de notre application **identidock** et les fichiers de dockerisation.

- Ajoutons le fichier de `tests.py` suivant dans `app/`:
  
```python
import unittest
import identidock

class TestCase(unittest.TestCase):

  def setUp(self):
    identidock.app.config["TESTING"] = True
    self.app = identidock.app.test_client()

  def test_get_mainpage(self):
    page = self.app.post("/", data=dict(name="Moby Dock"))
    assert page.status_code == 200
    assert 'Hello' in str(page.data)
    assert 'Moby Dock' in str(page.data)

  def test_html_escaping(self):
    page = self.app.post("/", data=dict(name='"><b>TEST</b><!--'))
    assert '<b>' not in str(page.data)

if __name__ == '__main__':
  unittest.main()
```

- Lancer les tests avec `python tests.py` pour vérifier leur exactitude.

- ajouter un `Jenkinsfile` (le Jenkinsfile est le )
```groovy
node('docker') {
    stage('Checkout'){
        checkout scm
    }
}
```

- Poussez le code sur gitlab.

- Allez dans l'interface BlueOcean (bouton dans le menu de gauche) et créez un pipeline de type git

- Ajoutez l'url ssh de votre dépôt (à récupérer avec le bouton clone bleu sur la page de gitlab)

- Ajoutez la clé proposée par gitlab

- Terminez et lancez le pipeline. Il n'est pas très utile.

- Introspectons notre conteneur "esclave"
  - Tout le contexte est présent
  
- Pour lancer les tests ajoutez le stage suivant:

```groovy
stage('Build & UnitTest'){
    sh "docker build -t identidock:0.1 -f Dockerfile ."
    sh "docker run identidock:0.1 python tests.py"
}
```
- Ajoutons un versionement des images avec les numéros de build : `sh "docker build -t identicustom:B${BUILD_NUMBER} -f Dockerfile .". Jenkins expose par défaut la variable BUILD_NUMBER dans l'environnement. 

- Ajoutons un stage de cleaning avec `docker container prune` puis `docker image prune`.

- Ajoutez un stage de smoke test sommaire : construire et lancer l'application avec un compose-file de production puis utiliser curl pour voir si elle répond.

## Bonus (à faire après le TP 6 cluster) déployer notre application

- Utiliser `docker-machine` pour déployer notre application en production sur DigitalOcean.