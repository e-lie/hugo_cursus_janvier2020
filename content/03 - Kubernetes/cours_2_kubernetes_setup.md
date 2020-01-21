---
title: Cours 2 - Mettre en place un cluster Kubernetes
draft: false
---


## Architecture de Kubernetes - Partie 1 


##### Kubernetes master

- Le Kubernetes master est responsable du maintien de l’état souhaité pour votre cluster. Lorsque vous interagissez avec Kubernetes, par exemple en utilisant l’interface en ligne de commande kubectl, vous communiquez avec le master Kubernetes de votre cluster.

- Le “master” fait référence à un ensemble de processus gérant l’état du cluster. Le master peut également être répliqué pour la disponibilité et la redondance.

##### Noeuds Kubernetes

Les nœuds d’un cluster sont les machines (serveurs physiques, machines virtuelles, etc.) qui exécutent vos applications et vos workflows. Le master node Kubernetes contrôle chaque noeud; vous interagirez rarement directement avec les nœuds.

![](../../images/kubernetes/k8s_archi1.png)

- Pour utiliser Kubernetes, vous utilisez les objets de l’API Kubernetes pour décrire l’état souhaité de votre cluster: quelles applications ou autres processus que vous souhaitez exécuter, quelles images de conteneur elles utilisent, le nombre de réplicas, les ressources réseau et disque que vous mettez à disposition, et plus encore.

- Vous définissez l’état souhaité en créant des objets à l’aide de l’API Kubernetes, généralement via l’interface en ligne de commande, kubectl. Vous pouvez également utiliser l’API Kubernetes directement pour interagir avec le cluster et définir ou modifier l’état souhaité.

- Une fois que vous avez défini l’état souhaité, le plan de contrôle Kubernetes (control plane) permet de faire en sorte que l’état actuel du cluster corresponde à l’état souhaité. Pour ce faire, Kubernetes effectue automatiquement diverses tâches, telles que le démarrage ou le redémarrage de conteneurs, la mise à jour du nombre de réplicas d’une application donnée, etc.

### Le Kubernetes control plane

- Le control plane Kubernetes comprend un ensemble de processus en cours d’exécution sur votre cluster:

    - Le Kubernetes master en anglais est un ensemble de trois processus qui s’exécutent sur un seul nœud de votre cluster, désigné comme nœud maître (master node en anglais). Ces processus sont:
      - kube-apiserver
      - kube-controller-manager
      - kube-scheduler.
  
    - Chaque nœud non maître de votre cluster exécute deux processus:
        kubelet, qui communique avec le Kubernetes master.
        kube-proxy, un proxy réseau reflétant les services réseau Kubernetes sur chaque nœud.


Les différentes parties du control plane Kubernetes, telles que les processus Kubernetes master et kubelet, déterminent la manière dont Kubernetes communique avec votre cluster.

Le control plane conserve un enregistrement de tous les objets Kubernetes du système et exécute des boucles de contrôle continues pour gérer l’état de ces objets. À tout moment, les boucles de contrôle du control plane répondent aux modifications du cluster et permettent de faire en sorte que l’état réel de tous les objets du système corresponde à l’état souhaité que vous avez fourni.

Par exemple, lorsque vous utilisez l’API Kubernetes pour créer un objet Deployment, vous fournissez un nouvel état souhaité pour le système. Le control plane Kubernetes enregistre la création de cet objet et exécute vos instructions en lançant les applications requises et en les planifiant vers des nœuds de cluster, afin que l’état actuel du cluster corresponde à l’état souhaité.


## Le client Kubectl

Permet depuis sa machine de travail de contrôler le cluster avec une ligne de commande qui ressemble un peu à celle de docker (cf TP1 et TP2):

- Lister les ressources
- Créer et supprimer les ressources
- Gérer les droits d'accès
- etc.

Cet utilitaire s'installe avec un gestionnaire de paquet classique mais est souvent fournit directement par une distribution de développement de kubernetes (microk8s ou Docker Desktop).

Nous l'installerons avec `apt` dans le TP1.

Pour se connecter `kubectl` a besoin de l'adresse de l'API kubernetes, d'un nom d'utilisateur et d'un certificat.

- Ces informations sont fournies sous forme d'un fichier YAML appelé kubeconfig
- Comme nous le verrons en TP ces informations sont généralement fournies directement par le fournisseur d'un cluster k8s (provider ou k8s de dev)

Le fichier kubeconfig par défaut se trouve sur Linux à l'emplacement `~/.kube/config`.

On peut aussi préciser la configuration a runtime comme ceci: `kubectl --kubeconfig=fichier_kubeconfig.yaml <commandes_k8s>`

Le même fichier kubeconfig peut stocker plusieurs configurations dans un fichier YAML:

Example:

