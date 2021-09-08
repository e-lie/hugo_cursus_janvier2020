---
title: Introduction DevOps
weight: 3
---

## A propos de moi

Élie Gavoty

- Developpeur backend et DevOps (Sewan Group / Yunohost)
- Formateur DevOps, Linux, Python
- Philosophie de la technique

## A propos de vous

- Attentes ?
- Début du cursus : 
  - Est-ce que ça vous plait ?
  - Quels modules avez vous déjà fait ?

## Le mouvement DevOps

Le DevOps est avant tout le nom d'un mouvement de transformation professionnelle et technique de l'informatique.

Ce mouvement se structure autour des solutions **humaines** (organisation de l'entreprise et des équipes) et **techniques** (nouvelles technologies de rupture) apportées pour répondre aux défis que sont:

- L'agrandissement rapide face à la demande des services logiciels et infrastructures les supportant.
- La célérité de déploiement demandée par le développement agile (cycles journaliers de développement).
- Difficultées à organiser des équipes hétérogènes de grande taille et qui s'agrandissent très vite selon le modèle des startups.et

Il y a de nombreuses versions de ce que qui caractérise le DevOps mais pour résumer:

Du côté humain:

  - Application des process de management agile aux opérations et la gestion des infrastructures (pour les synchroniser avec le développement).
  - Remplacement des procédés d'opérations humaines complexes et spécifiques par des opérations automatiques et mieux standardisées.
  - Réconciliation de deux cultures divergentes (Dev et Ops) rapprochant en pratique les deux métiers du développeur et de l'administrateur système.

Du côté technique:

  - L'intégration et le déploiement continus des logiciels/produits.
  - L'infrastructure as code: gestion sous forme de code de l'état des infrastructures d'une façon le plus possible déclarative.
  - Les conteneurs (Docker surtout mais aussi Rkt et LXC/LXD): plus léger que la virtualisation = permet d'isoler chaque service dans son "OS" virtuel sans dupliquer le noyau.
  - Le cloud (Infra as a service, Plateforme as a Service, Software as a service) permet de fluidifier l'informatique en alignant chaque niveau d'abstraction d'une pile logicielle avec sa structuration économique sous forme de service.

### L'agilité en informatique

- Traditionnellement la qualité logicielle provient :
  - d'une conception détaillée en amont = création d'un spécification détaillée
  - d'un contrôle de qualité humain avant chaque livraison logicielle basé sur une processus = vérification du logiciel par rapport à la spécification

- Problèmes historiques posé par trop de spécification et validation humaine :
  - Lenteur de livraison du logiciel (une version par an ?) donc aussi difficulté de fixer les bugs et problèmes de sécurité a temps
  - Le Travail du développeur est dominé par des `process` formels : ennuyeux et abstrait
  - difficulté commerciale : comment répondre à la concurence s'il faut 3 ans pour lancer un produit logiciel.

##### Solution : développer de façon agile c'est à dire **itérative**

- Sortir une version par semaine voir par jour
- Créer de petites évolution plutôt que de grosses évolution
- Confronter en permanence le logiciel aux retours clients et utilisateurs

Mais l'agilité traditionnelle ne concerne pas l'administration système.

### La motivation au coeur du DevOps : La célérité

- La célérité est : la rapidité (itérative) non pas seulement dans le développement du logiciel mais plus largement dans la livraison du service au client:

Exemple : Netflix ou Spotify ou Facebook etc. déploient une nouvelle version mineure de leur logiciel par jour.

- Lorsque la concurrence peut déployer des innovations en continu il devient central de pouvoir le faire.

### Le problème que cherche à résoudre le DevOps

La célérité et l'agrandissementest sont incompatibles avec une administration système traditionnelle:

Dans un DSI (département de service informatique) on organise ces activités d'admin sys en opérations:

- On a un planning d'opération avec les priorités du moment et les trucs moins urgents
- On prépare chaque opération au minimum quelques jours à l'avance.
- On suit un protocole pour pas oublier des étapes de l'opération (pas oublier de faire une sauvegarde avant par exemple)

La difficulté principale pour les Ops c'est qu'un système informatique est:

- Un système très complexe qu'il est quasi **impossible de complètement visualiser** dans sa tête.
- Les **évènements** qui se passe sur la machines sont **instantanés** et **invisibles**
- L'**état actuel** de la machine n'est **pas ou peu explicite** (combien d'utilisateur, machine pas connectée au réseau par exemple.)
- Les **interractions entre des problèmes** peu graves peuvent entrainer des erreurs critiques en cascades.

On peut donc constater que les opérations traditionnelles implique une culture de la **prudence**

- On s'organise à l'avance.
- On vérifie plusieurs fois chaque chose.
- On ne fait pas confiance au code que nous donnent les développeurs.
- On suit des procédures pour limiter les risques.
- On surveille l'état du système (on parle de monitoring)
- Et on reçoit même des SMS la nuit si ya un problème :S

#### Bilan

Les opérations "traditionnelles":

- Peuvent pas aller trop vite car il faut marcher sur des oeufs.
- Les Ops veulent pas déployer de nouvelles versions **trop souvent** car ça fait plein de boulot et ils prennent des risques (bugs / incompatilibités).
- Quand c'est **mal organisé** ou qu'on va **trop vite** il y a des **catastrophes** possibles.

### L'objectif technique idéal du DevOps : Intégration et déploiement continus (CI/CD)

Du côté des développeurs avec l'agilité on a déjà depuis des années une façon d'automatiser pleins d'opérations sur le code à chaque fois qu'on valide une modification.

- Chaque modification du code est validée dans le gestionnaire de version **Git**.
- Ensuite est envoyée sur le dépot de code commun.
- Des tests logiciels se lancent automatiquement pour s'assurer qu'il n'y a pas de bugs ou de failles.
- Le développeurs est averti des problèmes.

C'est ce qu'on appelle l'intégration continue.

Le principe central du DevOps est d'automatiser également les opérations de déploiement et de maintenance en se basant sur le même modèle.

![](../../images/devops.png)
![](../../images/continuous_delivery.png)

Mais pour que ça fonctionne il faut résoudre des défi techniques nouveau => innovations

## Les innovations techniques du DevOps

### Le Cloud

Le cloud techniquement c'est l'ensemble des trois :

- Infrastructure as a Service (IaaS): on commande du linux, du réseau et des loadbalancer etc. à la demande

Exemple: Amazon Web Services, DigitalOcean, Azure etc

- Plateforme as a Service (PaaS): on commande directement un environnement PHP ou NodeJS pour notre application

Exemple: heroku, netlify, 

- Software as a service (SaaS): des services web à la demande pour des utilisateurs finaux

Exemple: Netflix plutôt que VLC, Spotify vs Itunes, etc.

On peut dire que chaque couche (d'abstraction) de l'informatique est commandable à la demande.

Nous utiliserons surtout l'IaaS avec DigitalOcean dans le module Docker.

### Les conteneurs (Docker et Kubernetes)

Faire des boîtes isolées avec nos logiciels:

- Un façon standard de packager un logiciel
- Cela permet d'assembler de grosses applications comme des legos
- Cela réduit la complexité grâce:
  - à l'intégration de toutes les dépendance déjà dans la boîte
  - au principe d'immutabilité qui implique de jeter les boîtes ( automatiser pour lutter contre la culture prudence). Rend l'infra prédictible.

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


## Notre programme

- Docker : les conteneurs et l'infra as code
- Ansible : couteau suisse de l'infra as code
- Kubernetes : infrastructure de conteneurs (iac et cloud)
- Jenkins : CI/CD pour intégrer ensemble le dev et les opérations


## Aller plus loin

- La DevOps roadmap: [https://github.com/kamranahmedse/developer-roadmap#devops-roadmap](https://github.com/kamranahmedse/developer-roadmap#devops-roadmap)

