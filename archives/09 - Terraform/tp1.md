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


## Explorer LXD 

LXD est une technologie de conteneurs actuellement promue par canonical (ubuntu) qui permet de faire des conteneur linux orientés systèmes plutôt qu'application. Par exemple `systemd` est disponible à l'intérieur des conteneurs contrairement aux conteneurs Docker.

LXD est déjà installé et initialisé sur notre ubuntu (sinon `apt install snapd` + `snap install lxd` + ajouter votre utilisateur courant au group unix `lxd`).

Il faut cependant l'initialiser avec : `lxd init`

- Cette commande vous pose un certain nombre de questions pour la configuration et vous pouvez garder TOUTES les valeurs par défaut en fait ENTER simplement à chaque question.

- Affichez la liste des conteneurs avec `lxc list`. Aucun conteneur ne tourne.
- Maintenant lançons notre premier conteneur `centos` avec `lxc launch images:centos/7/amd64 centos1`.
- Listez à nouveau les conteneurs lxc.
- Ce conteneur est un centos minimal et n'a donc pas de serveur SSH pour se connecter. Pour lancez des commandes dans le conteneur on utilise une commande LXC pour s'y connecter `lxc exec <non_conteneur> -- <commande>`. Dans notre cas nous voulons lancer bash pour ouvrir un shell dans le conteneur : `lxc exec centos1 -- bash`.
- Nous pouvons installer des logiciels dans le conteneur comme dans une VM. Pour sortir du conteneur on peut simplement utiliser `exit`.

- Un peu comme avec Docker, LXC utilise des images modèles pour créer des conteneurs. Affichez la liste des images avec `lxc image list`. Trois images sont disponibles l'image centos vide téléchargée et utilisée pour créer centos1 et deux autres images préconfigurée `ubuntu_ansible` et `centos_ansible`. Ces images contiennent déjà la configuration nécessaire pour être utilisée avec ansible (SSH + Python + Un utilisateur + une clé SSH).

- Supprimez la machine centos1 avec `lxc stop centos1 && lxc delete centos1`

## Facultatif : Configurer un conteneur pour Ansible manuellement
{{% expand "Facultatif :" %}}


- Connectez vous dans le conteneur avec la commande `lxc exec` précédente. Une fois dans le conteneur  lancez les commandes suivantes:

##### Pour centos

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
useradd -m -s /bin/bash -G wheel stagiaire

# Définission du mot de passe
passwd stagiaire

exit
```

##### Pour ubuntu

```bash
# installer SSH
apt update && apt install -y openssh-server sudo

# verifier que python2 ou python3 est installé
python --version || python3 --version

## Attention copiez cette commande bien correctement
# configurer sudo pour être sans password
sed -i 's@\(%sudo.*\)ALL@\1 NOPASSWD: ALL@' /etc/sudoers

# Créer votre utilisateur de connexion
useradd -m -s /bin/bash -G sudo stagiaire

# Définission du mot de passe
passwd stagiaire