```yaml
apiVersion: v1

clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURBVENDQWVtZ0F3SUJBZ0lKQUl5OUpGcjZreVBkTUEwR0NTcUdTSWIzRFFFQkN3VUFNQmN4RlRBVEJnTlYKQkFNTURERXdMakUxTWk0eE9ETXVNVEFlRncweU1EQXhNVEV5TXpRNU1qRmFGdzAwTnpBMU1qa3lNelE1TWpGYQpNQmN4RlRBVEJnTlZCQU1NRERFd0xqRTFNaTR4T0RNdU1UQ0NBU0l3RFFZSktvWklodmNOQVFFQkJRQURnZ0VQCkFEQ0NBUW9DZ2dFQkFMQ1ZwY1VzNCtIRmVoYWtyVGl5cytYWDVaUzFHcDd3WS9vSHBjTFJ4UUdVZVQ2SG5VbzYKc1Z5N2tpYllWR1Q2c2tlZUI0M21XaTZzWFpzdFhGd0o3clJBS3hFUFlSamdZMytHY24yZTRVU3hVT2pWUnBTVwo4Vm4zR2tKRFBtVTg3WEE0NHZqR2V0dTNweFZJSEhHT2FMQlFkSmdDWjBrVFNjT0piY3N1YXRURFFBVkh2SVc0CmVkSlFjTnk2WXVZd1hDU3kvTS9GS1czQllHcEpyWUI2SHNjazBWSWdpY3lsbVArZW1iODBSTmtSWUtDcXRIZk0KbFF4N0dmS25oK1Y0My95eUxSSkhIUmZWQzI2S2VzWlNMSWk1OVhiVmpLQ3loVFJxK2crYnRNRFZnZGdoTFpJQgo0OGJEZTNab2RyMldEWGZvM2JaeDliZmFWWHdLR3p1dXlwVUNBd0VBQWFOUU1FNHdIUVlEVlIwT0JCWUVGTW5YCmtLdXFNZnBoYjYwa2lZZlFHVGR2ckQ2OU1COEdBMVVkSXdRWU1CYUFGTW5Ya0t1cU1mcGhiNjBraVlmUUdUZHYKckQ2OU1Bd0dBMVVkRXdRRk1BTUJBZjh3RFFZSktvWklodmNOQVFFTEJRQURnZ0VCQUlNWkwyalYraVN1MWRYawpHZ0tPUTZGOVpTcE4weGp5RFMzVGYwb3d3YUxqWXNtUGlWREYrKzArUVZNUmJaVTc2SEhIUFI3WVlkc1lucTdLCkVFbTJrVGpCZWNmcGEwUGFpQVRDWVdwem1PZGNTWWhZQkpqWUxqQVpqZkV1b3NIUndFOU5hRHpIbmtRWXc2QzIKY2l4NUQ1U0Z5N3crRFlmZ1ZDNHJWMjNOMTlEeTZ3cWVCN0dWYmNjRUpEOGlVSkE4Y1BobUZGSnBjd0N1MVZqbApURHpDNEVmQlBHUEFLM2VBTm1FZ0hqQXRQK25ZVnhybS9aa1FLa2JYMVFQUHZhazJkcWlJM1REUFpLOVVPdDh1CnZpMVhObHJsVmZuaE9BdEo2TTJVUDdTUmRSSENzTzl5N3BTNFVtdVlhYldGVW1rRmhRdmdMN1loL1krV01NUHkKR28xN2ordz0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
    server: https://127.0.0.1:16443
  name: microk8s-cluster
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURKekNDQWcrZ0F3SUJBZ0lDQm5Vd0RRWUpLb1pJaHZjTkFRRUxCUUF3TXpFVk1CTUdBMVVFQ2hNTVJHbG4KYVhSaGJFOWpaV0Z1TVJvd0dBWURWUVFERXhGck9ITmhZWE1nUTJ4MWMzUmxjaUJEUVRBZUZ3MHlNREF4TVRreQpNVFEzTXpsYUZ3MDBNREF4TVRreU1UUTNNemxhTURNeEZUQVRCZ05WQkFvVERFUnBaMmwwWVd4UFkyVmhiakVhCk1CZ0dBMVVFQXhNUmF6aHpZV0Z6SUVOc2RYTjBaWElnUTBFd2dnRWlNQTBHQ1NxR1NJYjNEUUVCQVFVQUE0SUIKRHdBd2dnRUtBb0lCQVFERGxxU216eHFXT3YySm4zRlBvTk0zLzNRTXVvK2VPcktTd00xb2VMaTJFQXAvL2NjegphaUtReXpUTGFHam9MU042NFFNQlpBMzB5WWZxazI2M0RHOGpvU3JTaHZ3RFZEaE5LKzdHVGlBYUhEYXUxY0VzCi9TWWxFT2plTXNWZnAxT2gyT2dTb0Y0QWdNU3hZTXNsY0UyS2ZWUTdyTEo0bnlta2hRMEpnZTdzd29TVkNTdXAKaFV5UE1iYVBqYmJOWEJnaWNCSXB5VnpWeXoyL21KWXdtdWJNM1hzajkxemx0YVY5MjRNSGlZNVpKcEUrNnNFUApjRldBeFlyWFRhTkVmUTcyMWtIUmFOdTNtNWdmMWpYeVFGY1JVanBqNnl6UUxuNVpPa2Q3NUh2Qk9uZ1JUejRyCi91cTZFTTBsOURKSEgyWXhLSjZtZW5ESHFEcDM0b0ZNYW9iaEFnTUJBQUdqUlRCRE1BNEdBMVVkRHdFQi93UUUKQXdJQmhqQVNCZ05WSFJNQkFmOEVDREFHQVFIL0FnRUFNQjBHQTFVZERnUVdCQlMxQ1NvSnJFakptSTloMUpPTApld2t0SUltR2ZqQU5CZ2txaGtpRzl3MEJBUXNGQUFPQ0FRRUF3cmw0bnhQbkVPTGFQQ1g1d0Y1bTFtaGxiMklJClNrQXJOM1gzNDlZakJxWllIT1dQUDdWNUEwQzluRHRnTWZqNi9XVlBHTWhSS3ZsbElGMEx0WWNlM3NiUWtnSlkKWEdPditadVF3WWlzMXlndUhZV204RFpJSWtPMTRneU5ITlRkQVRjRi8ySWxSSU42ZXJ2Rk5rWnNZbnVtck9CTwo2SEVmMDdZTFBoQXF3TncyRm1ZV2hVay9vc2sySXVCNVRaN2VKeUFrSkdXZElCUlQ3YkZiWXFDK1NaUEhNaSs5CnZlZ1htejNDUW54bW9DMWR3Y1dOMEJNUm1Mck85ZVRqczlsQ1JkUzlEMTE5enlTTFd5OWk4MStPWHFOM1hlcGQKZjBOamJ2bkhEblVOTG5Jbm16d2FVZEcyRithWkRuNWV3SCsxYmtGOHcxdWI5eHYyemdXU1F3NTdtdz09Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
    server: https://5ba26bee-00f1-4088-ae11-22b6dd058c6e.k8s.ondigitalocean.com
  name: do-lon1-k8s-tp-cluster

contexts:
- context:
    cluster: microk8s-cluster
    user: admin
  name: microk8s
- context:
    cluster: do-lon1-k8s-tp-cluster
    user: do-lon1-k8s-tp-cluster-admin
  name: do-lon1-k8s-tp-cluster
- context:
    cluster: microk8s-cluster
    namespace: kube-system
    user: admin
  name: microk8s-system

current-context: do-lon1-k8s-tp-cluster

kind: Config
preferences: {}

users:
- name: do-lon1-k8s-tp-cluster-admin
  user:
      token: 8b2d33e45b980c8642105ec827f41ad343e8185f6b4526a481e312822d634aa4
- name: admin
  user:
    password: ZzltdE9PbHR3aTNjUEtFa0Z3V0FGZkZVcEdmaFhnWVZUSTZxdU9venkrbz0K
    username: admin
```

