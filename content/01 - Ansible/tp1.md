---
title: 'TP1 - Mise en place et Ansible ad-hoc'
draft: false
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


## Explorer LXD 

LXD est une technologie de conteneurs actuellement promue par canonical (ubuntu) qui permet de faire des conteneur linux orientés systèmes plutôt qu'application. Par exemple `systemd` est disponible à l'intérieur des conteneurs contrairement aux conteneurs Docker.

- Affichez la liste des conteneurs avec `sudo lxc list`.
- Maintenant lançons notre premier conteneur `ubuntu` avec `lxc launch images:ubuntu/bionic/amd64 ubu1`.
- Listez à nouveau les conteneurs lxc.
- Ce conteneur est un ubuntu minimal et n'a donc pas de serveur SSH pour se connecter. Pour lancez des commandes dans le conteneur on utilise une commande LXC pour s'y connecter `lxc exec <non_conteneur> -- <commande>`. Dans notre cas nous voulons lancer bash pour ouvrir un shell dans le conteneur : `lxc exec ubu1 -- bash`.
- Nous pouvons installer des logiciels dans le conteneur comme dans une VM. Pour sortir du conteneur on peut simplement utiliser `exit`.

- Un peu comme avec Docker, LXC utilise des images modèles pour créer des conteneurs. Affichez la liste des images avec `sudo lxc image list`. Deux images sont disponibles l'image ubuntu vide téléchargée et utilisée pour créer ubu1 et une autre `ubuntu_ansible`. Cette deuxième image contient déjà la configuration nécessaire pour être utilisée avec ansible (SSH + Python + Un utilisateur + une clé SSH)
- Créez deux nouveaux conteneurs à partir de cette image avec: `sudo lxc launch ubuntu_ansible host1 host2`
- Supprimez le conteneur `ubu1` avec `sudo lxc delete ubu1`.

- Nous allons essayer de nous connecter à `host1` et `host2` en ssh pour vérifier que la clé ssh est bien configurée et vérifiez dans chaque machine que le sudo est configuré sans mot de passe avec `sudo -i`:

{{% expand "Réponse   :" %}}
```bash
sudo lxc list # pour récupérer l'adresse IP de host1
ssh elk-master@<adresse_ip> # le mot de passe de la clé ssh est el4sticssh
```
Une fois dans le conteneur:

```
sudo -i
exit
```
Répétez l'opération pour host2
{{% /expand %}}

## Créer un projet de code Ansible

- Essayez de "pinguer" la nouvelle machine avec ansible comme précédemment. Quel est le problème ?

{{% expand "Réponse  :" %}}
- Ajoutez la machine au fichier `/etc/ansible/hosts`.
- Lancez `ansible all -m ping`.
- Le problème de connexion provient du fait que ansible ne peut pas deviner l'utilisateur de connexion. Nous utiliserons `ansible.cfg` avec le parametre `ansible_user` par la suite pour régler ce problème de connexion.
{{% /expand %}}

Lorsqu'on développe Ansible fonctionne comme un projet de code.

- Créez quelque part un dossier projet `adhoc_lab`.
- Initialisez le en dépôt git:

```
cd adhoc_lab
git init
```

- Ouvrez Visual Studio Code : `snap install code --classic`
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
