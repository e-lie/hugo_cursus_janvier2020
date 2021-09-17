---
title: "TP3 - Structurer le projet avec des roles" 
draft: false
weight: 23
---

## Ajouter un provisionneur d'infra maison pour créer les machines automatiquement

- Clonez la correction du TP2 (lien à la fin du TP2) et renommez là en `tp3_provisionner_roles`.
- Chargez ce dossier dans VSCode (vous pouvez fermer le tp2).

Dans notre infra virtuelle, nous avons trois machines dans deux groupes. Quand notre lab d'infra grossit il devient laborieux de créer les machines et affecter les ip à la main. En particulier détruire le lab et le reconstruire est pénible. Nous allons pour cela introduire un playbook de provisionning qui va créer les conteneurs lxd en définissant leur ip à partir de l'inventaire.

- modifiez l'inventaire comme suit:

```ini
[all:vars]
ansible_user=<votre_user>

[appservers]
app1 ansible_host=10.x.y.121 container_image=ubuntu_ansible node_state=started
app2 ansible_host=10.x.y.122 container_image=ubuntu_ansible node_state=started

[dbservers]
db1 ansible_host=10.x.y.131 container_image=ubuntu_ansible node_state=started
```

- Remplacez `x` et `y` dans l'adresse IP par celle fournies par votre réseau virtuel lxd (faites `lxc list` et copier simple les deux chiffre du milieu des adresses IP)

- Ajoutez un playbook `provision_lxd_infra.yml` dans un dossier `provisionners` contenant:

```yaml
- hosts: localhost
  connection: local

  tasks:
    - name: Setup linux containers for the infrastructure simulation
      lxd_container:
        name: "{{ item }}"
        state: "{{ hostvars[item]['node_state'] }}"
        source:
          type: image
          alias: "{{ hostvars[item]['container_image'] }}"
        profiles: ["default"]
        config:
          security.nesting: 'true' 
          security.privileged: 'false' 
        devices:
          # configure network interface
          eth0:
            type: nic
            nictype: bridged
            parent: lxdbr0
            # get ip address from inventory
            ipv4.address: "{{ hostvars[item].ansible_host }}"

        # Comment following line if you installed lxd using apt
        url: unix:/var/snap/lxd/common/lxd/unix.socket
        wait_for_ipv4_addresses: true
        timeout: 600

      register: containers
      loop: "{{ groups['all'] }}"
    

    # Uncomment following if you want to populate hosts file pour container local hostnames
    # AND launch playbook with --ask-become-pass option

    # - name: Config /etc/hosts file accordingly
    #   become: yes
    #   lineinfile:
    #     path: /etc/hosts
    #     regexp: ".*{{ item }}$"
    #     line: "{{ hostvars[item].ansible_host }}    {{ item }}"
    #     state: "present"
    #   loop: "{{ groups['all'] }}"
```

- Etudions le playbook (explication démo).

- Lancez le playbook avec `sudo` car `lxd` se contrôle en root sur localhost: `sudo ansible-playbook provision_lxd_infra` (c'est le seul cas exceptionnel ou ansible-playbook doit être lancé avec sudo, pour les autre playbooks ce n'est pas le cas)

- Lancez `lxc list` pour afficher les nouvelles machines de notre infra et vérifier que le serveur de base de données a bien été créé.

## Facultatif: Ajouter une machine mysql simple avec un role externe

{{% expand "Facultatif  :" %}}

