---
title: Ansible
weight: 2
pre: "<i class='fab fa-adn'></i> - "
chapter: true
---



### Module 1

# Ansible

Découvrir le couteau suisse de l'automatisation et de l'infrastructure as code.


## Plan
 
### Module 1 : Installer ansible, configurer la connexion et commandes ad hoc ansible

#### Installation
- créer un lab avec LXD
- configurer SSH et python pour utiliser ansible
#### configurer ansible
- /etc ou ansible.cfg
- configuration de la connexion
- connexion SSH et autres plugins de connection
- versions de Python et d'Ansible
#### L'inventaire ansible
- gérer des groupes de machines
- L'inventaire est la source d'information principale pour Ansible
#### Ansible ad-hoc et les modules de base
- la commande `ansible` et ses options
- explorer les nombreux modules d'Ansible
- idempotence des modules
- exécuter correctement des commandes shell avec Ansible
- le check mode pour controller l'état d'une ressource
#### TP1: Installation, configuration et prise en main avec des commandes ad-hoc


### Module 2 : Les playbooks pour déployer une application web

#### syntaxe yaml des playbooks
- structure d'un playbook
#### modules de déploiement et configuration
- Templates de configuration avec Jinja2
- gestion des paquets, utilisateurs et fichiers, etc.
#### Variable et structures de controle
- explorer les variables
- syntaxe jinja des variables et lookups
- facts et variables spéciales
- boucles et conditions
#### Idempotence d'un playbook
- handlers
- contrôler le statut de retour des tâches
- gestion de l'idempotence des commandes Unix
#### debugging de playbook
- verbosite
- directive de debug
- gestion des erreurs à l'exécution
#### TP2: Écriture d'un playbook simple de déploiement d'une application web flask en python.


### Module 3 : Structurer un projet, utiliser les roles

#### Complexifier notre lab en ajoutant de nouvelles machines dans plusieurs groupes.
- modules de provisionning de machines pour Ansible
- organisation des variables de l'inventaire
- la commande ansible-inventory
#### Les roles 
- Ansible Galaxy pour installer des roles.
- Architecture d'un role et bonnes pratiques de gestion des roles.
#### Écrire un role et organiser le projet
- Imports et includes réutiliser du code.
- Bonne pratiques d'organisation d'un projet Ansible
- Utiliser des modules personnalisés et des plugins pour étendre Ansible
- gestion de version du code Ansible
#### TP3: Transformation de notre playbook en role et utilisation de roles ansible galaxy pour déployer une infrastructure multitiers.



### Module 4 : Orchester Ansible dans un contexte de production

#### Intégration d'Ansible
- Intégrer ansible dans le cloud un inventaire dynamique et Terraform
- Différents type d'intégration Ansible
#### Orchestration
- Stratégies : Parallélisme de l'exécution
- Délégation de tâche
- Réalisation d'un rolling upgrade de notre application web grace à Ansible
- Inverser des tâches Ansible - stratégies de rollback
- Exécution personnalisée avec des tags
#### Sécurité
- Ansible Vault : gestion des secrets pour l'infrastructure as code
- desctiver les logs des taches sensibles
- Renforcer le mode de connexion ansible avec un bastion SSH
#### Exécution d'Ansible en production
- Intégration et déploiement avec Gitlab
- Gérer une production Ansible découvrir TOWER/AWX
- Tester ses roles et gérer de multiples versions
#### TP4: Refactoring de notre code pour effectuer un rolling upgrade et déploiement dans le cloud + AWX

