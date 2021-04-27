---
title: TP opt. - Les ingresses
draft: true
weight: 2080
---

## Ressources sur les ingresses

### Minikube
https://kubernetes.io/docs/tasks/access-application-cluster/ingress-minikube/

### Azure AKS
https://docs.microsoft.com/fr-fr/azure/aks/ingress-basic

### Scaleway (avec Traefik)
https://www.scaleway.com/en/docs/using-a-load-balancer-to-expose-your-kubernetes-kapsule-ingress-controller-service/


## DNS

Pour les DNS, 3 solutions :
- en local, éditer `/etc/hosts`
- sur Internet, ne pas l'utiliser et faire un Ingress avec l'adresse IP comme hostname
- sur Internet, utiliser <https://netlib.re> pour configurer un DNS avec un domaine en `netlib.re`