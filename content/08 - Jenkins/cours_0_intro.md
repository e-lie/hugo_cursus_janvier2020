---
title: Cours 0 - Intro - Pourquoi Jenkins et Kubernetes
draft: false
---



## Vous avez dis DevOps ? (43e version)

Le DevOps est un mouvement de transformation de l'informatique: découpler le déploiement du logiciel des détails d'administration système pour pouvoir déployer tout le temps, et suivre un rythme de développement agile.

- **Le** critère exigent pour savoir si vous êtes dans une équipe qui respecte la philosophie DevOps c'est de savoir si le logiciel est "**déployé en continu automatiquement**", ce qu'on appelle **Continuous Deployment**.

## Retour rapide sur la CI/CD




## Pourquoi Jenkins et Kubernetes

- Jenkins est le serveur d'automatisation **de référence** depuis des années car:
    - populaire déjà avant le DevOps
    - open source
    - ... donc pas de licence pour l'héberger on premise
    - hyper flexible car basé intégralement sur des plugins
    - ... qui suivent en permanence les nouveautés de l'automatisation logicielle

- Inconvénients:
    - un peu le bordel à apprendre à cause des multiples plugins
    - plus complexe et brut que d'autre solutions de CI/CD récentes

- Kubernetes est le PAAS (plateforme as a service) open source **de référence**
    - permet de déployer des applications de façon déclarative et "as code"
    - self healing pour beaucoup d'aspect des applications (plus de fiabilité)
    - permet de déployer des applications à grande échelle et en haute disponibilité plus facilement
    - un grosse partie de l'industrie est en train d'aller vers cette technologie$

- Inconvénients:
    - complexe à apprendre et utiliser.
    - très complexe à héberger on premise.
    - assez couteux à louer.
    - fait apparaitre une nouvelle gamme de problèmes (d'orchestration et d'application distribuée) qui nécessitent de nouvelles compétences (ce n'est pas vraiment un inconvénient de la techno plus du paradigme du cloud)


Ces deux technologies fonctionnent bien ensemble car Jenkins s'est adapté au monde Kubernetes avec ses plugins. Parenthèse : Jenkins X est complètement différent Jenkins en fait ! ne pas les confondre.

## Notre parcours pour ce module

1. préparer kubernetes (cert-manager & repository d'images privé)
1. Tester une application flask (test unitaires et tests fonctionnels)
1. Déployer notre application dans kubernetes dans différents environnements
1. Simuler notre pipeline étape par étape sans Jenkins et anticiper les besoins
1. Créer un pipeline as code Jenkins en reprenant les étapes précédentes
1. Intégrer ce pipeline directement dans le dépôt avec un Jenkinsfile et un multibranch pipeline
1. Tester notre CI/CD en ajoutant une fonctionnalité à notre application