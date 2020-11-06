---
title: 'TODO Ansible Avancé'
draft: true
---

- Faire une version publique du lab !!

- TP usage d'AWX partie 1
  - installation dans k8s avec Ansible et serveur postgresql externe
  - création d'une équipe de 2 en plus du superadmin
  - création d'un projet à partir github en SSH
  - lancement d'un playbook de ping accessible à tous
  - 

- TP qualité de code et fiabilité des Roles
  - refactorer notre role pour le tester avec molecule
  - chercher des bonnes pratiques dans d'autres roles et les ajouter au fur et à mesure à notre role
    - écrire une role multi os et le tester
    - validation des paramètres au début du role avec Assert et ajouter les tests pour confirmer
    - personnaliser la sortie
    - tester si une dépendance est présente avant de lancer et vérifier dans molecule que le role ne fonctionne pas sans  

- TP écriture d'un plugin d'inventaire
  - plugin python qui tape sur une API REST
  - l'intégrer dans un role
    - pour l'installation
    - pour le testing avec molecule

- TP usage d'AWX partie 2
  - Différents type de jobs Ansible avec des exemples
    - assurer la configuration de base (toutes les heures ?)
    - rolling upgrade (orchestré)
    - check des services avec alerting (sur mattermost)
    - repair ?
    - provisionning d'un nouvel environnement
  - Workflows
  - Explorons les conteneurs d'exécution de tâches
  - Quoi backuper ? bdd lol, fichiers d'environnement (regarder les volumes persistants)