exit
```

#### Copier la clé ssh à l'intérieur

Maintenant nous devons configurer une identité (ou clé) ssh pour pouvoir nous connecter au serveur de façon plus automatique et sécurisée. Cette clé a déjà été créé pour votre utilisateur stagiaire. Il reste à copier la version publique dans le conteneur.

- On copie notre clé dans le conteneur en se connectant en SSH avec `ssh_copy_id`:

```bash
lxc list # permet de trouver l'ip du conteneur
ssh-copy-id -i ~/.ssh/id_ed25519 stagiaire@<ip_conteneur>
ssh stagiaire@<ip_conteneur>
```

### Exporter nos conteneurs en image pour pouvoir les multipliers

LXD permet de gérer aisément des snapshots de nos conteneurs sous forme d'images (archive du systeme de fichier + manifeste).

Nous allons maintenant créer snapshots opérationnels de base qui vont nous permettre de construire notre lab d'infrastructure en local.

```bash
lxc stop centos1
lxc publish --alias centos_ansible_ready centos1
lxc image list
```

On peut ensuite lancer autant de conteneur que nécessaire avec la commande launch:

```bash
lxc launch centos_ansible_ready centos2 centos3
```

- Une fois l'image exportée faite supprimez les conteneurs.

```bash
lxc delete centos1 centos2 centos3 --force
```

{{% /expand %}}

### Récupérer les images de correction depuis un remote LXD

Pour avoir tous les mêmes images de base récupérons les depuis un serveur dédié à la formation. Un serveur distant LXD est appelé un `remote`.

- Ajoutez le remote `tp-images` avec la commande:

```bash
lxc remote add tp-images https://lxd-images.dopl.uk --protocol lxd
```

- Le mot de passe est: `formation_ansible`.


- Copiez ensuite les images depuis ce remote dans le dépot d'image local avec :

```bash
lxc image copy tp-images:centos_ansible local: --copy-aliases --auto-update
lxc image copy tp-images:ubuntu_ansible local: --copy-aliases --auto-update
```


### Lancer et tester les conteneurs

Créons à partir des images du remotes un conteneur ubuntu et un autre centos:

```bash
lxc launch ubuntu_ansible ubu1
lxc launch centos_ansible centos1
```

- Pour se connecter en SSH nous allons donc utiliser une clé SSH appelée `id_stagiaire` qui devrait être présente dans votre dossier `~/.ssh/`. Vérifiez cela en lançant `ls -l /home/stagiaire/.ssh`.

- Déverrouillez cette clé ssh avec `ssh-add ~/.ssh/id_stagiaire` et le mot de passe `devops101` (le ssh-agent doit être démarré dans le shell pour que cette commande fonctionne si ce n'est pas le cas `eval $(ssh-agent)`).

- Essayez de vous connecter à `ubu1` et `centos1` en ssh pour vérifier que la clé ssh est bien configurée et vérifiez dans chaque machine que le sudo est configuré sans mot de passe avec `sudo -i`.


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

Un projet Ansible implique généralement une configuration Ansible spécifique décrite dans un fichier `ansible.cfg`

- Ajoutez à la racine du projet un tel fichier `ansible.cfg` avec à l'intérieur:

```ini
[defaults]
inventory = ./inventory.cfg
roles_path = ./roles
host_key_checking = false # nécessaire pour les labs ou on créé et supprime des machines constamment avec des signatures SSH changées.
```

- Créez le fichier d'inventaire spécifié dans `ansible.cfg` et ajoutez à l'intérieur notre nouvelle machine `hote1`. Il faut pour cela lister les conteneurs lxc lancés.

```
lxc list # récupérer l'ip de la machine
```

Créez et complétez le fichier `inventory.cfg` d'après ce modèle:

```ini
ubu1 ansible_host=<ip>

[all:vars]
ansible_user=<votre_user>
```

## Contacter nos nouvelles machines

Ansible cherche la configuration locale dans le dossier courant. Conséquence: on **lance généralement** toutes les commandes ansible depuis **la racine de notre projet**.

- Dans le dossier du projet, essayez de relancer la commande ad-hoc `ping` sur cette machine.

- Ansible implique le cas échéant (login avec clé ssh) de déverrouiller la clé ssh pour se connecter à **chaque** hôte. Lorsqu'on en a plusieurs il est donc nécessaire de la déverrouiller en amont avec l'agent ssh pour ne pas perturber l'exécution des commandes ansible. Pour cela : `ssh-add`.

- Créez un groupe `adhoc_lab` et ajoutez les deux machines `ubu1` et  `centos1`.

{{% expand "Réponse  :" %}}
```ini
[all:vars]
ansible_user=<votre_user>

[adhoc_lab]
ubu1 ansible_host=<ip>
centos1 ansible_host=<ip>
```
{{% /expand %}}

- Lancez `ping` sur les deux machines.

{{% expand "Réponse  :" %}}
- `ansible adhoc_lab -m ping`
{{% /expand %}}

- Nous avons jusqu'à présent utilisé une connexion ssh par clé et précisé l'utilisateur de connexion dans le fichier `ansible.cfg`. Cependant on peut aussi utiliser une connexion par mot de passe et préciser l'utilisateur et le mot de passe dans l'inventaire ou en lançant la commande.

En précisant les paramètres de connexion dans le playbook il et aussi possible d'avoir des modes de connexion différents pour chaque machine.


## Installons nginx avec quelques modules et commandes ad-hoc

- Modifiez l'inventaire pour créer deux sous-groupes de `adhoc_lab`, `centos_hosts` et `ubuntu_hosts` avec deux machines dans chacun. (utilisez pour cela `[adhoc_lab:children]`)


```ini
[all:vars]
ansible_user=<votre_user>

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
