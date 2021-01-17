---
title: Conclusion
weight: 2100
draft: false
---

<!-- A supprimer de l'intro si ici -->
## Points forts de Kubernetes

- Open source et très actif.
- Une communauté très visible et présente dans l'évolution de l'informatique.
- Un standard collectif qui permet une certaine interopérabilité dans le cloud.
- Les _pods_ tendent à se rapprocher plus d'une VM du point de vue de l'application.
- Hébergeable de façon quasi-identique dans le cloud, on-premise ou en mixte.
- Kubernetes a un _flat network_ ce qui permet de faire des choses puissante facilement comme le multi-datacenter.
- K8s est pensé pour la _scalabilité_ et le _calcul distribué_.


## Faiblesses de Kubernetes


- Une difficulté à manier tout ce qui est *stateful*, comme des bases de données
  - …même si les Operators et les CRD (Custom Resources Definitions) permettent de combler cette lacune dans la logique *stateless* de k8s


- Beaucoup de points sont laissés à la décision du fournisseur de cloud ou des admins système :

  - Pas de solution de **stockage** par défaut, et parfois difficile de stocker "simplement" sans passer par les fournisseurs de cloud, ou par une solution de stockage décentralisé à part entière (**Ceph, Gluster, Longhorn**...)
    - …même si ces solutions sont souvent bien intégrées à k8s

  - Beaucoup de solutions de **réseau** qui se concurrencent, demandant un comparatif fastidieux
    - …même si plusieurs leaders émergent comme **Calico, Flannel, Weave ou Cilium**

  - Pas de solution de loadbalancing par défaut : soit on se base sur le fournisseur de cloud, soit on configure [*MetalLB*](https://metallb.universe.tf/) -->

  <!-- - Pas de solution de **reverse proxy (ingress)** standard
    - …même si l'ingress **Nginx** est très utilisé et plus ou moins officiel et que **Traefik** est optimisé pour k8s


## Pour approfondir

### Monitoring et logging
Avec Prometheus et la suite Elastic.

### Déploiement continu
- Exemple de workflow de déploiement continu (CD)
  - par exemple avec Gitlab (possiblement auto-hébergé dans K8s)
  - se connecter à un bastion
  - `git pull`
  - puis `kubectl apply`

### Exemple de stack avancée
La Bitnami Kubernetes Production Runtime (BKPR).
- Monitoring avec Prometheus et Grafana
- Logging avec Elasticsearch, Kibana et Fluentd
- HTTPS ingress avec Nginx, ExternalDNS, Cert-Manager et oauth2_proxy