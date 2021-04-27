---
title: 02 - Cours - Mettre en place un cluster Kubernetes
draft: false
weight: 2020
---

## Architecture de Kubernetes - Partie 1 

##### Kubernetes master

- Le Kubernetes master est responsable du maintien de l’état souhaité pour votre cluster. Lorsque vous interagissez avec Kubernetes, par exemple en utilisant l’interface en ligne de commande kubectl, vous communiquez avec le master Kubernetes de votre cluster.

- Le “master” fait référence à un ensemble de processus gérant l’état du cluster. Le master peut également être répliqué pour la disponibilité et la redondance.

##### Noeuds Kubernetes

Les nœuds d’un cluster sont les machines (serveurs physiques, machines virtuelles, etc.) qui exécutent vos applications et vos workflows. Le master node Kubernetes contrôle chaque noeud; vous interagirez rarement directement avec les nœuds.

![](../../images/kubernetes/k8s_archi1.png)

- Pour utiliser Kubernetes, vous utilisez les objets de l’API Kubernetes pour décrire l’état souhaité de votre cluster: quelles applications ou autres processus que vous souhaitez exécuter, quelles images de conteneur elles utilisent, le nombre de réplicas, les ressources réseau et disque que vous mettez à disposition, et plus encore.

- Vous définissez l’état souhaité en créant des objets à l’aide de l’API Kubernetes, généralement via l’interface en ligne de commande, `kubectl`. Vous pouvez également utiliser l’API Kubernetes directement pour interagir avec le cluster et définir ou modifier l’état souhaité.

- Une fois que vous avez défini l’état souhaité, le plan de contrôle Kubernetes (control plane) permet de faire en sorte que l’état actuel du cluster corresponde à l’état souhaité. Pour ce faire, Kubernetes effectue automatiquement diverses tâches, telles que le démarrage ou le redémarrage de conteneurs, la mise à jour du nombre de *replicas* d’une application donnée, etc.

### Le Kubernetes Control Plane

- Le control plane Kubernetes comprend un ensemble de processus en cours d’exécution sur votre cluster:

    - Le master Kubernetes est un ensemble de trois processus qui s’exécutent sur un seul nœud de votre cluster, désigné comme nœud maître (*master node* en anglais). Ces processus sont:
      - `kube-apiserver`: expose l'API pour parler au cluster
      - `kube-controller-manager`: basé sur une boucle qui controlle en permanence l'état des resources et essaie de le corriger s'il n'est plus conforme.
      - `kube-scheduler`: monitore les resources des différents workers, décide et cartographie ou doivent être créé les conteneur(Pods)
  
    - Chaque nœud non maître de votre cluster exécute deux processus :
        `kubelet`, qui communique avec le Kubernetes master et controle la création et l'état des pods sur son noeud.
        `kube-proxy`, un proxy réseau reflétant les services réseau Kubernetes sur chaque nœud.


Les différentes parties du control plane Kubernetes, telles que les processus `kube-controller-manager` et `kubelet`, déterminent la manière dont Kubernetes communique avec votre cluster.

Le control plane conserve un enregistrement de tous les objets Kubernetes du système et exécute des boucles de contrôle continues pour gérer l’état de ces objets. À tout moment, les boucles de contrôle du control plane répondent aux modifications du cluster et permettent de faire en sorte que l’état réel de tous les objets du système corresponde à l’état souhaité que vous avez fourni.

Par exemple, lorsque vous utilisez l’API Kubernetes pour créer un objet `Deployment`, **vous fournissez un nouvel état souhaité pour le systèm**e. Le control plane Kubernetes enregistre la création de cet objet et exécute vos instructions en lançant les applications requises et en les planifiant vers des nœuds de cluster, afin que l’état actuel du cluster corresponde à l’état souhaité.


## Le client `kubectl`

...Permet depuis sa machine de travail de contrôler le cluster avec une ligne de commande qui ressemble un peu à celle de Docker (cf. TP1 et TP2):

- Lister les ressources
- Créer et supprimer les ressources
- Gérer les droits d'accès
- etc.

Cet utilitaire s'installe avec un gestionnaire de paquet classique mais est souvent fourni directement par une distribution de développement de kubernetes.

Nous l'installerons avec `snap` dans le TP1.

Pour se connecter, `kubectl` a besoin de l'adresse de l'API Kubernetes, d'un nom d'utilisateur et d'un certificat.

- Ces informations sont fournies sous forme d'un fichier YAML appelé `kubeconfig`
- Comme nous le verrons en TP ces informations sont généralement fournies directement par le fournisseur d'un cluster k8s (provider ou k8s de dev)

Le fichier `kubeconfig` par défaut se trouve sur Linux à l'emplacement `~/.kube/config`.

On peut aussi préciser la configuration au *runtime* comme ceci: `kubectl --kubeconfig=fichier_kubeconfig.yaml <commandes_k8s>`

Le même fichier `kubeconfig` peut stocker plusieurs configurations dans un fichier YAML :

Exemple :

