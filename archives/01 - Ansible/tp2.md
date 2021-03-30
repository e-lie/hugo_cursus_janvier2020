---
title: "TP2 - Créer un playbook de déploiement d'application flask" 
draft: false
weight: 22
---

## Création du projet

- Créez un nouveau dossier `tp2_flask_deployment`.
- Créez le fichier `ansible.cfg` comme précédemment.

```ini
[defaults]
inventory = ./inventory.cfg
roles_path = ./roles
host_key_checking = false
```

- Créez deux machines ubuntu `app1` et `app2`.

```
lxc launch ubuntu_ansible app1
lxc launch ubuntu_ansible app2
```

- Créez l'inventaire statique `inventory.cfg`.

```
$ lxc list # pour récupérer les adresses ip 
```


[all:vars]
ansible_user=<user>

[appservers]
app1 ansible_host=10.x.y.z
app2 ansible_host=10.x.y.z
```

- Ajoutez à l'intérieur les deux machines dans un groupe `appservers`.
- Pinguez les machines.

```
ansible all -m ping
```

{{% expand "Facultatif  :" %}}
- Configurez git et initialisez un dépôt git dans ce dossier.

```
git init   # à executer à la racine du projet
```

- Ajoutez un fichier `.gitignore` avec à l'intérieur:

```bash
*.retry   # Fichiers retry produits lors des execution en echec de ansible-playbook
```

- Committez vos modifications avec git.

```
git add -A
git commit -m "démarrage tp2"
```
{{% /expand %}}

## Premier playbook : installer les dépendances

Le but de ce projet est de déployer une application flask, c'est a dire une application web python.
Le code (très minimal) de cette application se trouve sur github à l'adresse: [https://github.com/e-lie/flask_hello_ansible.git](https://github.com/e-lie/flask_hello_ansible.git).

- N'hésitez pas consulter extensivement la documentation des modules avec leur exemple ou d'utiliser la commande de doc `ansible-doc <module>`

- Créons un playbook : ajoutez un fichier `flaskhello_deploy.yml` avec à l'intérieur:

```yaml
- hosts: <hotes_cible>
  
  tasks:
    - name: ping
      ping:
```

- Lancez ce playbook avec la commande `ansible-playbook <nom_playbook>`.

- Commençons par installer les dépendances de cette application. Tous nos serveurs d'application sont sur ubuntu. Nous pouvons donc utiliser le module `apt` pour installer les dépendances. Il fournit plus d'option que le module `package`.

- Avec le module `apt` installez les applications: `python3-dev`, `python3-pip`, `python3-virtualenv`, `virtualenv`, `nginx`, `git`. Donnez à cette tache le nom: `ensure basic dependencies are present`. Ajoutez, pour devenir root, la directive `become: yes` au début du playbook.

```yaml
    - name: Ensure apt dependencies are present
      apt:
        name:
          - python3-dev
          - python3-pip
          - python3-virtualenv
          - virtualenv
          - nginx
          - git
        state: present
```


- Lancez ce playbook sans rien appliquer avec la commande `ansible-playbook <nom_playbook> --check --diff`. La partie `--check` indique à Ansible de ne faire aucune modification. La partie `--diff` nous permet d'afficher ce qui changerait à l'application du playbook.

- Relancez bien votre playbook à chaque tache : comme Ansible est idempotent il n'est pas grave en situation de développement d'interrompre l'exécution du playbook et de reprendre l'exécution après un échec.

- Ajoutez une tâche `systemd` pour s'assurer que le service `nginx` est démarré.

```yaml
    - name: Ensure nginx service started
      systemd:
        name: nginx
        state: started
```

- Ajoutez une tache pour créer un utilisateur `flask` et l'ajouter au groupe `www-data`. Utilisez bien le paramètre `append: yes` pour éviter de supprimer des groupes à l'utilisateur.

```yaml
    - name: Add the user running webapp
      user:
        name: "flask"
        state: present
        append: yes # important pour ne pas supprimer les groupes d'un utilisateur existant
        groups:
          - "www-data"
```


<!-- TODO: faire plus court pour adhoc pour pouvoir explorer --check et become: et autres avec les playbooks -->
<!-- ## Installons nginx avec quelques modules et commandes ad-hoc

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
``` -->


## Récupérer le code de l'application

- Pour déployer le code de l'application deux options sont possibles.
  - Télécharger le code dans notre projet et le copier sur chaque serveur avec le module `sync` qui fait une copie rsync.
  - Utiliser le module `git`.

- Nous allons utiliser la deuxième option (`git`) qui est plus cohérente pour le déploiement et la gestion des versions logicielles. Allez voir la documentation comment utiliser ce module.
  
