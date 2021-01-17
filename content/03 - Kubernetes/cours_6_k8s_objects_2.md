---
title: Cours 6 - Objets Kubernetes - Partie 2
draft: famse
---

## Objets k8s, suite



### Le stockage dans Kubernetes

#### StorageClasses 
Le stockage dans Kubernetes est fourni à travers des types de stockage appelés *StorageClasses* :
- dans le cloud, ce sont les différentes offres du fournisseur,
- dans un cluster auto-hébergé c'est par exemple :
  - un disque dur local ou distant (NFS)
  - ou bien une solution de stockage distribué
    - les plus connues sont Ceph et GlusterFS

#### PersistentVolumeClaims et PersistentVolumes
Quand un conteneur a besoin d'un volume, il crée une *PersistentVolumeClaim* : une demande de volume (persistant). Si un des objets *StorageClass* est en capacité de le fournir, alors un *PersistentVolume* est créé et lié à ce conteneur : il devient disponible en tant que volume monté dans le conteneur.


- les *StorageClasses* fournissent du stockage
- les conteneurs demandent du volume avec les *PersistentVolumeClaims*
- les *StorageClasses* répondent aux *PersistentVolumeClaims* en créant des objets *PersistentVolumes* : le conteneur peut accéder à son volume.

### StatefulSets

On utilise les `Statefulsets` pour répliquer un ensemble de pods dont l'état est important : par exemple, des pods dont le rôle est d'être une base de données, manipulant des données sur un disque.

Un objet `StatefulSet` représente un ensemble de pods dotés d'identités uniques et de noms d'hôtes stables. Quand on supprime un StatefulSet, par défaut les volumes liés ne sont pas supprimés.

Les StatefulSets utilisent un nom en commun suivi de numéros qui se suivent. Par exemple, un StatefulSet nommé `web` comporte des pods nommés `web-0`, `web-1` et` web-2`. Par défaut, les pods StatefulSet sont déployés dans l'ordre et arrêtés dans l'ordre inverse (`web-2`, `web-1` puis `web-0`).

En général, on utilise des StatefulSets quand on veut :
- des identifiants réseau stables et uniques
- du stockage stable et persistant
- des déploiements et du scaling contrôlés et dans un ordre défini
- des rolling updates dans un ordre défini et automatisées

### DaemonSets

Une autre raison de répliquer un ensemble de Pods est de programmer un seul Pod sur chaque nœud du cluster. En général, la motivation pour répliquer un Pod sur chaque nœud est de faire atterrir une sorte d'agent ou de démon sur chaque nœud, et l'objet Kubernetes pour y parvenir est le DaemonSet. Par exemple pour des besoins de monitoring, ou pour configurer le réseau sur chacun des nœuds.

### Deployments, DaemonSets, StatefulSets

Étant donné les similitudes entre les DaemonSets, les StatefulSets et les Deployments, il est important de comprendre quand les utiliser.

- Les **Deployments** (liés à des ReplicaSets) doivent être utilisés :
  - lorsque votre application est complètement découplée du nœud
  - que vous pouvez en exécuter plusieurs copies sur un nœud donné sans considération particulière
  - que l'ordre de création des replicas et le nom des pods n'est pas important
  - lorsqu'on fait des opérations *stateless*
-  Les **DaemonSets** doivent être utilisés :
   - lorsqu'au moins une copie de votre application doit être exécutée sur tous les nœuds du cluster (ou sur un sous-ensemble de ces nœuds).
-  Les **StatefulSets** doivent être utilisés :
  - lorsque l'ordre de création des replicas et le nom des pods est pas important
  - lorsqu'on fait des opérations *stateful* (écrire dans une base de données)

### Jobs

Les jobs sont utiles pour les choses que vous ne voulez faire qu'une seule fois, comme les migrations de bases de données ou les travaux par lots. Si vous exécutez une migration en tant que Pod normal, votre tâche de migration de base de données se déroulera en boucle, en repeuplant continuellement la base de données.

### CronJobs

Comme des jobs, mais se lance à un intervalle régulier, comme avec `cron`.

### Les ConfigMaps 

D'après les recommandations de développement [12factor](https://12factor.net/fr), la configuration de nos programmes doit venir de l'environnement. L'environnement est ici Kubernetes.

Les objets ConfigMaps permettent d'injecter dans des pods des fichiers de configuration en tant que volumes.

### les Secrets

Les Secrets se manipulent comme des objets ConfigMaps, mais sont faits pour stocker des mots de passe, des clés privées, des certificats, des tokens, ou tout autre élément de config dont la confidentialité doit être préservée.
Un secret se créé avec l'API Kubernetes, puis c'est au pod de demander à y avoir accès.

Il y a 3 façons de donner un accès à un secret :
- le secret est un fichier que l'on monte en tant que volume dans un conteneur (pas nécessairement disponible à l'ensemble du pod). Il est possible de ne jamais écrire ce secret sur le disque (volume `tmpfs`).
- le secret est une variable d'environnement du conteneur.
- cas spécifique aux registres : le secret est récupéré par kubelet quand il pull une image.

Pour définir qui et quelle app a accès à quel secret, on utilise les fonctionnalités dites "RBAC" de Kubernetes.

### Le Role-Based Access Control, les Roles et les RoleBindings

<!-- TODO: add ABAC? https://kubernetes.io/docs/reference/access-authn-authz/abac/ -->

Kubernetes intègre depuis quelques versions un système de permissions fines sur les ressources et les namespaces.

- Classiquement on crée des `Roles` comme `admin` ou `monitoring` qui désignent un ensemble de permission
- La logique de ce système de permissions est d'associer un **objet** (un type de ressource k8s) à un **verbe** (par exemple : `get`, `list`, `create`, `delete`…)
- On crée ensuite des utilisateurs appelés `ServiceAccounts` dans k8s.
- On lie les *Roles* et *ServiceAccounts* à l'aide d'objets `RoleBindings`.
<!-- - TODO: Exemples -->

A côté des rôles crées pour les utilisateur·ices et processus du cluster, il existe des modèles de rôles prédéfinis qui sont affichables avec :

`kubectl get clusterroles`

La plupart de ces rôles intégrés sont destinés au `kube-system`, c'est-à-dire aux processus internes du cluster.

Cependant quatre rôles génériques existent aussi par défaut :

- Le rôle `cluster-admin` fournit un accès complet à l'ensemble du cluster.
- Le rôle `admin` fournit un accès complet à un espace de noms précis.
- Le rôle `edit` permet à un·e utilisateur·ice de modifier des choses dans un espace de noms.
- Le rôle `view` permet l'accès en lecture seule à un espace de noms.

La commande `kubectl auth can-i` permet de déterminer selon le profil utilisé (défini dans votre `kubeconfig`) les permissions actuelles de l'user sur les objets Kubernetes.

<!-- FIXME: CRD et Operators -->