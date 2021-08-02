---
title: 'Cours 4 - Ansible en production, sécurité et Cloud'
draft: false
weight: 13
---


## Execution d'Ansible en production

L'intérêt d'un outil d'installation idempotent comme Ansible est de pouvoir exécuter de façon régulière et automatiser l'execution d'Ansible pour s'assurer de la conformité de l'infrastructure avec le code.

Une production Ansible est généralement un serveur spécial (parfois appelé un ansible master) depuis lequel le code peut être exécuté, ponctuellement ou de préférence régulièrement (2x par jours par exemple).

Le serveur Ansible s'assure également que les exécutions sont correctement logguées et que les DevOps peuvent par la suite s'assurer que les différentes exécutions se sont déroulées correctement et éventuellement lire les logs d'execution pour diagnostiquer les erreurs.

## Différentes solutions de serveur de production Ansible

- Ansible Tower/AWX : La solution "officielle" pour exécuter ansible en production promue par RedHat. AWX est l'upstream open source de Tower. Cette solution est assez lourde à déployer et n'exécute que du Ansible (peu versatile) mais elle a été prouvé adapté pour des très grosses production pilotées principalement par Ansible.

- Un serveur master Linux simple pour executer Ansible en CLI ou en Cron : plus léger et versatile mais ne propose par de dashboard pour afficher l'état de de l'infrastructure

- Rundeck: une solution générique pour exécuter des Jobs d'infrastructure qui s'intègre plutôt correctement avec Ansible.

- Jenkins: souvent associé à la CI/CD, Jenkins est en réalité un serveur générique pour exécuter des Jobs automatiquement et à la demande. Il propose un plugin Ansible intéssant et permet de consulté les logs d'exécution et d'avoir une vue globale des dernières exécutions à travers des dashboard. Il est très flexible mais assez complexe à configurer correctement.

Nous allons pour le dernier TP de ce module utiliser Jenkins pour exécuter Ansible. Ainsi nous pouvons découvrir un peu en avance 
 Jenkins qui est complexe et important pour la fin du cursus.


# Sécurité

Les problématiques de sécurité linux ne sont pas résolue magiquement par Ansible. Tous le travail de réflexion et de sécurisation reste identique mais peut comme le reste être mieux controllé grace à l'approche déclarative de l'infrastructure as code.

Si cette problématique des liens entre Ansible et sécurité vous intéresse : `Security automation with Ansible`

Il est à noter tout de même qu'Ansible est généralement apprécié d'un point de vue sécurité car il n'augmente pas (vraiment) la surface d'attaque de vos infrastructure : il est basé sur ssh qui est éprouvé et ne nécessite généralement pas de réorganisation des infrastructures.

Pour les cas plus spécifiques et si vous voulez éviter ssh, Ansible est relativement agnostique du mode de connexion grâce aux plugins de connexions (voir ci-dessous).

## Authentification et SSH

Un bonne pratique importante : changez le port de connexion ssh pour un port atypique. Ajoutez la variable `ansible_ssh_port=17728` dans l'inventaire.

Il faut idéalement éviter de créer un seul compte ansible de connexion pour toutes les machines:
- difficile à bouger
- responsabilité des connexions pas auditable (auth.log + syslog)

Il faut utiliser comme nous avons fait dans les TP des logins ssh avec les utilisateurs humain réels des machines et des clés ssh. C'est à dire le même modèle d'authentification que l'administration traditionnelle.

## Les autres modes de connexion

Le mode de connexion par défaut de Ansible est SSH cependant il est possible d'utiliser de nombreux autres modes de connexion spécifiques :

- Pour afficher la liste des plugins  disponible lancez `ansible-doc -t connection -l`.

- Une autre connexion courante est `ansible_connection=local` qui permet de configurer la machine locale sans avoir besoin d'installer un serveur ssh.

- Citons également les connexions `ansible_connexion=docker` et `ansible_connexion=lxd` pour configurer des conteneurs linux ainsi que `ansible_connexion=` pour les serveurs windows

- Les questions de sécurités de la connexion se posent bien sur différemment selon le mode de connexion utilisés (port, authentification, etc.)