Ce fichier dé

## Installation de Développement

Pour installer un cluster de développement:

- solution officielle: Minikube tourne dans une VM (pas possible dans notre VM ubuntu de TP)
- solution plus légère et puissante: microk8s utilisée en TP
- avec Docker Desktop depuis peu (virtualisé aussi)

## Installer un cluster de production avec `kubeadm`

Installer un cluster de production Kubernetes à la main est nettement plus complexe que mettre en place un cluster Swarm.

- Installer le démon `Kubelet` sur tous les noeuds
- Installer l'outil de génération de cluster `kubeadm` sur un noeud master
- Générer les bon certificats avec `kubeadm`
- Installer un réseau CNI k8s comme `flannel` (d'autre sont possible et le choix vous revient)
- Déployer la base de donnée `etcd` avec `kubeadm`
- Connecter les noeuds worker au master.

L'installation est décrite dans la [documentation officielle](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/)

## Installer un cluster Complètement à la main

On peut également installer Kubernetes de façon encore plus manuelle soit pour déployer une configuration vraiment spécifique ou simplement pour mieux comprendre ses rouages et composants.

Ce type d'installation est décrite par exemple [ici - kubernetes the hard way](https://github.com/kelseyhightower/kubernetes-the-hard-way).

## Commander un cluster en tant que service (managed cluster) dans le cloud

Tous les principaux provider de cloud fournissent depuis plus ou moins longtemps des solution de cluster gérées par eux:

- Google Cloud Plateform avec Google Kubernetes Engine (GKE) : très populaire car tres flexible et l'implémentation de référence de kubernetes.
- AWS avec EKS : kubernetes assez standard mais à la sauce amazon pour la gestion de l'accès, des load balancer ou du scaling.
- DigitalOcean : Un peu moins de fonctions mais plus simple à appréhender (nous utiliserons )