- Utilisez le pour télécharger le code source de l'application (branche `master`) dans le dossier `/home/flask/hello` mais en désactivant la mise à jour (au cas ou le code change).

```yaml
    - name: Git clone/update python hello webapp in user home
      git:
        repo: "https://github.com/e-lie/flask_hello_ansible.git"
        dest: /home/flask/hello
        version: "master"
        clone: yes
        update: no
```

- Lancez votre playbook et allez vérifier sur une machine en ssh que le code est bien téléchargé.

## Installez les dépendances python de l'application

Le langage python a son propre gestionnaire de dépendances `pip` qui permet d'installer facilement les librairies d'un projet. Il propose également un méchanisme d'isolation des paquets installés appelé `virtualenv`. Normalement installer les dépendances python nécessite 4 ou 5 commandes shell.

- La liste de nos dépendances est listée dans le fichier `requirements.txt` à la racine du dossier d'application.

- Nous voulons installer ces dépendances dans un dossier `venv` également à la racine de l'application.

- Nous voulons installer ces dépendance en version python3 avec l'argument `virtualenv_python: python3`.

Avec ces informations et la documentation du module `pip` installez les dépendances de l'application.

```yaml
    - name: Install python dependencies for the webapp in a virtualenv
      pip:
        requirements: /home/flask/hello/requirements.txt
        virtualenv: /home/flask/hello/venv
        virtualenv_python: python3
```

## Changer les permission sur le dossier application

Notre application sera executée en tant qu'utilisateur flask pour des raisons de sécurité. Pour cela le dossier doit appartenir à cet utilisateur or il a été créé en tant que root (à cause du `become: yes` de notre playbook).

- Créez une tache `file` qui change le propriétaire du dossier de façon récursive.

```yaml
    - name: Change permissions of app directory
      file:
        path: /home/flask/hello
        state: directory
        owner: "flask"
        recurse: true
```

## Module Template : configurer le service qui fera tourner l'application

Notre application doit tourner comme c'est souvent le cas en tant que service (systemd). Pour cela nous devons créer un fichier service adapté `hello.service` dans le le dossier `/etc/systemd/system/`.

Ce fichier est un fichier de configuration qui doit contenir le texte suivant:

```ini
[Unit]
Description=Gunicorn instance to serve hello
After=network.target

[Service]
User=flask
Group=www-data
WorkingDirectory=/home/flask/hello
Environment="PATH=/home/flask/hello/venv/bin"
ExecStart=/home/flask/hello/venv/bin/gunicorn --workers 3 --bind unix:hello.sock -m 007 app:app

[Install]
WantedBy=multi-user.target
```

Pour gérer les fichier de configuration on utilise généralement le module `template` qui permet à partir d'un fichier modèle situé dans le projet  ansible de créer dynamiquement un fichier de configuration adapté sur la machine distante.

- Créez un dossier `templates`, avec à l'intérieur le fichier `app.service.j2` contenant le texte précédent.
- Utilisez le module `template` pour le copier au bon endroit avec le nom `hello.service`.

- Utilisez ensuite `systemd` pour démarrer ce service (`state: restarted` ici pour le cas ou le fichier à changé).

## Configurer nginx

- Comme précédemment créez un fichier de configuration `hello.test.conf` dans le dossier `/etc/nginx/sites-available` à partir du fichier modèle:

`nginx.conf.j2`

```
server {
    listen 80;

    server_name hello.test;

    location / {
        include proxy_params;
        proxy_pass http://unix:/home/flask/hello/hello.sock;
    }
}
```

