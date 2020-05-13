---
title: Cours 1 - Présentation de Kubernetes
draft: false
---

Kubernetes est la solution dominante d'orchestration de conteneurs développée en Open Source au sein de la Cloud Native Computing Foundation.

## Historique et popularité

![](../../images/kubernetes/k8s_logo.png)

Kubernetes est un orchestrateur développé originellement par Google et basé sur une dizaine d'année d'expérience de déploiement d'application énormes.

La première version est sortie en 2014 et K8S est devenu depuis l'un des projets open source les plus populaire du monde.

Autour de ce projet s'est développée la **Cloud Native Computing Foundation** qui comprend : Google, CoreOS, Mesosphere, Red Hat, Twitter, Huawei, Intel, Cisco, IBM, Docker, Univa, and VMware.

Il s'agit d'une solution **robuste**, **structurante** et **open source**  qui se construit autour des objectifs de:

- Velocité
- Scaling des logiciels et des équipe de développement
- L'abstraction et l'uniformisation des infrastructures


## Architecture de Kubernetes

### Les ressources k8s en bref

![](../../images/docker/kubernetes.png?width=600px)

- Kubernetes a une architecture master/worker (Cf cours 2) composés d'un (control plane) et de noeuds **worker** ou **compute**.
- Les **pods** kubernetes servent à grouper des conteneurs fortement couplés en unité d'application (microservices ou non) 
- Les **deployments** sont une abstraction pour scaler ou mettre à jours des groupes de **pods**.
- Enfin Les **services** sont des groupes de pods (des deployments) exposés à l'extérieur du cluster.

## Points forts de Kubernetes

- Ces derniers tendent à se rapprocher plus d'une VM du point de vue de l'application.
- Hébergeable de façon identique dans le cloud, on-premise ou en mixte.
- Kubernetes a un flat network ([un overlay de plus bas niveau que Swarm](https://neuvector.com/network-security/kubernetes-networking/)) ce qui permet de faire des choses plus puissante facilement comme le multi-DC.

## Comparer Swarm et Kubernetes

#### Swarm

- Swarm plus intégré avec la CLI et le workflow docker.
- Swarm est plus fluide, moins structurant mais moins puissant pour l'automatisation que Kubernetes.
- Swarm groupe les containers entre eux par **stack** mais c'est un groupement assez lâche.
- Kubernetes au contraire créé des **pods**, avec une meilleure cohésion qui sont toujours déployés ensembles puis les scale avec des **deployments** et **services**.
  - => Kubernetes à une meilleure fault tolerance que Swarm
  - un service swarm est un seul conteneur répliqué, un service Kubernetes est un groupes de conteneur (pods) répliqué.
- Kubernetes a plus d'outils intégrés. Il s'agit plus d'un écosystème qui couvre un large panel de cas d'usage.
- Kubernetes peut fonctionner avec autre chose que Docker (Rkt par exemple)
- Swarm a un mauvais monitoring par défaut et le stockage distribué n'est pas intégré de façon standard.
- Dans un contexte on premise, Swarm est beaucoup plus simple à mettre en oeuvre et plus rapide à migrer qu'une stack Kubernetes.


## Solution concurrentes

### Openshift

Est une version de kubernetes configurée et optimisée par RedHat pour être utilisée dans son écosystème.

Avantages:
    - tout intégré donc plus guidé
    - compatible avec k8s

Inconvénients:
    - Un peu captif dans l'écosystème et les services vendus par RedHat

### Apache Mesos

Mesos est une solution plus générale que Kubernetes pour exécuter des applications distribuées. En combinant **Mesos** avec son "application Framework" **Marathon** on obtient une solution équivalente sur de nombreux point à kubernetes.

Elle est cependant moins standard :
    - Moins de ressources disponibles pour apprendre, intégrer avec d'autre solution etc.
    - Peu vendu en tant que service par les principaux cloud provider.
    - Plus chère à mettre en oeuvre.

Comparaison d'architecture: [mesos VS kubernetes](https://www.baeldung.com/mesos-kubernetes-comparison)