- Créez à la racine du projet le dossier `roles` dans lequel seront rangés tous les roles (c'est une convention ansible à respecter).
- Cherchez sur [https://galaxy.ansible.com/](https://galaxy.ansible.com/) le **nom** du role `mysql` de `geerlingguy`. Il s'agit de l'auteur d'un livre de référence **"Ansible for DevOps"** et de nombreux roles de références.
- Pour décrire les roles nécessaires pour notre projet il faut créer un fichier `requirements.yml` contenant la liste de ces roles. Ce fichier peut être n'importe où mais il faut généralement le mettre directement dans le dossier `roles` (autre convention).

- Ajoutez à l'intérieur du fichier:

```yaml
- src: <nom_du_role_mysql>
```

- Pour installez le role lancez ensuite `ansible-galaxy install -r roles/requirements.yml -p roles`.

- Ajoutez la ligne `geerlingguy.*` au fichier `.gitignore` pour ne pas ajouter les roles externes à votre dépot git.

- Pour installer notre base de données, ajoutez un playbook `dbservers.yml` appliqué au groupe `dbservers` avec juste une section:

```yaml
    ...
    roles:
        - <nom_role>
```

- Faire un playbook `configuration.yml` qui importe juste les deux playbooks `flaskapp_deploy.yml` et `dbservers.yml` avec `import_playbook`.

- Lancer la configuration de toute l'infra avec ce playbook.

{{% /expand %}}

## Transformer notre playbook en role

- Si ce n'est pas fait, créez à la racine du projet le dossier `roles` dans lequel seront rangés tous les roles (c'est une convention ansible à respecter).
- Créer un dossier `flaskapp` dans `roles`.
- Ajoutez à l'intérieur l'arborescence:

```
flaskapp
├── defaults
│   └── main.yml
├── handlers
│   └── main.yml
├── tasks
│   ├── deploy_app_tasks.yml
│   └── main.yml
└── templates
    ├── app.service.j2
    └── nginx.conf.j2
```

- Les templates et les listes de handlers/tasks sont a mettre dans les fichiers correspondants (voir plus bas)
- Le fichier `defaults/main.yml` permet de définir des valeurs par défaut pour les variables du role. Mettez à l'intérieur une application par défaut:

```yaml
flask_apps:
  - name: defaultflask
    domain: defaultflask.test
    repository: https://github.com/e-lie/flask_hello_ansible.git
    version: master
    user: defaultflask
```

Ces valeurs seront écrasées par celles fournies dans le dossier `group_vars` (la liste de deux applications du TP2). Elle est présente pour éviter que le role plante en l'absence de variable (valeurs de fallback).

- Copiez les tâches (juste la liste de tiret sans l'intitulé de section `tasks:`) contenues dans le playbook `appservers` dans le fichier `tasks/main.yml`.

- De la même façon copiez le handler dans `handlers/main.yml` sans l'intitulé `handlers:`.
- Copiez également le fichier `deploy_flask_tasks.yml` dans le dossier `tasks`.
- Déplacez vos deux fichiers de template dans le dossier `templates` du role (et non celui à la racine que vous pouvez supprimer).

- Pour appeler notre nouveau role, supprimez les sections `tasks:` et `handlers:` du playbook `appservers.yml` et ajoutez à la place:

```yaml
  roles:
    - flaskapp
```

- Votre role est prêt : lancez `appservers.yml` et debuggez le résultat le cas échéant.

## Facultatif: Ajouter un paramètre d'exécution à notre rôle pour mettre à jour l'application.

{{% expand "Facultatif  :" %}}

Notre role `flaskapp` est jusqu'ici concu pour être un rôle de configuration, idéalement lancé régulièrement à l'aide d'un cron ou de AWX. En particulier, nous avons mis les paramètres `update` et `force` à `false` au niveau de notre tâche qui clone le code avec git. Ces paramètres indiquent si la tâche doit récupérer systématiquement la dernière version. Dans notre cas il pourrait être dangereux de mettre à jour l'application à chaque fois donc nous avons mis `false` pour éviter d'écraser l'application existante avec une version récente.

Nous aimerions maintenant créer un playbook `upgrade_apps.yml` qui contrairement à `configuration.yml` devrait être lancé ponctuellement pour mettre à jour l'application. Il serait bête de ne pas réutiliser notre role pour cette tâche : nous allons rajouter un paramère `flask_upgrade_apps`.

- Remplacez dans la tâche `git` la valeur `false` des paramètres `update` et `force` par cette variable.

!! Vous noterez que son nom commence par `flask_` car elle fait partie du role `flaskapp`. Cette façon de créer une sorte d'espace de nom simple pour chaque role est une bonne pratique.

- Ajoutez une valeur par défaut `no` ou `false` pour cette variable dans le role (defaults/main.yml).

- Créez le playbook `upgrade_apps.yml` qui appelle le role mais avec une section `vars:` qui définit la variable upgrade à `yes` ou `true`.

- Pour tester votre playbook et pourvoir constater une modification de version vous pouvez éditer `group_vars/appservers.yml` pour changer la version des deux applications à `version2`. Le playbook installera alors une autre version de l'application présente dans le dépot git.

- Charger l'application dans un navigateur avec l'une des IPs. Vous devriez voir "version: 2" apparaître en bas de la page.

{{% /expand %}}

## Correction

- Pour la correction clonez le dépôt de base à l'adresse [https://github.com/e-lie/ansible_tp_corrections](https://github.com/e-lie/ansible_tp_corrections).
- Renommez le clone en tp3.
- ouvrez le projet avec VSCode.
- Activez la branche `tp3_correction` avec `git checkout tp3_correction`.

Il contient également les corrigés du TP2 et TP4 dans d'autre branches.
## Bonus 

Essayez différents exemples de projets de Geerlingguy accessibles sur github à l'adresse [https://github.com/geerlingguy/ansible-for-devops](https://github.com/geerlingguy/ansible-for-devops).


## Bonus 2 - Unit testing de role avec Molecule

Pour des roles fiable il est conseillé d'utiliser l'outil de testing molecule dès la création d'un nouveau role pour effectuer des tests unitaire dessus dans un environnement virtuel comme Docker:

- Tutoriel : https://www.adictosaltrabajo.com/2020/05/08/ansible-testing-using-molecule-with-ansible-as-verifier/
- Documentation : https://molecule.readthedocs.io/en/latest/