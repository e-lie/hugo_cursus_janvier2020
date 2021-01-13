---
title: Cours 5 - Helm, le gestionnaire de paquets Kubernetes
draft: false
---

Nous avons vu que dans Kubernetes la configuration de nos services / applications se fait généralement via de multiples fichiers YAML.

Quand on a une seule application cela reste gérable mais dès qu’on a plusieurs environnements, applications et services, on se retrouve vite submergé de fichiers de centaines, voire de milliers, de lignes qui sont, de plus, assez semblables. 
C'est donc "trop" déclaratif, et il faut se concentrer sur les quelques propriétés que l'on souhaite créer ou modifier,


Pour pallier ce problème, il existe l'utilitaire Helm, qui produit les fichiers de déploiement que l'on souhaite.

Helm est le package manager recommandé par Kubernetes, c'est aussi le seul sur le marché.
<!-- - car son unique concurrent, KPM de CoreOS, n’est plus maintenu depuis juillet 2017. -->

Helm permet donc de déployer des applications / stacks complètes en utilisant un système de templating et de dépendances, ce qui permet d’éviter la duplication et d'avoir ainsi une arborescence cohérente pour nos fichiers de configuration.

Mais Helm propose également :

  - la possibilité de mettre les Charts dans un répertoire distant (Git, disque local ou partagé…), et donc de distribuer ces Charts publiquement.
  - un système facilitant les Updates et Rollbacks de vos applications.

Il existe des sortes de *stores* d'applications Kubernetes packagées avec Helm, le plus gros d'entre eux est : [Kubeapps Hub](https://hub.kubeapps.com/)

<!-- 
Nous verrons qu'un chart helm est un peu l'équivalent d'un role Ansible dans l'écosystème Kubernetes. -->


### Concepts

Les quelques concepts centraux de Helm :

- Un package Kubernetes est appelé **Chart** dans Helm.

- Une Chart contient un lot d’informations nécessaires pour créer une application Kubernetes :
  - la **Config** contient les informations dynamiques concernant la configuration d’une **Chart**
  - Une **Release** est une instance existante sur le cluster, combinée avec une **Config** spécifique.


### Architecture client-serveur de Helm

Helm désigne une application client en ligne de commande.

Pour fonctionner sur le cluster, Helm a besoin d'installer un gestionnaire appelé Tiller : c'est le serveur qui communique avec le client Helm et l’API de Kubernetes pour gérer vos déploiements.

Lors de l’initialisation de Helm, le client installe Tiller sur un pod du cluster.

Helm utilise automatiquement votre fichier `kubeconfig` pour se connecter.

### Quelques commandes Helm:

Voici quelques commandes de bases pour Helm :

- `helm install my-chart` : permet d’installer le chart my-chart. Le nom de release est généré aléatoirement dans votre cluster Kubernetes.

- `helm upgrade my-release my-chart` : permet de mettre à jour notre release avec une nouvelle version.

- `helm ls`: Permet de lister les Charts installés sur votre Cluster

- `helm delete my-release`: Permet de désinstaller la release `my-release` de Kubernetes

### La configuration d'un Chart: des templates jinja d'objets Kubernetes

Visitons un exemple de Chart : [minecraft](https://github.com/helm/charts/tree/master/stable/minecraft/templates)

On constate que Helm rassemble des fichiers de descriptions d'objets k8s avec des variables (Jinja) à l'intérieur, ce qui permet de factoriser le code et de gérer puissamment la différence entre les versions.

