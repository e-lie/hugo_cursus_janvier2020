---
title: 'TP1 - Mise en place et Ansible ad-hoc'
draft: false
weight: 21
---

## Installation de Ansible

- Installez Ansible au niveau du système avec `apt` en lançant:

```
$ sudo apt update
$ sudo apt install software-properties-common
$ sudo apt-add-repository --yes --update ppa:ansible/ansible
$ sudo apt install ansible
```
  
- Affichez la version pour vérifier que c'est bien la dernière stable.

```
ansible --version
=> 2.8.x
```



- Traditionnellement lorsqu'on veut vérifier le bon fonctionnement d'une configuration on utilise `ansible all -m ping`. Que signifie-t-elle ?

{{% expand "Réponse  :" %}}
Cette commande lance le module ansible `ping` (test de connection ansible) sur le groupe all c'est à dire toutes les machines de notre inventaire. Il s'agit d'une commande ad-hoc ansible.
{{% /expand %}}



- Lancez la commande précédente. Que ce passe-t-il ?

{{% expand "Réponse  :" %}}
```
ansible all -m ping 
```
Cette commande renvoie une erreur car `all` ne matche aucun hôte.
{{% /expand %}}


- Utilisez en plus l'option `-vvv` pour mettre en mode très verbeux. Ce mode est très efficace pour **débugger** lorsqu'une erreur inconnue se présente. Que se passe-t-il avec l'inventaire ?

{{% expand "Réponse  :" %}}

`ansible all -m ping -vvv

ansible essaye de trouver un inventaire c'est à dire une liste de machine à contacter et cherche par défaut le fichier `/etc/ansible/hosts`. Comme il ne trouve rien il crée un inventaire implicite contenant uniquement localhost.
{{% /expand %}}


- Testez l'installation avec la commande `ansible` en vous connectant à votre machine `localhost` et en utilisant le module `ping`.

{{% expand "Réponse  :" %}}
```
ansible localhost -m ping
```
La commande échoue car ssh n'est pas configuré sur l'hote mais la machine est contactée (sortie en rouge). Nous allons dans la suite créer des machines de lab avec ssh installé.
{{% /expand %}}


- Ajoutez la ligne `hotelocal ansible_host=127.0.0.1` dans l'inventaire par défaut (le chemin est indiqué dans). Et pinguer hotelocal.

{{% expand "Réponse  :" %}}

- Éditez le fichier `/etc/ansible/hosts` avec par exemple `sudo gedit /etc/ansible/hosts`

- Testez cette configuration avec `ansible hotelocal -m ping`
- => idem echec de login

{{% /expand %}}

## Créer un projet de code Ansible

Lorsqu'on développe avec Ansible il est conseillé de le gérer comme un véritable projet de code :

- versionner le projet avec Git
- Ajouter tous les paramètres nécessaires dans un dossier pour être au plus proche du code. Par exemple utiliser un inventaire `inventory.cfg` ou `hosts` et une configuration locale au projet `ansible.cfg`

Nous allons créer un tel projet de code pour la suite du tp1

- Créez un dossier projet `tp1` sur le Bureau.


{{% expand "Facultatif  :" %}}
- Initialisez le en dépôt git et configurez git:

```
cd tp1
git config --global user.name "<votre nom>"
git config --global user.email "<votre email>"
git init
```
{{% /expand %}}

- Ouvrez Visual Studio Code.
- Installez l'extension Ansible dans VSCode.
- Ouvrez le dossier du projet avec `Open Folder...`

Nous sommes maintenant prêts à créers des fichiers pour notre projet Ansible.
## Découvrir Vagrant

Vagrant est un outil pour créer des VMs (ou conteneurs) à partir de code. Son objectif est de permettre la création d'environnement de développement / DevOps reproductibles et partageables.

Pour utiliser Ansible nous avons justement besoin de machine vituelles à provisionner. Nous allons utiliser Vagrant et Virtualbox pour créer plusieurs serveurs. 

- Installez Vagrant en ajoutant le dépôt ubuntu et utilisant apt (voir https://www.vagrantup.com/downloads pour d'autres installation):

```sh
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
sudo apt-get update && sudo apt-get install vagrant
```

- Ajoutez à l'intérieur un fichier `Vagrantfile` contenant le code suivant:

```ruby
Vagrant.configure("2") do |config|
    config.vm.synced_folder '.', '/vagrant', disabled: true
    config.ssh.insert_key = false # to use the global unsecure key instead of one insecure key per VM
    config.vm.provider :virtualbox do |v|
      v.memory = 512
      v.cpus = 1
    end

    config.vm.define :ubu1 do |ubu1|
      # Vagrant va récupérer une machine de base ubuntu 20.04 (focal) depuis cette plateforme https://app.vagrantup.com/boxes/search
      ubu1.vm.box = "ubuntu/focal64"
      ubu1.vm.hostname = "ubu1"
      ubu1.vm.network :private_network, ip: "10.10.10.11"
    end

    config.vm.define :centos1 do |centos1|
      # Vagrant va récupérer une machine de base ubuntu 20.04 (focal) depuis cette plateforme https://app.vagrantup.com/boxes/search
      centos1.vm.box = "geerlingguy/centos7"
      centos1.vm.hostname = "centos1"
      centos1.vm.network :private_network, ip: "10.10.10.12"
    end
  end
