---
title: 11 - Cours - Helm, le gestionnaire de paquets Kubernetes
draft: false
weight: 2070
---


Nous avons vu que dans Kubernetes la configuration de nos services / applications se fait généralement via de multiples fichiers YAML.

Les kustomizations permettent de rassembler ces descriptions en dossier de code et ont pas mal d'avantages mais on a vite besoin de quelque chose de plus puissant.

- Pour s'adapter à plein de paramétrages différents de notre application
- Pour éviter la répétition de code

C'est donc "trop" déclaratif en quelque sorte, et il faut se concentrer sur les quelques propriétés que l'on souhaite créer ou modifier,

### Helm

Pour pallier ce problème, il existe un utilitaire appelé Helm, qui produit les fichiers de déploiement que l'on souhaite.

Helm est le package manager recommandé par Kubernetes, il utilise les fonctionnalités de templating du langage Go.

Helm permet donc de déployer des applications / stacks complètes en utilisant un système de templating et de dépendances, ce qui permet d’éviter la duplication et d'avoir ainsi une arborescence cohérente pour nos fichiers de configuration.

Mais Helm propose également :

  - la possibilité de mettre les Charts dans un répertoire distant (Git, disque local ou partagé…), et donc de distribuer ces Charts publiquement.
  - un système facilitant les Updates et Rollbacks de vos applications.

Il existe des sortes de *stores* d'applications Kubernetes packagées avec Helm, le plus gros d'entre eux est [Kubeapps Hub](https://hub.kubeapps.com/), maintenu par l'entreprise Bitnami qui fournit de nombreuses Charts assez robustes.

Si vous connaissez Ansible, un chart Helm est un peu l'équivalent d'un rôle Ansible dans l'écosystème Kubernetes.

### Concepts

Les quelques concepts centraux de Helm :

- Un package Kubernetes est appelé **Chart** dans Helm.

- Un Chart contient un lot d’informations nécessaires pour créer une application Kubernetes :
  - la **Config** contient les informations dynamiques concernant la configuration d’une **Chart**
  - Une **Release** est une instance existante sur le cluster, combinée avec une **Config** spécifique.

### Quelques commandes Helm:

Voici quelques commandes de bases pour Helm :

- `helm repo add bitnami https://charts.bitnami.com/bitnami`: ajouter un repo contenant des charts

- `helm search repo bitnami` : rechercher un chart en particulier

- `helm install my-chart` : permet d’installer le chart my-chart. Le nom de release est généré aléatoirement dans votre cluster Kubernetes.

- `helm upgrade my-release my-chart` : permet de mettre à jour notre release avec une nouvelle version.

- `helm ls`: Permet de lister les Charts installés sur votre Cluster

- `helm delete my-release`: Permet de désinstaller la release `my-release` de Kubernetes

### La configuration d'un Chart: des templates d'objets Kubernetes

Visitons un exemple de Chart : [minecraft](https://github.com/helm/charts/tree/master/stable/minecraft/templates)

On constate que Helm rassemble des fichiers de descriptions d'objets k8s avec des variables (moteur de templates de Go) à l'intérieur, ce qui permet de factoriser le code et de gérer puissamment la différence entre les versions.

