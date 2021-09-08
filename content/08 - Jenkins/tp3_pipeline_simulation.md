---
title: TP3 - Simuler le pipeline hors de Jenkins
draft: true
---



## Un pipeline classique

<!-- - Commitez vos modifications et récupérer la base du tp3 avec `git checkout jenkins_tp3_correction` -->


![](../../images/jenkins/00047.jpeg)


### Une question de sécurité : ajouter un docker build agent séparé dans une VM

Nous aurons besoin de `docker build, docker tag, docker push` tout au long de ce pipeline. Cependant il n'est pas possible de sécuriser l'usage de docker à l'intérieur du cluster Kubernetes car celui ci doit forcément avoir des droits proche de root pour fonctionner quelle que soit la méthode.

En utilisant docker dans un conteneur ou directement dans le cluster nous aurions donc un vecteur d'attaque privilégié pour prendre le contrôle du cluster à partir du déploiement d'images particulières.

Les solutions sont :

- soit d'effectuer le build avec d'autres solutions comme Kaniko (développé par google a cet effet)
- soit d'ajouter un noeud docker extérieur au cluster qui est  donc isolé du cluster en terme de sécurité.

Kaniko manque encore un peu de maturité et fonctionne légèrement différemment de docker. Nous utiliserons donc un noeud docker extérieur pour la suite.

### Créer le serveur docker agent avec Vagrant

Nous allons donc créer un serveur agent docker manuellement pour Jenkins à l'aide de Vagrant. Nous dirons ensuite à Jenkins de s'y connecter en SSH avec l'utilisateur Vagrant pour exécuter son job à l'intérieur. Comme nous avons fait pour le noeud ansible dans le TP4 ansible.

- Ajoutez dans un dossier `vagrant_docker_agent` dans le dossier `tp3_infra_et_app/tp3_infra` et à l'intérieur le fichier `Vagrantfile` suivant:

```ruby
Vagrant.configure('2') do |config|
  config.ssh.insert_key = false
  config.vm.provider :virtualbox do |v|
    v.memory = 1024
    v.cpus = 1
  end

  config.vm.define :master do |master|
    master.vm.box = 'ubuntu/focal64'
    master.vm.hostname = 'master'
    master.vm.network :private_network, ip: '10.12.0.11'
    master.vm.provision :shell, privileged: false, inline: <<-SHELL
      sudo rm /etc/resolv.conf && echo "nameserver 1.1.1.1" | sudo tee /etc/resolv.conf && sudo chattr +i /etc/resolv.
      sudo apt update && sudo apt-get install -y apt-transport-https ca-certificates curl gnupg lsb-release
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
      echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
          $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
      sudo apt-get update
      sudo apt-get install -y docker-ce docker-ce-cli containerd.io
      sudo apt-get install -y openjdk-13-jdk
      sudo -u vagrant mkdir -p /home/vagrant/jenkins_agent
    SHELL
  end
end
```

Les étapes du provisionning servent à nous assurer un minimum de configuration de ce serveur pour que Jenkins puisse fonctionner et que Docker soit disponible.

- Créer le serveur avec `vagrant up`. Pour exécuter plusieurs fois les étapes d'installation on pourra utiliser `vagrant provision`.

### Connecter l'agent au master dans Jenkins

- Allons voir la configuration des agents Jenkins : `Administrer Jenkins > Gérer les noeuds`. 

Nous avons besoin de nous connecter en SSH au serveur agent. Pour cela il faut créer dans Jenkins un `credential` (identifiant) qui lui permettra de se connecter. 

