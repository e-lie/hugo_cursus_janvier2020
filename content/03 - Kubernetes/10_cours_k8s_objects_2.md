---
title: 10 - Cours - Objets Kubernetes Partie 2.
draft: false
weight: 2060
---


## Le stockage dans Kubernetes

### Les Volumes Kubernetes

Comme dans Docker, Kubernetes fournit la possibilité de mondes volumes virtuels dans les conteneurs de nos pod. On liste séparément les volumes de notre pod puis on les monte une ou plusieurs dans les différents conteneurs Exemple:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-pd
spec:
  containers:
  - image: k8s.gcr.io/test-webserver
    name: test-container
    volumeMounts:
    - mountPath: /test-pd
      name: test-volume
  volumes:
  - name: test-volume
    hostPath:
      # chemin du dossier sur l'hôte
      path: /data
      # ce champ est optionnel
      type: Directory
```

La problématique des volumes et du stockage est plus compliquée dans kubernetes que dans docker car k8s cherche à répondre à de nombreux cas d'usages. [doc officielle](https://kubernetes.io/fr/docs/concepts/storage/volumes/). Il y a donc de nombeux types de volumes kubernetes correspondants à des usages de base et aux solutions proposées par les principaux fournisseurs de cloud.

Mentionnons quelques d'usage de base des volumes:

- `hostPath`: monte un dossier du noeud ou est plannifié le pod à l'intérieur du conteneur.
- `local`: comme hostPath mais conscient de la situation physique du volume sur le noeud et à combiner avec les placements de pods avec `nodeAffinity`
- `emptyDir`: un dossier temporaire qui est supprimé en même temps que le pod
- `configMap`: pour monter des fichiers de configurations provenant du cluster à l'intérieur des pods
- `secret`: pour monter un secret (configuration) provenant du cluster à l'intérieur des pods
- `cephfs`: monter un volume ceph provenant d'un ceph installé sur le cluster
- etc.

En plus de la gestion manuelle des volumes avec les option précédentes, kubernetes permet de provisionner dynamiquement du stockage en utilisant des plugins de création de volume grâce à 3 types d'objets: `StorageClass` `PersistentVolume` et `PersistentVolumeClaim`.
### Les types de stockage avec les `StorageClasses`

Le stockage dynamique dans Kubernetes est fourni à travers des types de stockage appelés *StorageClasses* :

- dans le cloud, ce sont les différentes offres de volumes du fournisseur,
- dans un cluster auto-hébergé c'est par exemple des opérateurs de stockage comme `rook.io` ou `longhorn`(Rancher).

[doc officielle](https://kubernetes.io/docs/concepts/storage/storage-classes/)

### Demander des volumes et les liers aux pods :`PersistentVolumes` et `PersistentVolumeClaims`

Quand un conteneur a besoin d'un volume, il crée une *PersistentVolumeClaim* : une demande de volume (persistant). Si un des objets *StorageClass* est en capacité de le fournir, alors un *PersistentVolume* est créé et lié à ce conteneur : il devient disponible en tant que volume monté dans le conteneur.

- les *StorageClasses* fournissent du stockage
- les conteneurs demandent du volume avec les *PersistentVolumeClaims*
- les *StorageClasses* répondent aux *PersistentVolumeClaims* en créant des objets *PersistentVolumes* : le conteneur peut accéder à son volume.

[doc officielle](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)

Le provisionning de volume peut être manuelle (on crée un objet `PersistentVolume` ou non la `PersistentVolumeClaim` mène directement à la création d'un volume persistant si possible)

### Des déploiements plus stables et précautionneux : les StatefulSets

L'objet `StatefulSet` est relativement récent dans Kubernetes.

On utilise les `Statefulsets` pour répliquer un ensemble de pods dont l'état est important : par exemple, des pods dont le rôle est d'être une base de données, manipulant des données sur un disque.

Un objet `StatefulSet` représente un ensemble de pods dotés d'identités uniques et de noms d'hôtes stables. Quand on supprime un StatefulSet, par défaut les volumes liés ne sont pas supprimés.

Les StatefulSets utilisent un nom en commun suivi de numéros qui se suivent. Par exemple, un StatefulSet nommé `web` comporte des pods nommés `web-0`, `web-1` et` web-2`. Par défaut, les pods StatefulSet sont déployés dans l'ordre et arrêtés dans l'ordre inverse (`web-2`, `web-1` puis `web-0`).

En général, on utilise des StatefulSets quand on veut :
- des identifiants réseau stables et uniques
- du stockage stable et persistant
- des déploiements et du scaling contrôlés et dans un ordre défini
- des rolling updates dans un ordre défini et automatisées

## Paramétrer ses Pods

### Les ConfigMaps 

D'après les recommandations de développement [12factor](https://12factor.net/fr), la configuration de nos programmes doit venir de l'environnement. L'environnement est ici Kubernetes.

Les objets ConfigMaps permettent d'injecter dans des pods des fichiers de configuration en tant que volumes.

### les Secrets

Les Secrets se manipulent comme des objets ConfigMaps, mais sont faits pour stocker des mots de passe, des clés privées, des certificats, des tokens, ou tout autre élément de config dont la confidentialité doit être préservée.
Un secret se créé avec l'API Kubernetes, puis c'est au pod de demander à y avoir accès.

Il y a 3 façons de donner un accès à un secret :
- le secret est un fichier que l'on monte en tant que volume dans un conteneur (pas nécessairement disponible à l'ensemble du pod). Il est possible de ne jamais écrire ce secret sur le disque (volume `tmpfs`).
- le secret est une variable d'environnement du conteneur.

Pour définir qui et quelle app a accès à quel secret, on utilise les fonctionnalités dites "RBAC" de Kubernetes.


## Lier utilisateurs et autorisations: Le Role-Based Access Control (RBAC)

<!-- TODO: add ABAC? https://kubernetes.io/docs/reference/access-authn-authz/abac/ -->

Kubernetes intègre depuis quelques versions un système de permissions fines sur les ressources et les namespaces. Il fonctionne en liant des ensembles de permissions appelées `Roles` à des identités/comptes humains appelés `User` ou des comptes de services pour vos programmes appelés `ServiceAccount`.

Exemple de comment générer un certificat à créer un nouvel utilisateur dans minikube: https://docs.bitnami.com/tutorials/configure-rbac-in-your-kubernetes-cluster/

### Roles et ClusterRoles + bindings

Une `role` est un objet qui décrit un ensemble d'actions permises sur certaines ressources et s'applique sur **un seul namespace**. Pour prendre un exemple concret, voici la description d'un roles qui authorise la lecture, création et modification de `pods` et de `services` dans le namespace par défaut:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: default
  name: pod-and-services
rules:
- apiGroups: [""]
  resources: ["pods", "services"]
  verbs: ["create", "delete", "get", "list", "patch", "update", "watch", "proxy"]
```

