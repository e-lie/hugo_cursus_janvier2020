---
title: Introduction DevOps
---



### Le mouvement DevOps

Le DevOps est avant tout le nom d'un mouvement de transformation professionnelle et technique de l'informatique.

Ce mouvement se structure autour des solutions **humaines** (organisation de l'entreprise et des équipes) et **techniques** (nouvelles technologies de rupture) apportées pour répondre aux défis que sont:

- L'agrandissement rapide face à la demande des services logiciels et infrastructures les supportant.
- La célérité de déploiement demandée par le développement agile (cycles journaliers de développement).
- Difficultées à organiser des équipes hétérogènes de grande taille et qui s'agrandissent très vite selon le modèle des startups.et

Il y a de nombreuses versions de ce que qui caractérise le DevOps mais pour résumer:

Du côté humain:

  - Application des process de management agile aux opérations et la gestion des infrastructures (pour les synchroniser avec le développement).
  - Réconciliation de deux cultures divergentes (Dev et Ops) rapprochant en pratique les deux métiers du développeur et de l'administrateur système.

Du côté technique:

  - Les conteneurs (Docker surtout mais aussi Rkt et LXC/LXD): plus léger que la virtualisation = permet d'isoler chaque service dans son "OS" virtuel sans dupliquer le noyau.
  - Le cloud (Infra as a service, Plateforme as a Service, Software as a service) permet de fluidifier l'informatique en alignant chaque niveau d'abstraction d'une pile logicielle avec sa structuration économique sous forme de service.
  - L'intégration et le déploiement continus des logiciels/produits.
  - L'infrastructure as code: gestion sous forme de code de l'état des infrastructures d'une façon le plus possible déclarative.


### L'infrastructure as code (IaC)

Il s'agit comme son nom l'indique de gérer les infrastructures en tant que code c'est-à-dire des fichiers textes avec une logique algorithmique/de données et suivis grâce à un gestionnaire de version (git).

Le problème identifié que cherche a résoudre l'IaC est un écheveau de difficulées pratiques rencontrée dans l'administration système traditionnelle:

1. Connaissance limité de l'état courant d'un système lorsqu'on fait de l'**administration ad-hoc** (manuelle avec des commandes unix/dos).
  - Dérive progressive de l'état des systèmes et difficultés à documenter leur états.
  - Fiabilité limitée et risques peu maîtrisés lors de certaines opérations transversales (si d'autres méchanismes de fiabilisation n'ont pas été mis en place).
  - Problème de communication dans les grandes équipes car l'information est détenue implicitement par quelques personnes.

2. Faible reproductibilité des systèmes et donc difficultée/lenteur du passage à l'échelle (horizontal scaling).
  - Multiplier les serveurs identiques est difficile si leur état est le résultat d'un processus manuel partiellement documenté.
  - Difficulté à reproduire/simuler l'état précis de l'infrastructure de production dans les contextes de tests logiciels.

3. Difficultés du travail collaboratif dans de grandes équipes avec plusieurs culture (Dev vs Ops) lorsque les rythmes et les modes de travail diffèrent
  - L'IaC permet de tout gérer avec git et des commits.
  - L'IaC permet aux Ops qui ne le faisait pas de se mettre au code et aux développeur de se confronter plus facilement.
  - L'IaC permet d'accélérer la transformation des infrastructures pour l'aligner sur la livraison logicielle quotidienne (idéalement ;) )

