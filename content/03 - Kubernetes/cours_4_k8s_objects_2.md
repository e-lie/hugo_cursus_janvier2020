---
title: Cours 4 - Objets Kubernetes - Partie 2
draft: famse
---

## D'autres objets k8s

<!-- FIXME: Secrets and configmaps -->

### StatefulSets

<!-- TODO: préciser usage 
    Stable, unique network identifiers.
    Stable, persistent storage.
    Ordered, graceful deployment and scaling.
    Ordered, automated rolling updates.

-->

On utilise les `Statefulsets` pour répliquer un ensemble de pods dont l'état est important : par exemple, des pods dont le rôle est d'être une base de données, manipulant des données sur un disque.

Un objet `StatefulSet` représente un ensemble de pods dotés d'identités uniques et de noms d'hôtes stables. Quand on supprime un StatefulSet, par défaut les volumes liés ne sont pas supprimés.

Les StatefulSets utilisent un nom en commun suivi de numéros qui se suivent. Par exemple, un StatefulSet nommé `web` comporte des pods nommés `web-0`, `web-1` et` web-2`. Par défaut, les pods StatefulSet sont déployés dans l'ordre et arrêtés dans l'ordre inverse (`web-2`, `web-1` puis `web-0`).

### DaemonSets

Une autre raison de répliquer un ensemble de Pods est de programmer un seul Pod sur chaque nœud du cluster. En général, la motivation pour répliquer un Pod sur chaque nœud est de faire atterrir une sorte d'agent ou de démon sur chaque nœud, et l'objet Kubernetes pour y parvenir est le DaemonSet. Par exemple pour des besoins de monitoring.

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

### Role-Based Access Control

<!-- TODO: add ABAC? https://kubernetes.io/docs/reference/access-authn-authz/abac/ -->

Kubernetes intègre depuis quelques versions un système de permissions fines sur les ressources et les namespaces.

- Classiquement on crée des `roles` comme `admin` ou `monitoring` qui désignent un ensemble de permission
- La logique de ce système de permissions est d'associer un **objet** (un type de ressource k8s) à un **verbe** (par exemple : `get`, `list`, `create`, `delete`…)
- On crée ensuite des utilisateurs appelés `serviceaccounts` dans K8s.
- On lie les roles et serviceaccounts à l'aide d'objets `rolebindings`.
<!-- - TODO: Exemples -->

A côté des rôles crées pour les utilisateur·ices et processus du cluster, il existe des modèles de rôles prédéfinis qui sont affichables avec :

`kubectl get clusterroles`

La plupart de ces rôles intégrés sont destinés au `kube-system`, c'est-à-dire aux processus internes du cluster.

Cependant quatre rôles génériques existent aussi par défaut :

- Le rôle `cluster-admin` fournit un accès complet à l'ensemble du cluster.
- Le rôle `admin` fournit un accès complet à un espace de noms précis.
- Le rôle `edit` permet à un·e utilisateur·ice de modifier des choses dans un espace de noms.
- Le rôle `view` permet l'accès en lecture seule à un espace de noms.

<!-- TODO MENTIONNER la commande kc auth can-i (cf kubernetes up and running chap RBAC)>

### Les fichiers kustomization

Il est courant de décrire un ensemble de resources dans le même fichier, séparées par `---`.
Mais on pourrait préférer rassembler plusieurs fichiers dans un même dossier et les appliquer d'un coup.

Pour cela K8s propose le concept de `kustomization`.

Exemple:

```yaml
k8s-mysql/
├── kustomization.yaml
├── mysql-deployment.yaml
└── wordpress-deployment.yaml
```

`kustomization.yaml`

```yaml
secretGenerator:
  - name: mysql-pass
    literals:
      - password=YOUR_PASSWORD
resources:
  - mysql-deployment.yaml
  - wordpress-deployment.yaml
```
On peut ensuite l'appliquer avec `kubectl apply -k ./`

A noter que `kubectl kustomize .` permet de visualiser l'ensemble des modifications avant de les appliquer (`kubectl kustomize . | less` pour mieux lire).

<!-- 
Goal: grant a pod access to a secured something?
don’t put secrets in the container image!
12-factor says: config comes from the environment
Kubernetes is the environment
Manage secrets via the Kubernetes API
Inject them as virtual volumes into Pods
late-binding
tmpfs - never touches disk -->
