---
draft: false
title: Cours 5 - Le réseau dans Kubernetes
---

Les solutions réseau dans Kubernetes ne sont pas standard.
Il existe plusieurs façons d'implémenter le réseau.

## Les services

<!-- FIXME: SCHEMAS

https://medium.com/google-cloud/kubernetes-nodeport-vs-loadbalancer-vs-ingress-when-should-i-use-what-922f010849e0
 -->

Les Services sont de trois types principaux :

- `ClusterIP`: expose le service **sur une IP interne** au cluster appelée ClusterIP. Les autres pods peuvent alors accéder au service mais pas l'extérieur.

- `NodePort`: expose le service depuis l'IP publique de **chacun des noeuds du cluster** en ouvrant port directement sur le nœud, entre 30000 et 32767. Cela permet d'accéder aux pods internes répliqués. Comme l'IP est stable on peut faire pointer un DNS ou Loadbalancer classique dessus.
- 
![](../../public/images/kubernetes/nodeport.png)
*Crédits à [Ahmet Alp Balkan](https://medium.com/@ahmetb) pour les schémas*

- `LoadBalancer`: expose le service en externe à l’aide d'un Loadbalancer de fournisseur de cloud. Les services NodePort et ClusterIP, vers lesquels le Loadbalancer est dirigé sont automatiquement créés.

![](../../public/images/kubernetes/loadbalancer.png)
*Crédits [Ahmet Alp Balkan](https://medium.com/@ahmetb)*

## Les implémentations du réseau

Beaucoup de solutions de réseau qui se concurrencent, demandant un comparatif un peu fastidieux.

  - plusieurs solutions très robustes
  - diffèrent sur l'implémentation : BGP, réseau overlay ou non (encapsulation VXLAN, IPinIP, autre)
  - toutes ne permettent pas d'appliquer des **NetworkPolicies** : l'isolement et la sécurité réseau
  - peuvent parfois s'hybrider entre elles (Canal = Calico + Flannel)
  <!-- - ou être plus ou moins compatible avec des *service meshes* (Envoy, Istio) -->
  - ces implémentations sont souvent concrètement des *DaemonSets* : des pods qui tournent dans chacun des nodes de Kubernetes

- Calico, Flannel, Weave ou Cilium sont très employées et souvent proposées en option par les fournisseurs de cloud
- Cilium a la particularité d'utiliser la technologie eBPF de Linux qui permet une sécurité et une rapidité accrue

Comparaisons :
- <https://www.objectif-libre.com/fr/blog/2018/07/05/comparatif-solutions-reseaux-kubernetes/>
- <https://rancher.com/blog/2019/2019-03-21-comparing-kubernetes-cni-providers-flannel-calico-canal-and-weave/>

## Les network policies
<!-- Schema static/images/kubernetes/ahmetb_networkpolicies.gif ? CREDITER ou refaire -->
**Par défaut, les pods ne sont pas isolés au niveau réseau** : ils acceptent le trafic de n'importe quelle source.

Les pods deviennent isolés en ayant une NetworkPolicy qui les sélectionne. Une fois qu'une NetworkPolicy (dans un certain namespace) inclut un pod particulier, ce pod rejettera toutes les connexions qui ne sont pas autorisées par cette NetworkPolicy.

## Le loadbalancing

Le loadbalancing permet de balancer le trafic à travers plusieurs nodes Kubernetes.

Pas de solution de loadbalancing par défaut :
- soit on se base sur ce que le fournisseur de cloud propose,
- soit on configure [*MetalLB*](https://metallb.universe.tf/), seule alternative en dehors des fournisseurs de cloud

## Les ingresses

![](../../public/images/kubernetes/ingress.png)
*Crédits [Ahmet Alp Balkan](https://medium.com/@ahmetb)*


Un Ingress est un objet pour gérer le **reverse proxy** dans Kubernetes : il a besoin d'un **ingress controller** installé sur le cluster, qui agit donc au niveau du protocole HTTP et écoute sur un port (`80` ou `443` généralement), pour pouvoir rediriger vers différents services (qui à leur tour redirigent vers différents ports sur les pods) selon l'URL.

- Un ingress basé sur Nginx plus ou moins officiel à Kubernetes et très utilisé
- Traefik est optimisé pour k8s
- il en existe d'autres : celui de l'entreprise Nginx, Istio, Contour, HAProxy....

Comparaison : <https://medium.com/flant-com/comparing-ingress-controllers-for-kubernetes-9b397483b46b>


## Le mesh networking et les *service meshes*
Envoy et Istio sont des *service meshes*.
- Il faut y penser comme des super-ingresses : des proxy qui font beaucoup plus que du reverse proxy
  - en particulier : ajouter des fonctions de monitoring et de sécurité
  <!-- - ils s'adaptent plus ou moins bien à une solution réseau particulière -->


## Ressources sur le réseau

### Vidéos

- [Kubernetes Services networking](https://www.youtube.com/watch?v=NFApeJRXos4&list=PLoWxE_5hnZUZMWrEON3wxMBoIZvweGeiq&index=4)

---

- Istio : https://www.digitalocean.com/community/tutorials/an-introduction-to-service-meshes

## Videos
Why you need to use metallb: https://www.youtube.com/watch?v=Ytc24Y0YrXE
### Réseau

#### Vidéos de Calico
Kubernetes networking on Azure
https://www.youtube.com/watch?v=JyLtg_SJ1lo&list=PLoWxE_5hnZUZMWrEON3wxMBoIZvweGeiq&index=2

Kubernetes Services networking
https://www.youtube.com/watch?v=NFApeJRXos4&list=PLoWxE_5hnZUZMWrEON3wxMBoIZvweGeiq&index=4

Kubernetes Ingress networking
https://www.youtube.com/watch?v=40VfZ_nIFWI&list=PLoWxE_5hnZUZMWrEON3wxMBoIZvweGeiq&index=5


<!-- TODO: Mettre en forme ressources -->
<!-- 

- Reprendre ça lentement: https://kubernetes.io/fr/docs/concepts/services-networking/service/

- et/ou ça : https://medium.com/google-cloud/kubernetes-nodeport-vs-loadbalancer-vs-ingress-when-should-i-use-what-922f010849e0


Pas confondre ingress et egress comme NetworkPolicy et Ingress resource


Toutes les networking solutions : https://kubernetes.io/docs/concepts/cluster-administration/networking/

From dumb iptables or GCE route stuff to Cilium or Canal (Calico + Flannel)

- Canal deprecated: should configure both (to confirm)

- Is kubenet the default network solution of k8s?

- prendre le gif de https://github.com/ahmetb/kubernetes-network-policy-recipes

- simple official docs are sometimes outdated...


- MetalLB is simply a way to use LoadBalancer when not captive from big Google Microsoft or Amazon cloud (no official K8s solution) : https://metallb.universe.tf/ 
- 
- 
- 
- 

## Network Plugins
Network plugins in Kubernetes come in a few flavors:

* CNI plugins: adhere to the Container Network Interface (CNI) specification, designed for interoperability.
* Kubenet plugin: implements basic cbr0 using the bridge and host-local CNI plugins

---

- **Network policies** : Faire un petit tableau avec Cilium / Calico / Kubenet etc et ce qu'ils font et si CNI ou non
- Dire que pas confondre avec **services meshes** Istio / Pilot etc.
- Ni avec **ingress controllers** !!! https://medium.com/flant-com/comparing-ingress-controllers-for-kubernetes-9b397483b46b


- Service Topology enables a service to route traffic based upon the Node topology of the cluster : https://kubernetes.io/docs/concepts/services-networking/service-topology/


- Rappel layer 2/3 layer 4 layer 7 ?

---

## Pour ressources :
- https://docs.projectcalico.org/networking/determine-best-networking
- https://metallb.universe.tf/


Pitch MetalLB :
Kubernetes does not offer an implementation of network load-balancers (Services of type LoadBalancer) for bare metal clusters. The implementations of Network LB that Kubernetes does ship with are all glue code that calls out to various IaaS platforms (GCP, AWS, Azure…). If you’re not running on a supported IaaS platform (GCP, AWS, Azure…), LoadBalancers will remain in the “pending” state indefinitely when created.

Bare metal cluster operators are left with two lesser tools to bring user traffic into their clusters, “NodePort” and “externalIPs” services. Both of these options have significant downsides for production use, which makes bare metal clusters second class citizens in the Kubernetes ecosystem.

MetalLB aims to redress this imbalance by offering a Network LB implementation that integrates with standard network equipment, so that external services on bare metal clusters also “just work” as much as possible.


-->
