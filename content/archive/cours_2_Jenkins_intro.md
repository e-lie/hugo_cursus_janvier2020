---
title: Cours 2 - Jenkins Intro
draft: false
---


## Couteau suisse d'automatisation



### Un serveur d'automatisation

- Permet de lancer depuis un serveur central différent type d'opérations
  - tester un logiciel
  - construire une image docker
  - provisionner un infrastructure
  - n'importe quoi de scriptable

- De fait Jenkins est devenu Un outil très populaire de CI/CD: il permet d'automatiser la plupart des étapes du processus CI/CD de façon flexible.



### Jobs et pipelines

Jenkins permet donc d'automatiser n'importe quelle tâche sur forme d'un **job** ou tache ponctuelle.

  - planifié ou non
  - récurrent ou non

Mais la plupart des jobs (dans le cadre de la CI/CD) sont des **pipelines**

Un pipeline est une suite d'étapes appelées stages conditionnées les unes aux autres (qui peuvent rater ou réussir).


### Jenkins les pipelines de CI/CD

- Dans une entreprise avec une équipe de développement, Jenkins va être utilisé pour automatiser la plupart des tâches d'intégration continue:
  - Lancer la construction des artefacts logiciels à chaque push et les mettre sur un serveur spécial
  - Lancer les tests sur les artefacts : déclenchés à chaque push pour les tests unitaires et plus rarement pour les tests plus longs.
  - Scanner la qualité du code et bloquer les régressions logicielles.
  - Provisionner des infrastructures (Ansible, plus rare).
  - Déployer des conteneurs sur ces infrastructures "orchestrées".


## Jenkins

- agnostique du langage: Jenkins a beaucoup de plugins qui supportent la plupart des langages et frameworks.

- Open source et extensible par des plugins: Jenkins a une grosse communauté et beaucoup de plugins.

- Portable: Jenkins est écrit Java donc il peut tourner sur la plupart des systèmes.

- Supporte la plupart des gestionnaires de version et systèmes de build.

- Distribué et scalable: Jenkins inclus un mode master/slave, qui permet de distribuer son exécution sur plusieurs serveurs.

- Simplicité: Jenkins est simple seulement pour faire des trucs simples.

- Orienté Code:  Les pipelines Jenkins peuvent être définis "as code" et Jenkins lui même peut se configurer en XML ou Groovy. (cf TP2)


### Des plugins pour tout faire

- Jenkins est très limité sans plugins.
- Chaque fonctionnalité, même les plus centrales comme les pipelines ou les noeuds dockers sont basées sur des plugins.

### Installation de Jenkins

Jenkins s'installe généralement sur un serveur dédié:

- Soit en java directement (historiquement). Comme Java est portable, Jenkins est un logiciel très portable

- Soit à l'aide d'un conteneur docker (Avantage pour avoir de multiple Jenkins masters, on peut alors utiliser un système d'orchestration comme Kubernetes)

### Une architecture distribuée et/ou haute disponibilité

- Jenkins fonctionne soit sur un seul serveur soit avec une architecture master/runner:
  - Le master centralise et affiche les informations sur les builds.
  - Les runners se répartissent les builds lancés depuis le master à partir de divers critères (compatibilité, taille, type)

- Idéalement il faut également dupliquer le noeud master pour fournir une haute disponibilité garantie: si l'un des master cesse de fonctionner pour une quelconque raison le deuxième récupère toute la charge. Le service n'est pas interrompu.

### Un pipeline complet classique

- **Build/Compile** (facultatif: seulement pour les langages compilés comme Java)
- **Unit test** (Lance les tests de chaque fonctions de notre application)
- **Docker / Artefact build**: construit l'application pour produire un **artefact** : **jar** ou **image docker** ou autre.
- **Publish** (sur un serveur d'artefacts): Docker Hub, Artifactory, Votre serveur privé d'entreprise.
- **Staging Deploy**: Installer l'application dans un environnement de validation.
- **Acceptance testing** : Faire des tests fonctionnels dans l'environnement Staging.