- Pour débugger les connexions et diagnotiquer leur sécurité on peut afficher les détails de chaque connection ansible avec le mode de verbosité maximal (network) en utilisant le paramètre `-vvvv`.

## Variables et secrets

Le principal risque de sécurité lié à Ansible comme avec Docker et l'IaC en général consiste à laisser trainer des secrets (mot de passe, identités de clients, api token, secret de chiffrement / migration etc.) dans le code ou sur les serveurs (moins problématique).

Attention : les dépôt git peuvent cacher des secrets dans leur historique. Pour chercher et nettoyer un secret dans un dépôt l'outil le plus courant est BFG : https://rtyley.github.io/bfg-repo-cleaner/

## Désactiver le logging des informations sensibles

Ansible propose une directive `no_log: yes` qui permet de désactiver l'affichage des valeurs d'entrée et de sortie d'une tâche.

Il est ainsi possible de limiter la prolifération de données sensibles.

## Ansible vault

Pour éviter de divulguer des secrets par inadvertance, il est possible de gérer les secrets avec des variables d'environnement ou avec un fichier variable externe au projet qui échappera au versionning git, mais ce n'est pas idéal.

Ansible intègre un trousseau de secret appelé , **Ansible Vault** permet de chiffrer des valeurs **variables par variables** ou des **fichiers complets**.
Les valeurs stockées dans le trousseaux sont déchiffrée à l'exécution après dévérouillage du trousseau. 

- `ansible-vault create /var/secrets.yml`
- `ansible-vault edit /var/secrets.yml` ouvre `$EDITOR` pour changer le fichier de variables.
- `ansible-vault encrypt_file /vars/secrets.yml` pour chiffrer un fichier existant
- `ansible-vault encrypt_string monmotdepasse` permet de chiffrer une valeur avec un mot de passe. le résultat peut être ensuite collé dans un fichier de variables par ailleurs en clair.

Pour déchiffrer il est ensuite nécessaire d'ajouter l'option `--ask-vault-pass` au moment de l'exécution de `ansible` ou `ansible-playbook`

Il existe également un mode pour gérer plusieurs mots de passe associés à des identifiants.

## Ansible dans le cloud

L'automatisation Ansible fait d'autant plus sens dans un environnement d'infrastructures dynamique:

- L'agrandissement horizontal implique de résinstaller régulièrement des machines identiques
- L'automatisation et la gestion des configurations permet de mieux contrôler des environnements de plus en plus complexes.

Il existe de nombreuses solutions pour intégrer Ansible avec les principaux providers de cloud (modules ansible, plugins d'API, intégration avec d'autre outils d'IaC Cloud comme Terraform ou Cloudformation).

## Inventaires dynamiques

Les inventaires que nous avons utilisés jusqu'ici implique d'affecter à la main les adresses IP des différents noeuds de notre infrastructure. Cela devient vite ingérable.

La solution ansible pour le pas gérer les IP et les groupes à la main est appelée `inventaire dynamique` ou `inventory plugin`. Un inventaire dynamique est simplement un programme qui renvoie un JSON respectant le format d'inventaire JSON ansible, généralement en contactant l'api du cloud provider ou une autre source.

```
$ ./inventory_terraform.py
{
  "_meta": {
    "hostvars": {
      "balancer0": {
        "ansible_host": "104.248.194.100"
      },
      "balancer1": {
        "ansible_host": "104.248.204.222"
      },
      "awx0": {
        "ansible_host": "104.248.204.202"
      },
      "appserver0": {
        "ansible_host": "104.248.202.47"
      }
    }
  },
  "all": {
    "children": [],
    "hosts": [
      "appserver0",
      "awx0",
      "balancer0",
      "balancer1"
    ],
    "vars": {}
  },
  "appservers": {
    "children": [],
    "hosts": [
      "balancer0",
      "balancer1"
    ],
    "vars": {}
  },
  "awxnodes": {
    "children": [],
    "hosts": [
      "awx0"
    ],
    "vars": {}
  },
  "balancers": {
    "children": [],
    "hosts": [
      "appserver0"
    ],
    "vars": {}
  }
}%  
```

On peut ensuite appeler `ansible-playbook` en utilisant ce programme plutôt qu'un fichier statique d'inventaire: `ansible-playbook -i inventory_terraform.py configuration.yml`
