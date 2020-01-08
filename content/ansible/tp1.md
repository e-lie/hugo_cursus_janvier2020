---
title: 'TP1 - Mise en place d''Ansible et usage des commandes ad-hoc'
visible: true
---

## Installation de Ansible

- Installez Ansible au niveau du système avec `pip`.

```
sudo pip3 install ansible
```

- Ou avec `apt` en lançant:

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

- Comment mettre à jour Ansible lorsqu'il est installé avec `pip` ?

```
sudo pip3 install ansible --upgrade
```

- Traditionnellement lorsqu'on veut vérifier le bon fonctionnement d'une configuration on utilise `ansible all -m ping`. Que signifie-t-elle ?


!!! Cette commande lance le module ansible `ping` (test de connection ansible) sur le groupe all c'est à dire toutes les machines de notre inventaire. Il s'agit d'une commande ad-hoc ansible.


- Lancez la commande précédente. Que ce passe-t-il ?

```
ansible all -m ping 
=> erreur car all ne matche aucun hôte.
```

- Utilisez en plus l'option `-vvv` pour mettre en mode très verbeux. Ce mode est très efficace pour **débugger** lorsqu'une erreur inconnue se présente. Que se passe-t-il avec l'inventaire ?

```
ansible essaye de trouver un inventaire c'est à dire une liste de machine à contacter et cherche par défaut le fichier `/etc/ansible/hosts`. Comme il ne trouve rien il crée un inventaire implicite contenant uniquement localhost.
```

- Testez l'installation avec la commande `ansible` en vous connectant à votre machine `localhost` et en utilisant le module `ping`.

```
ansible localhost -m ping

=> la commande échoue car ssh n'est pas configuré sur l'hote mais la machine est contactée (sortie en rouge). Nous allons dans la suite créer des machines de lab avec ssh installé.
```

- Ajoutez la ligne `hotelocal ansible_host=127.0.0.1` dans l'inventaire par défaut (le chemin est indiqué dans). Et pinguer hotelocal.

```
ouvrir pour cela /etc/ansible/hosts

ansible hotelocal -m ping
=> idem echec de login
```

## Installer LXD et créer un conteneur adapté

LXD est une technologie de conteneurs actuellement promue par canonical (ubuntu) qui permet de faire des conteneur linux orientés systèmes plutôt qu'application. Par exemple `systemd` est disponible à l'intérieur des conteneurs contrairement aux conteneurs Docker.

- Installez LXD avec snap (méthode recommandée): `sudo snap install lxd`

- Initialisons la configuration LXD avec `sudo lxd init`. Gardez les paramètres par défauts pour toutes les questions.

- Maintenant lançons notre premier conteneur `CentOS` avec `lxc launch images:centos/7/amd64 centos1`.

- Pour lancez des commandes dans le conteneur on utilise `lxc exec <non_conteneur> -- <commande>`. Dans notre cas nous voulons lancer bash pour ouvrir un shell dans le conteneur : `lxc exec centos1 -- bash`.


- Une fois dans le conteneur (centos) lancez les commandes suivantes:

```bash
# installer SSH
yum update -y && yum install -y openssh-server sudo

systemctl start sshd

# verifier que python2 ou python3 est installé
python --version || python3 --version

## Attention copiez cette commande bien correctement
# configurer sudo pour être sans password
sed -i 's@\(%wheel.*\)ALL@\1 NOPASSWD: ALL@' /etc/sudoers

# Créer votre utilisateur de connexion
useradd -m -s /bin/bash -G wheel <yourname>

# Définission du mot de passe
passwd <yourname>

exit
```

- On teste en copiant notre clé sur le conteneur puis en se connectant en SSH

```bash
ssh-keygen -t ed25519 # gardez l'emplacement par défaut et mettez une passphrase
sudo lxc list # permet de trouver l'ip du conteneur
ssh-copy-id -i ~/.ssh/id_ed25519 <yourname>@<ip_conteneur>
ssh <yourname>@<ip_conteneur>
```

