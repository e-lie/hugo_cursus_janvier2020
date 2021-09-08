---
title: Cours 2 - Déploiement Kubernetes paramètres et environnements
draft: true
---


## Déploiement classique (révision)

- deployments, services, ingress
- statefulsets, services, volumes and claims, ingress

## Ranger les choses par environnement dans Kubernetes

On voudrait isoler nos différentes installation de notre application. On créé pour cela des environnements, typiquement `prod` et `dev` dans Kubernetes.

Il y a deux principales façon de faire cela:
### Utiliser plusieurs namespaces

L'isolation des namespaces permet 3 choses :

- ne voir que ce qui concerne une tâche particulière (ne réfléchir que sur une seule chose lorsqu'on opère sur un cluster)
- créer des limites de ressources (CPU, RAM, etc.) pour le namespace
- définir des rôles et permissions sur le namespace qui s'appliquent à toutes les ressources à l'intérieur.

Cela permet de garantir en terme de sécurité et de resources que chaque namespace ne viendra pas déranger son voisin.

On peut donc créer un namespace pour la `prod` et un namespace `dev` pour les différentes releases liées au dev.

### Utiliser plusieurs clusters

L'isolation en terme de namespace est parfois insuffisante ou complexe en terme de sécurité. On privilégie alors la création d'un cluster par environnement

Cela qui rend également le risque de chute d'un cluster moins élevé (moins couteux).

Par contre cela demande un peu plus de travail initial et de maintenance.

## Problème: comment gérer le déploiement de plusieurs versions d'un application à partir du même code k8s ?


Deux principales solutions s'offrent à nous pour éviter de répéter le même code pour chaque environnement:

- Écrire notre propre chart Helm. Helm utilise des templates un peu comme ansible sur des fichiers resources k8s.
- Utiliser le mode `kustomize` de `kubectl` pour 

Helm est :

- plus puissant est flexible
- plus complexe
- nécessite un dépôt de chart helm comme `chartmuseum` en plus du repository docker

Kustomize est :

- disponible dans kubectl
- plus rigide
- plus adapté lorsque les modifications ne sont pas trop importantes comme pour nous (peu de usecases différents)