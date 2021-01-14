---
draft: true
---

```bash
minikube start --kubernetes-version=v1.7.0 --extra-config=apiserver.Authorization.Mode=RBAC

kubectl create clusterrolebinding add-on-cluster-admin --clusterrole=cluster-admin --serviceaccount=kube-system:default

minikube dashboard
```
Élie Gavoty, [14.01.21 17:43]
En vrai pour faire un TP RBAC avec minikube il faut

1. créer trois connections minikube dans ~/.kube/config
une en mode cluster-admin, une en mode admin sur un namespace et une en mode user avec un rolebinding
2. et faire des kubectl auth can-i pour différents cas
Donc switcher de contexte

### Ressources 
https://medium.com/@HoussemDellai/rbac-with-kubernetes-in-minikube-4deed658ea7b
https://docs.bitnami.com/tutorials/configure-rbac-in-your-kubernetes-cluster/