- Un role est une liste de règles `rules`

- Les rules sont décrites à l'aide de 8 verbes différents qui sont ceux présent dans le role d'exemple au dessus qu'ont associe à une liste d'objets.

- Le role **ne fait rien par lui même** : il doit être appliqué à une identité ie un `User` ou `ServiceAccount`.

- Classiquement on crée des `Roles` comme `admin` ou `monitoring` qui désignent un ensemble de permission consistante pour une tâche donnée.

- Notre role exemple est limité au `namespace default`. Pour créer des permissions valable pour tout le cluster on utilise à la place un objet appelé un `ClusterRole` qui fonctionne de la même façon mais indépendamment des namespace.

- Les `Roles` et `ClusterRoles` sont ensuite appliqués aux `ServicesAccounts` à l'aide respectivement de `RoleBinding` et `ClusterRoleBinding` comme l'exemple suivant:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  namespace: default
  name: pods-and-services
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: alice
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: mydevs
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: pod-and-services
```

En plus des rôles que vous pouvez créer pour les utilisateur·ices et processus de votre cluster, il existe déjà dans kubernetes un ensemble de `ClusterRoles` prédéfinis qui sont affichables avec :

`kubectl get clusterroles`

La plupart de ces rôles intégrés sont destinés au `kube-system`, c'est-à-dire aux processus internes du cluster.

Cependant quatre rôles génériques existent aussi par défaut :

- Le rôle `cluster-admin` fournit un accès complet à l'ensemble du cluster.
- Le rôle `admin` fournit un accès complet à un espace de noms précis.
- Le rôle `edit` permet à un·e utilisateur·ice de modifier des choses dans un espace de noms.
- Le rôle `view` permet l'accès en lecture seule à un espace de noms.


La commande `kubectl auth can-i <verb> <type_de_resource>` permet de déterminer selon le profil utilisé (défini dans votre `kubeconfig`) les permissions actuelles de l'user sur les objets Kubernetes.

### Kubernetes API et extension par APIgroups

Tous les types de resources Kubernetes correspondent à un morceau (un sous arbre) d'API REST de Kubernetes. Ces chemins d'API pour chaque ressources sont classés par groupe qu'on appelle des `apiGroups`:

- On peut lister les resources et leur groupes d'API avec la commande `kubectl api-resources -o wide`.
- Ces groups correspondent aux préfixes indiqué dans la section `apiVersion` des descriptions de ressources.
- Ces groupes d'API sont versionnés sémantiquement et classés en `alpha` `beta` et `stable`. `beta` indique déjà un bon niveau de stabilité et d'utilisabilité et beaucoup de ressources officielles de kubernetes ne sont pas encore en api stable. Exemple: les `CronJobs` viennent de se stabiliser au début 2021.
- N'importe qui peut développer ses propres types de resources appelées `CustomResourceDefinition` (voir ci dessous) et créer un `apiGroup` pour les ranger.

Documentation: https://kubernetes.io/docs/reference/using-api/

### Operators et Custom Resources Definitions (CRD)

Un opérateur est :
 - un morceau de logique opérationnelle de votre infrastructure (par exemple: la mise à jour votre logiciel de base de donnée stateful comme cassandra ou elasticsearch) ...
 - ... implémenté dans kubernetes par un/plusieurs conteneur(s) "controller" ...
 - ... controllé grâce à une extension de l'API Kubernetes sous forme de nouveaux type d'objets kubernetes personnalisés (de haut niveau) appelés *CustomResourcesDefinition* ...
 - ... qui crée et supprime des resources de base Kubernetes comme résultat concrêt.

Les opérateurs sont un sujet le plus *méta* de Kubernetes et sont très à la mode depuis leur démocratisation par Red Hat pour la gestion automatique de base de données.

- Il peuvent être développés avec un framework Go ou Ansible
- Ils sont généralement répertoriés sur le site: https://operatorhub.io/

Exemples :
- L'opérateur Prometheus permet d'automatiser le monitoring d'un cluster et ses opérations de maintenance.
- la chart officielle de la suite Elastic (ELK) définit des objets de type `elasticsearch`
- KubeVirt permet de rajouter des objets de type VM pour les piloter depuis Kubernetes
- Azure propose des objets correspondant à ses ressources du cloud Azure, pour pouvoir créer et paramétrer des ressources Azure directement via la logique de Kubernetes.


![](../../images/kubernetes/k8s_crd.png)

##### Limites des opérateurs

Il est possible de développer soit même des opérateurs mais il s'agit de développement complexes qui devraient être entrepris par les développeurs d'un logiciel uniquement et qui sont surtout utiles pour des applications distribuées et directement stateful.

Voir : https://thenewstack.io/kubernetes-when-to-use-and-when-to-avoid-the-operator-pattern/