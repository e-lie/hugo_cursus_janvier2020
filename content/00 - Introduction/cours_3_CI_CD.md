---
title: Automatisation DevOps

layout: true
draft: true
---

# {{title}}

## _Cloud et infrastructure as Code_

---

# Types de technologies / mouvements

- ## Cloud

- ## InfraAsCode

- ## Conteneurs (et orchestration)

- CI / CD

---

# Le Cloud

Plutôt que d'**installer manuellement** de nouveaux serveurs linux pour faire tourner des logiciels
on peut utiliser des outils pour faire apparaître de nouveaux serveurs **à la demande**.

Du coup on peut agrandir sans effort l'infrastructure de production pour délivrer une nouvelle version

C'est ce qu'on appelle le IaaS (Infrastructure as a service)

---

# Cloud et API

Dans le cloud, **à la demande** signifie que les vendeurs de cloud fournissent une API (REST généralement) Pour contrôler leur infrastructure.

- Une API est un ensemble de fonctions qu'on peut appeler en codant.
- Une API REST (assez simple et très populaire depuis) est une API qui permet de discuter sur le web avec des informations décrite dans le format JSON.

Exemple pour Scaleway: https://developer.scaleway.com/

---

# Cloud et API

Vous pouvez donc commander la création de machine avec votre langage de programmation favori.

Inconvénients :

- ## La logique de description d'une infrastructure complexe n'est pas facile à programmer du tout. Ceci ne facilite pas l'adoption du cloud de façon vraiment automatisé

- Pour chaque fournisseur de cloud il faut recommencer comme les API sont différentes à chaque fois

---

# Terraform

Un logiciel pratique qui permet de décrire assez simplement des infrastructures dans l'esprit du mouvement Infrastructure as Code

- Fonctionne avec les différents cloud providers

- Est descriptif et incrémental (comme ansible)

- Permet de configurer des infrastructure complexe en exprimant les dépendances entre les ressources

---

# Ansible

## Un logiciel pour configurer des machines

--

- ## On parle de provisionning
- ## Installer des logiciels (apt install)

- ## Modifier des fichier de configuration (vim /etc)

- ## Contrôler les services qui tournent (systemctl...)

- ## Gérer les utilisateurs et les permissions sur les fichiers

- Etc.

---

# Ansible en image

.col-9[![](img/ansible_overview.jpg)]

---

# Instrastructure As Code

## Du code qui contrôle l'état d'un serveur

## Un peu comme un script bash mais:

- ## **Descriptif** : on peut lire facilement l'**état actuel** de l'infra

- ## **Idempotent** : on peut rejouer le playbook **plusieurs fois** pour s'assurer de l'état

- ## Du coup: playbook == état actuel de l'infra

- ## On contrôle ce qui se passe

- Assez différent de l'administration système "adhoc" (= improvisation)

---

# Infrastructure As Code

## Avantages

- ## On peut multiplier les machines (une machine ou 100 machines identiques c'est pareil).

- ## Git ! gérer les version de l'infrastructure et collaborer facilement comme avec du code.

- ## Tests fonctionnels (pour éviter les régressions/bugs)

- Pas de surprise = possibilité d'agrandir les clusters sans soucis !

---

# Prérequis pour utiliser Ansible (minimal)

1.  Pouvoir se connecter en **ssh** sur la machine : **obligatoire** pour démarrer !!!
1.  **Python** disponible sur la machine à configurer : **facultatif** car on peut l'installer avec ansible

---

# Ansible en image

.col-9[![](img/ansible_overview.jpg)]

---

# L'inventaire des machines

- Une liste de toutes les machines
- Classées par catégories
- Avec des variables spécifiques pour leur configuration

```
[nodes]
node1 ansible_host=10.0.2.4 node_number=1
node2 ansible_host=10.0.2.5 node_number=2
node3 ansible_host=10.0.2.7 node_number=3
node4 ansible_host=10.0.2.8 node_number=4

[nodes:vars]
ansible_user=elk-admin
ansible_password=el4stic
ansible_become_password=el4stic%
```

---

# A quoi ça ressemble un playbook

.col-8[.col-10[

```yaml
---
- hosts: serveur_web
  vars:
    - logfile_path: "/var/log/monlog"
  tasks:
    - name: installer le serveur nginx
      apt:
        name: nginx
        state: present
    - name: créer un fichier de log
      file:
        path: { { logfile_path } }
        mode: 755
  handlers:
    - systemd:
        name: nginx
        state: "reloaded"
```

]]

## Quatre parties (ou plus)

--

- ## `hosts:` sur quelle machine on applique la configuration

- ## `vars:` des valeurs pour la configuration

- ## `tasks:` les taches de configuration

- ## `handlers:` des postraitements

- Des **modules python** !
  - _file_, _apt_, _systemd_

---

# TP !

---