```

- Utilisez la commande `vagrant up` pour démarrer la machine.

- Entrainez vous à allumer, éteindre, détruire la machine et vous y connecter en ssh en suivant ce tutoriel: https://les-enovateurs.com/vagrant-creation-machines-virtuelles/. (pensez également à utiliser `vagrant --help` ou `vagrant <commande> --help` pour découvrir les possibilités de la ligne de commande vagrant).

Remarques pratiques sur Vagrant :

- Pour voir toutes les machines en train de tourner utilisez `vagrant global-status --prune`
- Toutes les machines vagrant (on parle de boxes vagrant) ont automatiquement un utilisateur vagrant qui a une clé SSH publiquement disponible (ce n'est pas sécurisé mais utile pour le développement).
- Vagrant partage automatiquement le dossier dans lequel est le `Vagrantfile` à l'intérieur de la VM dans le dossier `/vagrant`. Les scripts et autres fichiers de votre projet sont donc directement accessibles dans la VM.


### Lancer et tester les VMs

- Pour se connecter en SSH avec Ansible nous allons donc utiliser l'utilisateur vagrant et une clé SSH (non sécure) ajoutée automatiquement à chaque box Vagrant. Cette clé est disponible dans le dossier `~/.vagrant.d/insecure_private_key`.

- Essayez de vous connecter à `ubu1` et `centos1` en ssh avec l'option `-i ~/.vagrant.d/insecure_private_key` pour vérifier que la clé ssh est bien configurée et vérifiez dans chaque machine que le sudo est configuré sans mot de passe avec `sudo -i`.

### Configuration par projet d'Ansible

Un projet Ansible implique généralement une configuration Ansible locale décrite dans un fichier `ansible.cfg`. Ainsi, la configuration est versionnée avec git en même temps que le code et l'infrastructure devient portable entre les ordinateurs des différents développeurs/DevOps.

- Ajoutez à la racine du projet un tel fichier `ansible.cfg` avec à l'intérieur:

```ini
[defaults]
inventory = ./inventory.cfg
roles_path = ./roles
host_key_checking = false # nécessaire pour les labs ou on créé et supprime des machines constamment avec des signatures SSH changées.
```

- Créez le fichier d'inventaire `inventory.cfg` comme spécifié dans `ansible.cfg` et ajoutez à l'intérieur nos machines `ubu1` et `centos1` d'après ce modèle:

```ini
ubu1 ansible_host=<ip_ubu1>
centos1 ansible_host=<ip_centos1>

[all:vars]
ansible_user=<user>
ansible_ssh_private_key_file=~/.vagrant.d/insecure_private_key
```

## Contacter nos nouvelles machines

Ansible cherche la configuration locale dans le dossier courant. Conséquence: on **lance généralement** toutes les commandes ansible depuis **la racine de notre projet**.

Dans le dossier du projet, essayez de relancer la commande ad-hoc `ping` sur:

- Chaque machine séparément

{{% expand "Réponse  :" %}}
- `ansible ubu1 -m ping`
- `ansible centos1 -m ping`
{{% /expand %}}

- Sur toutes les machines en même temps

{{% expand "Réponse  :" %}}
- `ansible all -m ping`
{{% /expand %}}

- Créez un groupe `adhoc_lab` et ajoutez les deux machines `ubu1` et  `centos1` dedans.

{{% expand "Réponse  :" %}}
```ini
[adhoc_lab]
ubu1 ansible_host=<ip>
centos1 ansible_host=<ip>