- Refaite rapidement la même opération avec un conteneur ubuntu:
  - `sudo lxc launch images:ubuntu/bionic/amd64 ubuntu1`
  - connectez dans le conteneur et lancez:

```bash
# installer SSH
apt update && apt install -y openssh-server

# verifier que python ou python3 est installé
python --version || python3 --version

# configurer sudo pour être sans password
sed -i 's@\(%sudo.*\)ALL@\1 NOPASSWD: ALL@' /etc/sudoers

# Créer votre utilisateur de connexion sudo
useradd -m -s /bin/bash -G sudo <yourname>

# Définission du mot de passe
passwd <yourname>

exit
```
  - Ajoutez votre clé SSH au conteneur comme précédemment:

```
sudo lxc list # permet de trouver l'ip du conteneur
ssh-copy-id -i ~/.ssh/id_ed25519 <yourname>@<ip_conteneur>
ssh <yourname>@<ip_conteneur>
```


## Multiplier les conteneurs : exporter une image de base

LXD permet de gérer aisément des snapshots de nos conteneurs sous forme d'images (archive du systeme de fichier + manifeste).

- Pour lister les images faites: `sudo lxc image list`

Nous allons maintenant créer snapshots opérationnels de base qui vont nous permettre de construire notre lab d'infrastructure en local.

```
sudo lxc stop centos1 ubuntu1
sudo lxc publish --alias centos_ansible centos1
sudo lxc publish --alias ubuntu_ansible ubuntu1
sudo lxc image list
```

```
sudo lxc launch ubuntu_ansible host1
sudo lxc launch centos_ansible host2
```

- Essayez de vous connecter à `host1` et `host2` en ssh pour vérifier que la clé ssh est bien configurée et vérifiez dans chaque machine que le sudo est configuré sans mot de passe avec `sudo -i`.

- Une fois la modification faite supprimé les deux conteneurs initiaux.
 
```
sudo lxc delete ubuntu1 centos1
```

## Créer un projet de code Ansible

- Essayez de "pinguer" la nouvelle machine comme précédemment. Quel est le problème ?

!!!! - Ajoutez la machine au fichier `/etc/ansible/hosts`.
!!!! - Le problème de connexion provient du fait que ansible ne peut pas deviner l'utilisateur de connexion. Nous utiliserons `ansible.cfg` avec le parametre `ansible_user` par la suite pour régler ce problème de connexion.


Lorsqu'on développe Ansible fonctionne comme un projet de code.

- Créez quelque part un dossier projet `adhoc_lab`.
- Initialisez le en dépôt git.

```
cd adhoc_lab
git init
```

- Installez Visual Studio Code avec snap : `snap install code --classic`
- Installez l'extension Ansible dans VSCode.
- Ouvrez le dossier du projet avec `Open Folder...`

Un projet Ansible géré par git implique généralement une configuration Ansible spécifique décrite dans un fichier `ansible.cfg`

- Ajoutez à la racine du projet un tel fichier `ansible.cfg` avec à l'intérieur:

```ini
[defaults]
inventory = ./inventory.cfg
roles_path = ./roles
host_key_checking = false # nécessaire pour les labs ou on créé et supprime des machines constamment avec des signatures SSH changées.
```

- Créez le fichier d'inventaire spécifié dans `ansible.cfg` et ajoutez à l'intérieur notre nouvelle machine `hote1`. Il faut pour cela lister les conteneurs lxc lancés.

```
sudo lxc list
```

`inventory.cfg`

```ini
host1 ansible_host=<ip>

[all:vars]
ansible_user=<votre_user>
```

- Dans le dossier du projet, essayez de relancer la commande ad-hoc `ping` sur cette machine. Ansible cherche la configuration locale dans le dossier courant. Conséquence: on lance généralement toutes les commandes ansible depuis la racine de notre projet.


- Ansible implique le cas échéant (login avec clé ssh) de déverrouiller la clé ssh pour se connecter à **chaque** hôte. Lorsqu'on en a plusieurs il est donc nécessaire de la déverrouiller en amont avec l'agent ssh pour ne pas perturber l'exécution des commandes ansible:

