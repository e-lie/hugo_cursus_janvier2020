---
title: TP optionnel - Bootstrapper un cluster multi-noeud avec Ansible (Kubeadm ou mode manuel)
draft: false
weight: 2090
---

<!-- Comme nous l'avons évoqué dans le cours précédent, pour installer Kubernetes soi-même (et dans sa version la plus "vanilla"), on utilise généralement `kubeadm` qui est une sorte d'opérateur d'installation et mise à jour des différents composants de Kubernetes ou on peut installer les composants à la main en suivant un tutoriel `Kubernetes the hard way` (ce qui est principalement utile a des fins d'apprentissage).

Dans ce TP, après avoir choisit l'une ou l'autre méthode nous effectuerons cette installation en louant des serveurs dans le cloud et en utilisant Ansible pour bootstrapper le Cluster.

## Kubeadm : l'opérateur de cluster -->


## `Kubernetes the hard way` avec Ansible

La version la plus manuelle de l'installation de Kubernetes a été documentée à des fins d'apprentissage par Kelsey Hightower qui l'a nommé `Kubernetes the hard way`. On peut la retrouver à l'adresse https://github.com/kelseyhightower/kubernetes-the-hard-way/tree/master/docs.

La principale limite de cette méthode d'installation est le nombre très important de manipulations sur plusieurs serveurs et donc le temps d'installation conséquent, peu reproductible et qui favorise les erreurs. Pour remédier à cela, l'installation manuelle a été notamment reprise par `githubixx/RW` de tauceti.blog et intégré dans une série de tutoriels adossés à des roles Ansible qui documentent cette installation manuelle et sont d'après l'auteur utilisable en production à une échelle moyenne.

Nous allons suivre étape par étapes cette installation manuelle Ansible pour observer et commenter concrêtement les différents composants et étapes d'installation de Kubernetes.


`git clone git@github.com:e-lie/k8s_notsohardway_correction.git`