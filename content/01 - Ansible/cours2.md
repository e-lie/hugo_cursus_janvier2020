---
title: 'Cours 2 - Les playbooks Ansible'
draft: false
weight: 11
---

Les commandes ad-hoc sont des appels directs de modules Ansible qui fonctionnent de façon idempotente mais ne présente pas les avantages du code qui donne tout son intérêt à l'IaC:

- texte descriptif écrit une fois pour toute
- logique lisible et auditable
- versionnable avec git
- reproductible et incrémental

La dimension incrémentale du code rend en particulier plus aisé de construire une infrastructure progressivement en la complexifiant au fur et à mesure plutôt que de devoir tout plannifier à l'avance.

Le `playbook` est une sorte de script ansible, c'est à dire du code.
Le nom provient du football américain : il s'agit d'un ensemble de stratégies qu'une équipe a travaillé pour répondre aux situations du match. Elle insiste sur la versatilité de l'outil.

## Syntaxe yaml

Les playbooks ansible sont écrits au format **YAML**.

- YAML est basé sur les identations à base d'espaces (2 espaces par indentation en général). Comme le langage python.
- C'est un format assez lisible et simple à écrire bien que les indentations soient parfois difficiles à lire.
- C'est un format assez flexible avec des types liste et dictionnaires qui peuvent s'imbriquer.
- Le YAML est assez proche du JSON (leur structures arborescentes typées sont isomorphes) mais plus facile à écrire.

A quoi ça ressemble ?

#### Une liste

```yaml
- 1
- Poire
- "Message à caractère informatif"
```

#### Un dictionnaire

```yaml
clé1: valeur1
clé2: valeur2
clé3: 3
```

#### Un exemple imbriqué plus complexe

```yaml
marché: # debut du dictionnaire global "marché"
  lieu: Crimée Curial
  jour: dimanche
  horaire:
    unité: "heure"
    min: 9


    max: 14 # entier
  fruits: #liste de dictionnaires décrivant chaque fruit
    - nom: pomme
      couleur: "verte"
      pesticide: avec #les chaines sont avec ou sans " ou '
            # on peut sauter des lignes dans interrompre la liste ou le dictionnaire en court
    - nom: poires
      couleur: jaune
      pesticide: sans
  légumes: #Liste de 3 éléments
    - courgettes
    - salade

    - potiron
#fin du dictionnaire global
```

Pour mieux visualiser l'imbrication des dictionnaires et des listes en YAML on peut utiliser un convertisseur YAML -> JSON : [https://www.json2yaml.com/](https://www.json2yaml.com/).

Notre marché devient:

```json
{
  "marché": {
    "lieu": "Crimée Curial",
    "jour": "dimanche",
    "horaire": {
      "unité": "heure",
      "min": 9,
      "max": 14
    },
    "fruits": [
      {
        "nom": "pomme",
        "couleur": "verte",
        "pesticide": "avec"
      },
      {
        "nom": "poires",
        "couleur": "jaune",
        "pesticide": "sans"
      }
    ],
    "légumes": [
      "courgettes",
      "salade",
      "potiron"
    ]
  }
}
```

Observez en particulier la syntaxe assez condensée de la liste "fruits" en YAML qui est une liste de dictionnaires.

## Structure d'un playbook

```yaml
--- 
- name: premier play # une liste de play (chaque play commence par un tiret)
  hosts: serveur_web # un premier play
  become: yes
  gather_facts: false # récupérer le dictionnaires d'informations (facts) relatives aux machines

  vars:
    logfile_name: "auth.log"

  var_files:
    - mesvariables.yml

  pre_tasks:
    - name: dynamic variable
      set_fact:
        mavariable: "{{ inventory_hostname + 'prod' }}" #guillemets obligatoires

  roles:
    - flaskapp
    
  tasks:
    - name: installer le serveur nginx
      apt: name=nginx state=present # syntaxe concise proche des commandes ad hoc mais moins lisible

    - name: créer un fichier de log
      file: # syntaxe yaml extensive : conseillée
        path: /var/log/{{ logfile_name }} #guillemets facultatifs
        mode: 755

    - import_tasks: mestaches.yml

  handlers:
    - systemd:
        name: nginx
        state: "reloaded"

- name: un autre play
  hosts: dbservers
  tasks:
    ... 
```

- Un playbook commence par un tiret car il s'agit d'une liste de plays.

- Un play est un dictionnaire yaml qui décrit un ensemble de taches ordonnées en plusieurs sections. Un play commence par préciser sur quelles machines il s'applique puis précise quelques paramètres faculatifs d'exécution comme `become: yes` pour l'élévation de privilège (section `hosts`).

- La section `hosts` est obligatoire. Toutes les autres sections sont **facultatives** !

- La section `tasks` est généralement la section principale car elle décrit les taches de configuration à appliquer.

- La section `tasks` peut être remplacée ou complétée par une section `roles` et des sections `pre_tasks` `post_tasks`

- Les `handlers` sont des tâches conditionnelles qui s'exécutent à la fin (post traitements conditionnels comme le redémarrage d'un service)