```
ssh-add
```

- Ajoutez la première machine (ubuntu) ainsi qu'une deuxième créé avec centos dans un groupe `adhoc_lab` et lancez `ping` sur les deux machines.

```ini
[all:vars]
ansible_user=<votre_user>

[adhoc_lab]
host1 ansible_host=<ip>
host2 ansible_host=<ip>
```

- Nous avons jusqu'à présent utilisé une connexion ssh par clé et précisé l'utilisateur de connexion dans le fichier `ansible.cfg`. Cependant on peut aussi utiliser une connexion par mot de passe et préciser l'utilisateur et le mot de passe dans l'inventaire ou en lançant la commande.

- Ajoutez le paramère `ansible_user=<username>` à la fin de la ligne de chacune de vos machines (après ansible_host).
- Ajoutez également le paramètre `ansible_passwd=<votre_mot_depasse>`
- Relancez un ping pour vérifier que la connexion fonctionne.

En précisant les paramètres de connexion dans le playbook il et possible d'avoir des mode de connexion différents pour chaque machine.

- Rétablissez la connexion en clé ssh avec l'utilisateur précisé dans `ansible.cfg`. C'est la méthode standard qui facilite la gestion centralisé et la scalabilité de votre infrastructure (cf module 4 avec awx)

## Installons nginx avec quelques modules et commandes ad-hoc

- Modifiez l'inventaire pour créer deux sous-groupes de `adhoc_lab`, `centos_hosts` et `ubuntu_hosts` avec deux machines dans chacun. (utilisez pour cela `[adhoc_lab:children]`)


```ini
[all:vars]
ansible_user=<votre_user>

[ubuntu_hosts]
host1 ansible_host=<ip>

[centos_hosts]
host2 ansible_host=<ip>

[adhoc_lab:children]
ubuntu_hosts
centos_hosts
```

Dans un inventaire ansible on commence toujours par créer les plus petits sous groupes puis on les rassemble en plus grands groupes.

- Pinguer chacun des 3 groupes avec une commande ad hoc.

- Lancez ensuite le module `setup` pour obtenir des informations sur les machines.

Nous allons maintenant installer `nginx` sur les 4 machines. Il y a plusieurs façons d'installer des logiciels grâce à Ansible: en utilisant le gestionnaire de paquets de la distribution ou un gestionnaire spécifique comme `pip` ou `npm`. Chaque méthode dispose d'un module ansible spécifique.

- Nous voudrions installer nginx avec la même commande sur les 4 machines or ubuntu utilise `apt` et centos utilise `yum`. Nous pouvons pour cela utiliser le module `package` qui permet d'uniformiser l'installation pour les cas simples.
  - Allez voir la documentation de ce module
  - utilisez `--become` pour devenir root avant d'exécuter la commande (cf élévation de privilège dans le cours2)
  - Utilisez le pour installer `nginx`
  - La commande fonctionne seulement sur les hotes ubuntu. Le retour est donc vert pour deux machine et rouge pour les deux autres.

```
ansible adhoc_lab --become -m package -a "name=nginx state=present"
```

- Pour résoudre le problème installez `epel-release` sur les deux machines centos.

```
ansible centos_hosts --become -m package -a "name=epel-release state=present"
```

- Relancez la commande d'installation de `nginx` sur les quatres machines. Que remarque-t-on ?

```
ansible adhoc_lab -m package -a name=nginx state=present

les deux machines centos on un retour changed jaune alors que les machines ubuntu on un retour ok vert. C'est l'idempotence: ansible nous indique que nginx est déjà présent sur les ubuntu.
```

- Utiliser le module `systemd` et l'option `--check` pour vérifier si le service `nginx` est démarré sur chacune des 4 machines. Normalement vous constatez que le service est déjà démarré (par défaut) sur les machines ubuntu (retour vert) et pas encore démarré sur les machines centos (retour jaune).

```
ansible adhoc_lab --become --check -m systemd -a "name=nginx state=started"
```

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