- Utilisez `file` pour créer un lien symbolique de ce fichier dans `/etc/nginx/sites-enabled` (avec l'option `force:yes` pour écraser le cas échéant).

- Ajoutez une tache pour supprimer le site `/etc/nginx/sites-enabled/default`.

- Ajouter une tache de redémarrage de nginx.

- Ajoutez `hello.test` dans votre fichier `/etc/hosts` pointant sur l'ip d'un des serveur d'application.

- Visitez l'application dans un navigateur et debugger le cas échéant.


# Correction intermédiaire

`flaskhello_deploy.yml`


{{% expand "Code de correction :" %}}
```yaml
- hosts: appservers
  become: yes

  tasks:
    - name: Update apt cache
      apt:
        update_cache: yes

    - name: Ensure apt dependencies are present
      apt:
        name:
          - python3-dev
          - python3-pip
          - python3-virtualenv
          - virtualenv
          - nginx
          - git
        state: present

    - name: Ensure nginx service started
      systemd:
        name: nginx
        state: started

    - name: Add the user running webapp
      user:
        name: "flask"
        state: present
        append: yes # important pour ne pas supprimer les groupes d'un utilisateur existant
        groups:
        - "www-data"

    - name: Git clone/update python hello webapp in user home
      git:
        repo: "https://github.com/e-lie/flask_hello_ansible.git"
        dest: /home/flask/hello
        version: "master"
        clone: yes
        update: no
    
    - name: Install python dependencies for the webapp in a virtualenv
      pip:
        requirements: /home/flask/hello/requirements.txt
        virtualenv: /home/flask/hello/venv
        virtualenv_python: python3
    
    - name: Change permissions of app directory recursively
      file:
        path: /home/flask/hello
        state: directory
        owner: "flask"
        group: www-data
        recurse: true
    
    - name: Template systemd service config
      template:
        src: templates/app.service.j2
        dest: /etc/systemd/system/hello.service
    
    - name: Start systemd app service
      systemd:
        name: "hello.service"
        state: restarted
        enabled: yes
    
    - name: Template nginx site config
      template:
        src: templates/nginx.conf.j2
        dest: /etc/nginx/sites-available/hello.test.conf
    
    - name: Remove default nginx site config
      file:
        path: /etc/nginx/sites-enabled/default
        state: absent
    
    - name: Enable nginx site for hello webapp
      file:
        src: /etc/nginx/sites-available/hello.test.conf
        dest: /etc/nginx/sites-enabled/hello.test.conf
        state: link
        force: yes
    
    - name: Restart nginx service
      systemd:
        name: "nginx"
        state: restarted
        enabled: yes
```

- Renommez votre fichier `flaskhello_deploy.yml` en `flaskhello_deploy_precorrection.yml`.
- Copiez la correction dans un nouveau fichier `flaskhello_deploy.yml`.
- Lancez le playbook de correction `ansible-playbook flaskhello_deploy.yml`.
- Après avoir ajouté `hello.test` à votre `/etc/hosts` testez votre application en visitant la page `hello.test`.
{{% /expand %}}


{{% expand "Facultatif  :" %}}
- Validez/Commitez votre version corrigée:
  
```
git add -A
git commit -m "tp2 correction intermediaire"
```

- Installez l'extension `git graph` dans vscode.
- Cliquez sur le bouton `Git Graph` en bas à gauche de la fenêtre puis cliquez sur le dernier point (commit) avec la légende **tp2 correction intermediaire**. Vous pouvez voir les fichiers et modifications ajoutées depuis le dernier commit.

!!! Nous constatons que git a mémorisé les versions successives du code et permet de revenir à une version antérieure de votre déploiement.

{{% /expand %}}

# Améliorer notre playbook avec des variables.

## Variables

Ajoutons des variables pour gérer dynamiquement les paramètres de notre déploiement:

- Ajoutez une section `vars:` avant la section `tasks:` du playbook.

- Mettez dans cette section la variable suivante (dictionnaire):

```yaml
  app:
    name: hello
    user: flask
    domain: hello.test
```

- Remplacez dans le playbook précédent et les deux fichiers de template:
  - toutes les occurence de la chaine `hello` par `{{ app.name }}`
  - toutes les occurence de la chaine `flask` par `{{ app.user }}`
  - toutes les occurence de la chaine `hello.test` par `{{ app.domain }}`

- Relancez le playbook : toutes les tâches devraient renvoyer `ok` à part les "restart" car les valeurs sont identiques.

{{% expand "Facultatif  :" %}}
- Ajoutez deux variables `repository` et `version` pour l'adresse du dépôt git et la version de l'application `master` par défaut.

- Remplacez les valeurs correspondante dans le playbook par ces nouvelles variables.


```yaml
app:
  name: hello
  user: flask
  domain: hello.test
  repository: https://github.com/e-lie/flask_hello_ansible.git
  version: master
```
{{% /expand %}}

- Pour la correction clonez le dépôt de base à l'adresse [https://github.com/e-lie/ansible_tp_corrections](https://github.com/e-lie/ansible_tp_corrections).
- Renommez le clone en tp2_before_handlers.
- ouvrez le projet avec VSCode.
- Activez la branche `tp2_before_handlers_correction` avec `git checkout tp2_before_handlers_correction`.

Le dépot contient également les corrigés du TP3 et TP4 dans d'autre branches.

Vous pouvez consultez la correction également directement sur le site de github.

## Ajouter un handler pour nginx et le service

Pour le moment dans notre playbook, les deux tâches de redémarrage de service sont en mode `restarted` c'est à dire qu'elles redémarrent le service à chaque exécution (résultat: `changed`) et ne sont donc pas idempotentes. En imaginant qu'on lance ce playbook toutes les 15 minutes dans un cron pour stabiliser la configuration, on aurait un redémarrage de nginx 4 fois par heure sans raison.

On désire plutôt ne relancer/recharger le service que lorsque la configuration conrespondante a été modifiée. c'est l'objet des taches spéciales nommées `handlers`.

Ajoutez une section `handlers:` à la suite

- Déplacez la tâche de redémarrage/reload de `nginx` dans cette section et mettez comme nom `reload nginx`.
- Ajoutez aux deux taches de modification de la configuration la directive `notify: <nom_du_handler>`.

- Testez votre playbook. il devrait être idempotent sauf le restart de `hello.service`.
- Testez le handler en ajoutant un commentaire dans le fichier de configuration `nginx.conf.j2`.

```yaml
    - name: template nginx site config
      template:
        src: templates/nginx.conf.j2
        dest: /etc/nginx/sites-available/{{ app.domain }}.conf
      notify: reload nginx

      ...

  handlers:
    - name: reload nginx
      systemd:
        name: "nginx"
        state: reloaded

# => penser aussi à supprimer la tâche de restart de nginx précédente
```

## Rendre le playbook dynamique avec une boucle.

Plutôt qu'une variable `app` unique on voudrait fournir au playbook une liste d'application à installer (liste potentiellement définie durant l'exécution).

- Identifiez dans le playbook précédent les tâches qui sont exactement communes aux deux installations.

!!! il s'agit des taches d'installation des dépendances apt et de vérification de l'état de nginx (démarré)

- Créez un nouveau fichier `deploy_app_tasks.yml` et copier à l'intérieur la liste de toutes les autres taches mais sans les handlers que vous laisserez à la fin du playbook.

!!! Il reste donc dans le playbook seulement les deux premières taches et les handlers, les autres taches (toutes celles qui contiennent des parties variables) sont dans `deploy_app_tasks.yml`.

- Ce nouveau fichier n'est pas à proprement parlé un `playbook` mais une liste de taches. utilisez `include_tasks:` pour importer cette liste de tâche à l'endroit ou vous les avez supprimées.
- Vérifiez que le playbook fonctionne et est toujours idempotent.
- Ajoutez une tâche `debug: msg={{ app }}` au début du playbook pour visualiser le contenu de la variable.


- Ensuite remplacez la variable `app` par une liste `flask_apps` de deux dictionnaires (avec `name`, `domain`, `user` différents les deux dictionnaires et `repository` et `version` identiques).

```yaml
flask_apps:
  - name: hello
    domain: "hello.test"
    user: "flask1"
    version: master
    repository: https://github.com/e-lie/flask_hello_ansible.git

  - name: hello2
    domain: "hello2.test"
    user: "flask2"
    version: master
    repository: https://github.com/e-lie/flask_hello_ansible.git
```

- Utilisez les directives `loop` et `loop_control`+`loop_var` sur la tâche `include_tasks` pour inclure les taches pour chacune des deux applications.

- Créez le dossier `group_vars` et déplacez le dictionnaire `flask_apps` dans un fichier `group_vars/appservers.yml`. Comme son nom l'indique ce dossier permet de définir les variables pour un groupe de serveurs dans un fichier externe.

- Testez en relançant le playbook que le déplacement des variables est pris en compte correctement.

## Correction

- Pour la correction clonez le dépôt de base à l'adresse [https://github.com/e-lie/ansible_tp_corrections](https://github.com/e-lie/ansible_tp_corrections).
- Renommez le clone en tp2.
- ouvrez le projet avec VSCode.
- Activez la branche `tp2_correction` avec `git checkout tp2_correction`.

Le dépot contient également les corrigés du TP3 et TP4 dans d'autre branches.

Vous pouvez consultez la correction également directement sur le site de github.

## Bonus

Pour ceux ou celles qui sont allé-es vite, vous pouvez tenter de créer une nouvelle version de votre playbook portable entre centos et ubuntu. Pour cela utilisez la directive `when: ansible_os_family == 'Debian'` ou `RedHat`.

## Bonus 2 pour pratiquer

Essayez de déployer une version plus complexe d'application flask avec une base de donnée mysql: [https://github.com/miguelgrinberg/microblog/tree/v0.17](https://github.com/miguelgrinberg/microblog/tree/v0.17)

Il s'agit de l'application construite au fur et à mesure dans un [magnifique tutoriel python](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-xvii-deployment-on-linux). Ce chapitre indique comment déployer l'application sur linux.