[all:vars]
ansible_user=vagrant
ansible_ssh_private_key_file=~/.vagrant.d/insecure_private_key
```
{{% /expand %}}

- Lancez `ping` sur le groupe de deux machines.

{{% expand "Réponse  :" %}}
- `ansible adhoc_lab -m ping`
{{% /expand %}}

- Nous avons jusqu'à présent utilisé une connexion ssh par clé et précisé l'utilisateur de connexion dans le fichier `ansible.cfg`. Cependant on peut aussi utiliser une connexion par mot de passe et préciser l'utilisateur et le mot de passe dans l'inventaire ou en lançant la commande.

En précisant les paramètres de connexion dans le playbook il et aussi possible d'avoir des modes de connexion (ssh, winrm, lxd, docker, etc) différents pour chaque machine.

## Installons nginx avec quelques modules et commandes ad-hoc

- Modifiez l'inventaire pour créer deux sous-groupes de `adhoc_lab`, `centos_hosts` et `ubuntu_hosts` avec deux machines dans chacun. (utilisez pour cela `[adhoc_lab:children]`)


```ini
[all:vars]
ansible_user=vagrant
ansible_ssh_private_key_file=~/.vagrant.d/insecure_private_key

[ubuntu_hosts]
ubu1 ansible_host=<ip>

[centos_hosts]
centos1 ansible_host=<ip>

[adhoc_lab:children]
ubuntu_hosts
centos_hosts
```

Dans un inventaire ansible on commence toujours par créer les plus petits sous groupes puis on les rassemble en plus grands groupes.

- Pinguer chacun des 3 groupes avec une commande ad hoc.

Nous allons maintenant installer `nginx` sur les 2 machines. Il y a plusieurs façons d'installer des logiciels grâce à Ansible: en utilisant le gestionnaire de paquets de la distribution ou un gestionnaire spécifique comme `pip` ou `npm`. Chaque méthode dispose d'un module ansible spécifique.

- Si nous voulions installer nginx avec la même commande sur des machines centos et ubuntu à la fois impossible d'utiliser `apt` car centos utilise `yum`. Pour éviter ce problème on peut utiliser le module `package` qui permet d'uniformiser l'installation (pour les cas simples).
  - Allez voir la documentation de ce module
  - utilisez `--become` pour devenir root avant d'exécuter la commande (cf élévation de privilège dans le cours2)
  - Utilisez le pour installer `nginx`

{{% expand "Réponse  :" %}}
```
ansible adhoc_lab --become -m package -a "name=nginx state=present"
```
{{% /expand %}}

- Pour résoudre le problème installez `epel-release` sur la  machine centos.

{{% expand "Réponse  :" %}}
```
ansible centos_hosts --become -m package -a "name=epel-release state=present"
```
{{% /expand %}}

- Relancez la commande d'installation de `nginx`. Que remarque-t-on ?

{{% expand "Réponse  :" %}}
```
ansible adhoc_lab -m package -a name=nginx state=present
```

la machine centos a un retour changed jaune alors que la machine ubuntu a un retour ok vert. C'est l'idempotence: ansible nous indique que nginx était déjà présent sur le serveur ubuntu.
{{% /expand %}}

- Utiliser le module `systemd` et l'option `--check` pour vérifier si le service `nginx` est démarré sur chacune des 2 machines. Normalement vous constatez que le service est déjà démarré (par défaut) sur la machine ubuntu et non démarré sur la machine centos.

{{% expand "Réponse  :" %}}
```
ansible adhoc_lab --become --check -m systemd -a "name=nginx state=started"
```
{{% /expand %}}

- L'option `--check` à vérifier l'état des ressources sur les machines mais sans modifier la configuration`. Relancez la commande précédente pour le vérifier. Normalement le retour de la commande est le même (l'ordre peu varier).

- Lancez la commande avec `state=stopped` : le retour est inversé.

- Enlevez le `--check` pour vous assurer que le service est démarré sur chacune des machines.

- Visitez dans un navigateur l'ip d'un des hôtes pour voir la page d'accueil nginx.

## Ansible et les commandes unix

Il existe trois façon de lancer des commandes unix avec ansible:

- le module `command` utilise python pour lancez la commande.
  - les pipes et syntaxes bash ne fonctionnent pas.
  - il peut executer seulement les binaires.
  - il est cependant recommandé quand c'est possible car il n'est pas perturbé par l'environnement du shell sur les machine et donc plus prévisible.
  
- le module `shell` utilise un module python qui appelle un shell pour lancer une commande.
  - fonctionne comme le lancement d'une commande shell mais utilise un module python.
  
- le module `raw`.
  - exécute une commande ssh brute.
  - ne nécessite pas python sur l'hote : on peut l'utiliser pour installer python justement.
  - ne dispose pas de l'option `creates` pour simuler de l'idempotence.

- Créez un fichier dans `/tmp` avec `touch` et l'un des modules précédents.

- Relancez la commande. Le retour est toujours `changed` car ces modules ne sont pas idempotents.

- Relancer l'un des modules `shell` ou `command` avec `touch` et l'option `creates` pour rendre l'opération idempotente. Ansible détecte alors que le fichier témoin existe et n'exécute pas la commande.

```
ansible adhoc_lab --become -m "command touch /tmp/file" -a "creates=/tmp/file"
```