### Ordre d'execution

1. `pre_tasks`
2. `roles`
3. `tasks`
4. `post_tasks`
5. `handlers`

Les roles ne sont pas des tâches à proprement parler mais un ensemble de tâches et ressources regroupées dans un module un peu comme une librairie developpement. Cf. cours 3.

### bonnes pratiques de syntaxe

- Indentation de deux espaces.
- Toujours mettre un `name:` qui décrit lors de l'execution la tache en court : un des principes de l'IaC est l'intelligibilité des opérations.
- Utiliser les arguments au format yaml (sur plusieurs lignes) pour la lisibilité, sauf s'il y a peu d'arguments

Pour valider la syntaxe il est possible d'installer et utiliser `ansible-linter` sur les fichiers YAML.

### Imports et includes

Il est possible d'importer le contenu d'autres fichiers dans un playbook:

- `import_tasks`: importe une liste de tâches (atomiques)
- `import_playbook`: importe une liste de play contenus dans un playbook.

Les deux instructions précédentes désignent un import **statique** qui est résolu avant l'exécution.

Au contraire, `include_tasks` permet d'intégrer une liste de tâche **dynamiquement** pendant l'exécution

Par exemple:

```yaml
vars:
  apps:
    - app1
    - app2
    - app3

tasks:
  - include_tasks: install_app.yml
    loop: "{{ apps }}"
```

Ce code indique à Ansible d'executer une série de tâches pour chaque application de la liste. On pourrait remplacer cette liste par une liste dynamique. Comme le nombre d'import ne peut pas facilement être connu à l'avance on **doit** utiliser `include_tasks`.

### Élévation de privilège

L'élévation de privilège est nécessaire lorsqu'on a besoin d'être `root` pour exécuter une commande ou plus généralement qu'on a besoin d'exécuter une commande avec un utilisateur différent de celui utilisé pour la connexion on peut utiliser:

- Au moment de l'exécution l'argument `--become` en ligne de commande avec `ansible`, `ansible-console` ou `ansible-playbook`.
- La section `become: yes`
  - au début du play (après `hosts`) : toutes les tâches seront executée avec cette élévation par défaut.
  - après n'importe quelle tâche : l'élévation concerne uniquement la tâche cible.

- Pour executer une tâche avec un autre utilisateur que root (become simple) ou celui de connexion (sans become) on le précise en ajoutant à `become: yes`, `become_user: username`

<!--  - Par défaut la méthode d'élévation est `become_method: sudo`. Il n'est donc pas besoin de le préciser à moins de vouloir l'expliciter.
`su` est aussi possible ainsi que d'autre méthodes fournies par les "become plugins" exp `runas`). -->

# Variables Ansible

Ansible utilise en arrière plan un dictionnaire contenant de nombreuses variables.

Pour s'en rendre compte on peut lancer : 
`ansible <hote_ou_groupe> -m debug -a "msg={{ hostvars }}"`

Ce dictionnaire contient en particulier:

- des variables de configuration ansible (`ansible_user` par exemple)
- des facts c'est à dire des variables dynamiques caractérisant les systèmes cible (par exemple `ansible_os_family`) et récupéré au lancement d'un playbook.
- des variables personnalisées (de l'utilisateur) que vous définissez avec vos propre nom généralement en **snake_case**.

## Jinja2 et variables dans les playbooks et rôles (fichiers de code)

La plupart des fichiers Ansible (sauf l'inventaire) sont traités avec le moteur de template python JinJa2.

Ce moteur permet de créer des valeurs dynamiques dans le code des playbooks, des roles, et des fichiers de configuration.

- Les variables écrites au format `{{ mavariable }}` sont remplacées par leur valeur provenant du dictionnaire d'exécution d'Ansible.

- Des filtres (fonctions de transformation) permettent de transformer la valeur des variables: exemple : `{{ hostname | default('localhost') }}` (Voir plus bas)

## Jinja2 et les variables dans les fichiers de templates

Les fichiers de templates (.j2) utilisés avec le module template, généralement pour créer des fichiers de configuration peuvent **contenir des variables** et des **filtres** comme les fichier de code (voir au dessus) **mais également** d'autres constructions jinja2 comme:

- Des `if` : `{% if nginx_state == 'present' %}...{% endif %}`.
- Des boucles `for` : `{% for host in groups['appserver'] %}...{% endfor %}`.
- Des inclusions de templates `{% include 'autre_fichier_template.j2' %}`



## Définition des variables

On peut définir et modifier la valeur des variables à différents endroits du code ansible:

- La section `vars:` du playbook.
- Un fichier de variables appelé avec `var_files:`
- L'inventaire : variables pour chaque machine ou pour le groupe.
- Dans des dossier extension de l'inventaire `group_vars`, `host_bars`
- Dans le dossier `defaults` des roles (cf partie sur les roles)
- Dans une tache avec le module `set_facts`.
- A runtime au moment d'appeler la CLI ansible avec `--extra-vars "version=1.23.45 other_variable=foo"`

Lorsque définies plusieurs fois, les variables ont des priorités en fonction de l'endroit de définition.
L'ordre de priorité est plutôt complexe: `https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable`

En résumé la règle peut être exprimée comme suit: les variables de runtime sont prioritaires sur les variables dans un playbook qui sont prioritaires sur les variables de l'inventaire qui sont prioritaires sur les variables par défaut d'un role.

- Bonne pratique: limiter les redéfinitions de variables en cascade (au maximum une valeur par défaut, une valeur contextuelle et une valeur runtime) pour éviter que le playbook soit trop complexe et difficilement compréhensible et donc maintenable.

### Remarques de syntaxe

- `groups.all` et `groups['all']` sont deux syntaxes équivalentes pour désigner les éléments d'un dictionnaire.

### variables spéciales

https://docs.ansible.com/ansible/latest/reference_appendices/special_variables.html

Les plus utiles:

- `hostvars`: dictionaire de toute les variables rangées par hote de l'inventaire.
- `ansible_host`: information utilisée pour la connexion (ip ou domaine).
- `inventory_hostname`: nom de la machine dans l'inventaire.
- `groups`: dictionnaire de tous les groupes avec la liste des machines appartenant à chaque groupe.

Pour explorer chacune de ces variables vous pouvez utiliser le module `debug` en mode adhoc ou dans un playbook:

`ansible <hote_ou_groupe> -m debug -a "msg={{ ansible_host }}"`

ou encore:

`ansible <hote_ou_groupe> -m debug -a "msg={{ groups.all }}"`

### Facts

Les facts sont des valeurs de variables récupérées au début de l'exécution durant l'étape **gather_facts** et qui décrivent l'état courant de chaque machine.

- Par exemple, `ansible_os_family` est un fact/variable décrivant le type d'OS installé sur la machine. Elle n'existe qu'une fois les facts récupérés.

! Lors d'une **commande adhoc** ansible les **facts** ne sont pas récupérés : la variable `ansible_os_family` ne sera pas disponible.

La liste des facts peut être trouvée dans la documentation et dépend des plugins utilisés pour les récupérés: https://docs.ansible.com/ansible/latest/user_guide/playbooks_vars_facts.html


## Structures de controle Ansible (et non JinJa2)

#### La directive `when`

Elle permet de rendre une tâche conditionnelle (une sorte de `if`)

```yaml
- name: start nginx service
  systemd:
    name: nginx
    state: started
  when: ansible_os_family == 'RedHat'
```

Sinon la tache est sautée (skipped) durant l'exécution.

#### La directive `loop:`

Cette directive permet d'executer une tache plusieurs fois basée sur une liste de valeur:

[https://docs.ansible.com/ansible/latest/user_guide/playbooks_loops.html](https://docs.ansible.com/ansible/latest/user_guide/playbooks_loops.html)

exemple:

```yaml
- hosts: localhost
  tasks:
    - name: exemple de boucle
      debug:
        msg: "{{ item }}"
      loop:
        - message1
        - message2
        - message3
```

On peut également controler cette boucle avec quelques paramètres:

```yaml
- hosts: localhost
  vars:
    messages:
      - message1
      - message2
      - message3

  tasks:
    - name: exemple de boucle
      debug:
        msg: "message numero {{ num }} : {{ message }}"
      loop: "{{ messages }}"
      loop_control:
        loop_var: message
        index_var: num
    
```

Cette fonctionnalité de boucle était anciennement accessible avec le mot clé `with_items:` qui est maintenant déprécié.

### Filtres Jinja

Pour transformer la valeur des variables à la volée lors de leur appel on peut utiliser des filtres (jinja2) :

- par exemple on peut fournir une valeur par défaut pour une variable avec filtre default: `{{ hostname | default('localhost') }}` 
- Un autre usage courant des filtres est de reformater et filtrer des listes et dictionnaires de paramètre. Ces syntaxes sont peut intuitives. Vous pouvez vous entrainer en regardant ces tutoriels:
  - [https://www.tailored.cloud/devops/how-to-filter-and-map-lists-in-ansible/](https://www.tailored.cloud/devops/how-to-filter-and-map-lists-in-ansible/)
  - [https://www.tailored.cloud/devops/advanced-list-operations-ansible/](https://www.tailored.cloud/devops/advanced-list-operations-ansible/)

La liste complète des filtres ansible se trouve ici : [https://docs.ansible.com/ansible/latest/user_guide/playbooks_filters.html](https://docs.ansible.com/ansible/latest/user_guide/playbooks_filters.html)

#### Debugger un playbook.

Avec Ansible on dispose d'au moins trois manières de debugger un playbook:

- Rendre la sortie verbeuse (mode debug) avec `-vvv`.

- Utiliser une tache avec le module `debug` : `debug msg="{{ mavariable }}"`.

- Utiliser la directive `debugger: always` ou `on_failed` à ajouter à la fin d'une tâche. L'exécution s'arrête alors après l'exécution de cette tâche et propose un interpreteur de debug.

Les commandes et l'usage du debugger sont décris dans la documentation: https://docs.ansible.com/ansible/latest/user_guide/playbooks_debugger.html
