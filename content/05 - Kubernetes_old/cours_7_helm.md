---
title: Cours 7 - Helm, le gestionnaire de paquets Kubernetes
draft: false
weight: 2070
---


Nous avons vu que dans Kubernetes la configuration de nos services / applications se fait généralement via de multiples fichiers YAML.

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

### Helm

Quand on a une seule application cela reste gérable avec des kustomizations ou sans, mais dès qu’on a plusieurs environnements, applications et services, on se retrouve vite submergé·es de fichiers de centaines, voire de milliers, de lignes qui sont, de plus, assez semblables. 
C'est donc "trop" déclaratif, et il faut se concentrer sur les quelques propriétés que l'on souhaite créer ou modifier,

Pour pallier ce problème, il existe l'utilitaire Helm, qui produit les fichiers de déploiement que l'on souhaite.

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


### Architecture client-serveur de Helm

Helm désigne une application client en ligne de commande.

Pour fonctionner sur le cluster, Helm a besoin d'installer un gestionnaire appelé Tiller : c'est le serveur qui communique avec le client Helm et l’API de Kubernetes pour gérer vos déploiements.

Lors de l’initialisation de Helm, le client installe Tiller sur un pod du cluster.

Helm utilise automatiquement votre fichier `kubeconfig` pour se connecter.

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

Il est possible de développer soit même des opérateurs mais il s'agit de développement complexes qui devraient être entrepris par les développeurs du logiciel et qui sont surtout utiles pour des applications distribuées et stateful. Les opérateurs n'ont pas forcément vocation à remplacer les Charts Helm comme on l'entend parfois.

Voir : https://thenewstack.io/kubernetes-when-to-use-and-when-to-avoid-the-operator-pattern/
