---
title: TP1 - Installation et configuration de Kubernetes
draft: true
---

Au cours de ce TP nous allons passer rapidement sur trois manières de configurer kubernetes :

- cluster de développement avec `microk8s`
- cluster managed loué chez le provider digital digital ocean
- cluster installé et géré manuellement grâce à Ansible (sur des VPS digital ocean)

### Installer microk8s

**Microk8s** est une version de développement de Kubernetes développée par Canonical (Ubuntu) qui peut être utilisée en local ou en mode cluster de plusieurs noeuds:
  - Elle n'utilise pas de machine virtuelle contrairement à la solution de dev plus classique **Minikube**.

sudo snap install microk8s --edge --classic

## Installer le client Kubernetes Kubectl

- Ajouter le dépot officiel kubernetes pour Ubuntu : `echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list`
- Mettre à jour les dépôts et installer **kubectl** : `sudo apt update && sudo apt install -y kubectl`
- vérifier l'installation: `kubectl version --short`

## Configurer kubectl pour se connecter au cluster microk8s

- `microk8s.config > ~/.kube/config`
- `sudo gedit ~/.kube/config`
- `kubectl

### Bash completion

```bash
apt-get install bash-completion
source <(kubectl completion bash)
echo "source <(kubectl completion bash)" >> ${HOME}/.bashrc
```

## Explorer un cluster k8s

`kubectl get nodes`

`kubectl describe node/<votrenode>`

`kubectl get all`

`kubectl get namespaces`

`kubectl get all -n kube-system`

`kubectl get all --all-namespaces`

`kubectl describe namespace/kube-system`

`kubectl create deployment microbot --image=dontrebootme/microbot:v1`
`kubectl scale deployment microbot --replicas=2`
`microk8s.kubectl expose deployment microbot --type=NodePort --port=80 --name=microbot-service`


### Dashboard Kubernetes

microk8s.enable dashboard

### Ingress example avec nginx et microk8s

https://kndrck.co/posts/microk8s_ingress_example/

vraiment compréhensible simple à tester sur digital ocean mais 404 à la fin :/


## Mettre en place un custer K8s dans le cloud avec DigitalOcean

- télécharger le projet modèle
- compléter le code terraform
  - changer le nom de votre cluster
  - lancer la création du cluster -> ça prend environ 5 minutes
  - un fichier a été ajouté en local
gg
### Accéder à la dashboard kubernetes

Les roles et les secrets

<!-- https://alexanderzeitler.com/articles/enabling-the-kubernetes-dashboard-for-digitalocean-kubernetes/ -->


wget https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-beta8/aio/deploy/recommended.yaml

ATTENTION PROBLEME de permission car déploie dans un autre namespace que le tuto précédent :/

kubectl apply -f recommended.yaml

kubectl proxy

http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/login

kubectl get secret

kubernetes-dashboard-token-2ddd2

kubectl  -n kubernetes-dashboard describe kubernetes-dashboard-token-2ddd2

token:      eyJhbGciOiJSUzI1NiIsImtpZCI6Im96ZmwxV2MwUHc3SFE3T3A0VGxVVmlYN09xOTFpWUdfSzBmaTVwcTJLZzgifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJrdWJlcm5ldGVzLWRhc2hib2FyZC10b2tlbi0yZGRkMiIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6ImU1YTY3YTkwLWQ5NzEtNGY3Ni05NzViLTA0M2NjMzNhMzFjYSIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlcm5ldGVzLWRhc2hib2FyZDprdWJlcm5ldGVzLWRhc2hib2FyZCJ9.fCR3b-zfNyehCBCzuEzUe2Dd0PmDiHbY3OPMKUJXNsIKv18iVbmSCEbKv2nAj3tbmPDb3JkRl_OjbVFVpHY6K0rybrwLOlroWvSCOAkWLV_4b0NtsJw0wrGsPeJz9arPjJFmZ_-Ol3s3Jgts30GQBLOh_CNwRcBix3ijHEN71CII-EZoBkTVpYHksmnYeBOmH0zqscZYf2UJ-kWE5LRk8OsJmZsHynO2lWBIG-hn5NWXQbGLc1M_2N9xmOQ_1zajvhfaErElctMME-1gx92bVGzpMDpCOAZH42AZm7LIZMIFW11Nt169YesFclgV2GRUk6anms8Hclo019KIaTp2EQ





<!-- 
## Installer un cluster manuellement avec `kubeadm`

Votre cluster comprendra les ressources matérielles suivantes :

- Un noeud "master" : Le noeud master est responsable de la gestion de l'état du cluster. Il exécute Etcd, qui stocke les données de cluster parmi les composants qui planifient les charges de travail sur les noeuds de travail.

- Deux noeuds "worker" : Les noeuds de travail sont les serveurs où vos "workloads" (c'est-à-dire les applications et services conteneurisés) s'exécuteront. Un travailleur continuera à exécuter votre charge de travail une fois qu'il y est affecté, même si le maître tombe en panne une fois la planification terminée. La capacité d'un cluster peut être augmentée par l'ajout de travailleurs.


### Provisionner les machines avec Terraform et digital ocean


### Générer un inventaire dynamique avec Ansible Terraform Provider


```ini
[k8s_masters]
master ansible_host=<master_ip> ansible_user=root

[k8s_workers]
worker1 ansible_host=<worker_1_ip> ansible_user=root
worker2 ansible_host=<worker_2_ip> ansible_user=root

[all:vars]
ansible_python_interpreter=/usr/bin/python3
``` -->