```yaml
apiVersion: v1

clusters:
- cluster:
    certificate-authority: /home/jacky/.minikube/ca.crt
    server: https://172.17.0.2:8443
  name: minikube
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURKekNDQWcrZ0F3SUJBZ0lDQm5Vd0RRWUpLb1pJaHZjTkFRRUxCUUF3TXpFVk1CTUdBMVVFQ2hNTVJHbG4KYVhSaGJFOWpaV0Z1TVJvd0dBWURWUVFERXhGck9<clipped>3SCsxYmtGOHcxdWI5eHYyemdXU1F3NTdtdz09Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
    server: https://5ba26bee-00f1-4088-ae11-22b6dd058c6e.k8s.ondigitalocean.com
  name: do-lon1-k8s-tp-cluster

contexts:
- context:
    cluster: minikube
    user: minikube
  name: minikube
- context:
    cluster: do-lon1-k8s-tp-cluster
    user: do-lon1-k8s-tp-cluster-admin
  name: do-lon1-k8s-tp-cluster
current-context: do-lon1-k8s-tp-cluster

kind: Config
preferences: {}

users:
- name: do-lon1-k8s-tp-cluster-admin
  user:
      token: 8b2d33e45b980c8642105ec827f41ad343e8185f6b4526a481e312822d634aa4
- name: minikube
  user:
    client-certificate: /home/jacky/.minikube/profiles/minikube/client.crt
    client-key: /home/jacky/.minikube/profiles/minikube/client.key
```

Ce fichier déclare 2 clusters (un local, un distant), 2 contextes et 2 users.

## Installation de développement

Pour installer un cluster de développement :

- solution officielle : Minikube, tourne dans Docker par défaut (ou dans des VMs)
- solution très pratique et vanilla: kind 
- avec Docker Desktop depuis peu (dans une VM aussi)
- un cluster léger avec `k3s`, de Rancher (simple et utilisable en production/edge)

## Commander un cluster en tant que service (*managed cluster*) dans le cloud

Tous les principaux provider de cloud fournissent depuis plus ou moins longtemps des solutions de cluster gérées par eux :

- Google Cloud Plateform avec Google Kubernetes Engine (GKE) : très populaire car très flexible et l'implémentation de référence de Kubernetes.
- AWS avec EKS : Kubernetes assez standard mais à la sauce Amazon pour la gestion de l'accès, des loadbalancers ou du scaling.
- Azure avec AKS : Kubernetes assez standard mais à la sauce Amazon pour la gestion de l'accès, des loadbalancers ou du scaling.
- DigitalOcean ou Scaleway : un peu moins de fonctions mais plus simple à appréhender <!-- (nous l'utiliserons) -->

Pour sa qualité on recommande souvent Google GKE qui est plus ancien avec un bonne UX. Mais il s'agit surtout de faciliter l'intégration avec l'existant:

- Si vous utilisez déjà AWS ou Azure

## Installer un cluster de production on premise : l'outil officiel `kubeadm`

`kubeadm` est un utilitaire aider générer les certificats et les configurations spéciques pour le control plane et connecter les noeuds au control plane. Il permet également d'effectuer des taches de maintenant comme la mise à jour progressive (rolling) de chaque noeud du cluster.

- Installer le dæmon `Kubelet` sur tous les noeuds
- Installer l'outil de gestion de cluster `kubeadm` sur un noeud master
- Générer les bons certificats avec `kubeadm`
- Installer un réseau CNI k8s comme `flannel` (d'autres sont possible et le choix vous revient)
- Déployer la base de données `etcd` avec `kubeadm`
- Connecter les nœuds worker au master.

L'installation est décrite dans la [documentation officielle](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/)

Opérer et maintenir un cluster de production Kubernetes "à la main" est très complexe et une tâche à ne pas prendre à la légère. De nombreux éléments doivent être installés et géré par les opérateurs.

- Mise à jour et passage de version de kubernetes qui doit être fait très régulièrement car une version n'est supportée que 2 ans.
- Choix d'une configuration réseau et de sécurité adaptée.
- Installation probable de système de stockage distribué comme Ceph à maintenir également dans le temps
- Etc.

## Kubespray

https://kubespray.io/#/

En réalité utiliser `kubeadm` directement en ligne de commande n'est pas la meilleure approche car cela ne respecte pas l'infrastructure as code et rend plus périlleux la maintenance/maj du cluster par la suite.

Le projet kubespray est un installer de cluster kubernetes utilisant Ansible et kubeadm. C'est probablement l'une des méthodes les plus populaires pour véritablement gérer un cluster de production on premise.

Mais la encore il s'agit de ne pas sous-estimer la complexité de la maintenance (comme avec kubeadm).
## Installer un cluster complètement à la main pour s'exercer

On peut également installer Kubernetes de façon encore plus manuelle pour mieux comprendre ses rouages et composants.
Ce type d'installation est décrite par exemple ici : [Kubernetes the hard way](https://github.com/kelseyhightower/kubernetes-the-hard-way).

## Remarque sur les clusters hybrides

Il est possible de connecter plusieurs clusters ensembles dans le cloud chez plusieurs fournisseurs