Nous allons crée un `credential` de type user / clé ssh avec `vagrant` et sa clé privée unsecure (c'est une configuration de test, car cette clé et publiquement disponible. En production, il faudrait ajouter un utilisateur et une nouvelle clé ssh "originale" au serveur agent)

- Allez voir la configuration des credentials Jenkins : `Administrer Jenkins > Manage Credentials > Jenkins > Identifiants globaux > Ajouter des identifiants`.

- Complétez le formulaire comme suit (dans `Private key > enter directly` collez le texte de la clé privée présent dans `~/.vagrant.d/insecure_private_key`):

![](../../images/jenkins/jenkins-sshkey-credential-creation.png)


Maintenant nous pouvons ajouter l'agent ssh.

- Retournez dans la configuration des agents Jenkins : `Administrer Jenkins > Gérer les noeuds`.

- Créez un nouvel agent comme suit en changeant `ansible-agent` par `docker-agent` et l'IP `10.10.10.9` par `10.12.0.11`: 

![](../../images/jenkins/jenkins-ssh-agent-configuration.png)

- Sauvegardez et vérifiez grace aux logs de Jenkins si tout s'est bien passé ou quelle partie corriger.

#### Pour debugger si la connexion de l'agent échoue

Il s'agit généralement soit d'un problème de connexion ssh:

  - revérifier que la clé/ip est valide etc
  - changer de stratégie pour la gestion des known_hosts dans la configuration de l'agent et réessayer

... soit d'un problème d'initialisation du programme agent jenkins sur le serveur agent.

  - revérifier que java est bien installé
  - vérifier l'existence du dossier de travail de jenkins (`/home/vagrant/jenkins_agent` pour nous)
  - vérifier les permissions sur le dossier de travail qui doit être accessible pour l'utilisateur de connection ssh, `vagrant` dans notre cas. Le dossier a été créé en root on obtient un erreur permission denied.


Nous pouvons maintenant builder des images docker avec Jenkins hors de notre cluster k8s.

## Tests unitaires et premier build d'une version beta


![](../../images/jenkins/00008.jpeg)

Nous allons maintenant exécuter les différentes étapes du pipeline mais :
    - à la main pour valider que chaque étape fonctionne
    - au même endroit qu'elles le seront lors du pipeline (dans les conteneurs d'un pod kubernetes adapté ou sur le noeud docker selon les étapes).

Comme vu dans le TP2, les tests unitaires nécessitent seulement python et quelques dépendances python pour fonctionner nous pouvons donc les utiliser sans construire d'image.

Idéalement, ces **tests unitaires** doivent **être exécutés avant le moindre build** car il est déconseiller des construire ou pousser une image avec des bugs ou failles majeur.e.s, qui pourrait se retrouver utilisée et prendrait de la place dans le dépôt d'image pour rien.

Nous avons donc besoin d'un environnement python temporaire pour exécuter ces tests. **Or** le principe des agents Jenkins dans Kubernetes sous forme de pods est qu'ils peuvent contenirs plusieurs conteneurs adapté à chaque partie du pipeline.

Pour ce pipeline nous avons besoin de trois principaux outils (mais nous pourrions en ajouter pour contrôler d'autre point de notre logiciel ou déployer différemment):

- python
- docker (dans un noeud à part voir partie précédent)
- kubectl pour déployer l'application

Notre pod d'exécution avec python et kubectl Kubernetes sera donc comme suit:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-test-pipeline-manuel
  labels:
    component: ci
spec:
  containers:
    - name: python
      image: python:3.9
      command: ["cat"]
      tty: true
    - name: kubectl
      image: vfarcic/kubectl
      command: ["cat"]
      tty: true
```

- Pour créer ce pod temporaire utilisez par exemple la fonction `+` de Lens à côté du terminal.

- Rentrez dans le conteneur python avec `kubectl exec -it pod-test-pipeline-manuel --container python -- /bin/bash`

- Il faut d'abord récupérer le code de notre application `git clone -b jenkins_application_correction https://github.com/Uptime-Formation/corrections_tp.git jenkins_application_correction`

 <!-- TODO faire un tag jenkins_application_correction avec seulement l'application mdu tp2 prête à déployer -->

- Allez dans le dossier de l'application corrigée : `cd jenkins_application_correction`

- Installez les dépendances python avec `pip install -r requirements.txt`

- Lancez les tests unitaires `pytest` comme dans le tp1.

- Allez dans la VM docker agent avec `vagrant ssh`

- Reclonez le code comme précédemment `git clone -b jenkins_application_correction https://github.com/Uptime-Formation/corrections_tp.git jenkins_application_correction && cd jenkins_application_correction`

- Lancer la construction de l'image monstericon beta avec `docker build -t registry.<votrenom>.vagrantk3s.dopl.uk/monstericon:beta .`

- Poussez l'image sur le dépôt `docker push registry.<votrenom>.vagrantk3s.dopl.uk/monstericon:beta`

## Deploy et Tests fonctionnels


![](../../images/jenkins/00009.jpeg)

Nous allons déployer une version dev de l'application dans notre cluster pour effectuer les tests d'intégration et fonctionnels dessus.

- Sortez de la VM docker et retournez dans le pod cette fois dans le conteneur `kubectl` avec la commande suivante: `kubectl exec -it pod-test-pipeline-manuel --container kubectl -- /bin/bash`

- Retournez dans le dossier de l'application avec `cd 

- Déployez l'application en mode dev avec le tag beta grace à `

## Release


![](../../images/jenkins/00010.jpeg)

## Déploiement

![](../../images/jenkins/00011.jpeg)



## Tests fonctionnels de la production

![](../../images/jenkins/00012.jpeg)



## Nettoyage !!!

![](../../images/jenkins/00013.jpeg)