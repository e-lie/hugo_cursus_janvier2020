---
title: "TP fil rouge DevOps"
weight: 350
# pre: "<i class='fab fa-git'></i> - "
pre: "<i class='fas fa-infinity'></i> - "
# chapter: true
draft: true
---

## Introduction

L'objectif de ce TP est de faire la démonstration pratique de la plupart des éléments techniques appris durant le cursus DevOps.

L'activité de DevOps dans une équipe est une activité de support au développement et d'automatisation des divers éléments pratiques nécessaire au bon fonctionnement d'une application. Elle est par nature intégrative.

Ce TP consiste donc logiquement à rassembler les aspects pratiques (éléments vus en TP) découverts dans les modules du cursus et de les combiner autour.

## Rendu

Le rendu du TP est à effectuer par groupe. Pour chaque groupe les éléments suivant devront être présentés lors de la présentation finale du cursus:

- Une installation fonctionnelle de l'infrastructure et de l'application du TP installé sur cette infrastructure telle que décrite dans l'énoncé suivant.

- Deux dépots de code sur Github contenant pour le premier le code d'infrastructure et pour le second l'application à déployer sur l'infrastructure.

- Une présentation succinte décrivant les différents élements du rendu et leurs objectifs ainsi que les choix réalisés lors de la réalisation.

## 1 - Application

Créer une application web python et l'installer sur Linux.

Installer une application minimale en mode production utilisant un service systemd uwsgi et nginx.

-> Inspirez vous du Dockerfile de l'application

#### Idées de bonus

Installer l'application flask microblog avec une base de donnée MySQL (mode développement flask et production uwsgi+nginx)

## 2 - Git

Versionner le code de l'application précédente sur Github.

- Un membre du groupe créé le dépôt et ajoute ses collègues à l'application en leur donnant le status de `maintainer`.
- Pousser le code avec une branche `development`, une branche `main` (production).
- Chaque membre crée une branche à son nom et s'efforce de ne pousse plus sur `development` ou `main` dans le futur.

Répétez les étapes précédentes en créant un dépôt pour le code d'infrastructure.

#### Idées de bonus

- Écrire à l'avance (au fur et a mesure pas toutes au départ) des issues pour décrire les prochaines étapes à réaliser.
- Utilisez pour la suite du TP des branches pour les issues et les merger dans `main` directement (les feature branch remplace la branche `develoment`).
- Utilisez le wiki Github du dépot d'infrastructure pour documenter votre infrastructure et servir de support à la présentation finale.

## 3 - Docker

Dockeriser l'application flask (Dockerfile) et la lancer avec un fichier docker-compose. Inspirez vous du TP2 et 3 du module Docker

#### Bonus

Dockeriser l'application microblog avec une base de donnée MySQL à mettre dans un conteneur à part

#### Bonus Avancé

Dockeriser une application microservice GRPC.

## 4 - Kubernetes installation

#### Simple

Installer k3s dans dans une machine virtuelle ubuntu.
Installer un ingress nginx pour pouvoir exposer l'application web à l'extérieur
Installer un repository d'image docker simple
Versionner les fichiers d'installation kubernetes

#### Bonus

Installer ArgoCD pour gérer le Ingress et les autres éléments de Kubernetes en mode GitOps

#### Bonus Avancé

Installer un cluster kubernetes dans 3 machines virtuelles avec Ansible et Kubespray.

## 5 - Kubernetes déploiement de l'application

#### Simple

Déployer l'application flask simple, l'exposer à l'aide d'un Ingress

#### Bonus

Déployer l'application flask avec une base de donnée. Installez MySQL à l'aide d'un chart Helm.

#### Bonus Avancé

Déployer l'application microservice GRPC avec Istio

<!--
## Ansible et Amazon Web Service

#### Simple

Écrire un playbook Ansible de provisionning de DNS avec AWS (pour pointer sur le cluster Kubernetes)

#### Bonus

Écrire un déploiement Ansible de l'application web (simple ou avec mysql) sur un VPS Amazon Web Service.

## Testing

#### Simple

Ecrire quelques tests unitaires et d'intégration

#### Bonus

Bootstrapper la base de données pour les tests d'intégration

#### Bonus avancé

Écrire des tests pour l'application GRPC

## Jenkins

#### Simple

Installer Jenkins avec un chart Helm
Créer un Pipeline as Code pour lancer les tests
Créer un Stage pour construire, pousser l'image docker et effectuer le déploiement dans Kubernetes si les tests sont concluants.

#### Bonus

Lancer et bootstrapper une BDD de test pour les tests d'intégration
Gérer Jenkins à l'aide de ArgoCD

#### Bonus Avancé

Adapter la CI et la CD à l'application GRPC

## Monitoring

#### Simple

Installer ELK dans le cluster.
Envoyer les logs des conteneurs Docker dans la suite ELK.

#### Bonus
Personnaliser un dashboard de monitoring de l'application dans Kibana -->
