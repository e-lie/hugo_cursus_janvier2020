---
title: 'TP Kubernetes'
visible: true
---

## Installer **microk8s**

sudo snap install microk8s --edge --classic

# Enable microk8s features
microk8s.enable dashboard

## Installer le client Kubernetes Kubectl

- Ajouter le dépot officiel kubernetes pour Ubuntu : `echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list`
- Mettre à jour les dépôts et installer **kubectl** : `sudo apt update && sudo apt install -y kubectl`

## Configurer kubectl pour se connecter au cluster microk8s

`microk8s.config > ~/.kube/config`


<!-- jx install --provider=kubernetes --external-ip 10.2.3.4 \
--ingress-service=default-http-backend \
--ingress-deployment=default-http-backend \
--ingress-namespace=default \
--on-premise \
--domain=devlab.rs -->





# TP - Commandes k8s

`kubectl get nodes`

`kubectl describe node/<votrenode>`

`kubectl get all`

`kubectl get namespaces`

`kubectl get all -n kube-system`

`kubectl get all --all-namespaces`

`kubectl describe namespace/kube-system`