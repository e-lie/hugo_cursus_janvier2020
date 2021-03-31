---
draft: true
title: 08 - Cours - Le réseau dans Kubernetes
weight: 2052
---

Les solutions réseau dans Kubernetes ne sont pas standard.
Il existe plusieurs façons d'implémenter le réseau.

## Rappel, les objets Services

<!-- 
https://medium.com/google-cloud/kubernetes-nodeport-vs-loadbalancer-vs-ingress-when-should-i-use-what-922f010849e0
 -->

Les Services sont de trois types principaux :

- `ClusterIP`: expose le service **sur une IP interne** au cluster appelée ClusterIP. Les autres pods peuvent alors accéder au service mais pas l'extérieur.

- `NodePort`: expose le service depuis l'IP publique de **chacun des noeuds du cluster** en ouvrant port directement sur le nœud, entre 30000 et 32767. Cela permet d'accéder aux pods internes répliqués. Comme l'IP est stable on peut faire pointer un DNS ou Loadbalancer classique dessus.

![](../../images/kubernetes/nodeport.png)
*Crédits à [Ahmet Alp Balkan](https://medium.com/@ahmetb) pour les schémas*

- `LoadBalancer`: expose le service en externe à l’aide d'un Loadbalancer de fournisseur de cloud. Les services NodePort et ClusterIP, vers lesquels le Loadbalancer est dirigé sont automatiquement créés.

![](../../images/kubernetes/loadbalancer.png)
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

![](../../images/kubernetes/ahmetb_networkpolicies.gif)
*Crédits [Ahmet Alp Balkan](https://medium.com/@ahmetb)*

**Par défaut, les pods ne sont pas isolés au niveau réseau** : ils acceptent le trafic de n'importe quelle source.

Les pods deviennent isolés en ayant une NetworkPolicy qui les sélectionne. Une fois qu'une NetworkPolicy (dans un certain namespace) inclut un pod particulier, ce pod rejettera toutes les connexions qui ne sont pas autorisées par cette NetworkPolicy.

- Des exemples de Network Policies : [Kubernetes Network Policy Recipes](https://github.com/ahmetb/kubernetes-network-policy-recipes)

## Le loadbalancing

Le loadbalancing permet de balancer le trafic à travers plusieurs nodes Kubernetes.

Pas de solution de loadbalancing par défaut :
- soit on se base sur ce que le fournisseur de cloud propose,
- soit on configure [*MetalLB*](https://metallb.universe.tf/), seule alternative en dehors des fournisseurs de cloud

## Les objets Ingresses

![](../../images/kubernetes/ingress.png)
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

- Documentation officielle : <https://kubernetes.io/fr/docs/concepts/services-networking/service/>

- [An introduction to service meshes - DigitalOcean](ttps://www.digitalocean.com/community/tutorials/an-introduction-to-service-meshes)

- [Kubernetes NodePort vs LoadBalancer vs Ingress? When should I use what?](https://medium.com/google-cloud/kubernetes-nodeport-vs-loadbalancer-vs-ingress-when-should-i-use-what-922f010849e0)

- [Determine best networking option - Project Calico](https://docs.projectcalico.org/networking/determine-best-networking)

- [Doc officielle sur les solutions de networking](https://kubernetes.io/docs/concepts/cluster-administration/networking/)

### Vidéos

Des vidéos assez complètes sur le réseau, faites par Calico :
- [Kubernetes Ingress networking](https://www.youtube.com/watch?v=40VfZ_nIFWI&list=PLoWxE_5hnZUZMWrEON3wxMBoIZvweGeiq&index=5)
- [Kubernetes Services networking](https://www.youtube.com/watch?v=NFApeJRXos4&list=PLoWxE_5hnZUZMWrEON3wxMBoIZvweGeiq&index=4)
- [Kubernetes networking on Azure](https://www.youtube.com/watch?v=JyLtg_SJ1lo&list=PLoWxE_5hnZUZMWrEON3wxMBoIZvweGeiq&index=2)

Sur MetalLB, les autres vidéos de la chaîne sont très bien :
- [Why you need to use MetalLB -  Adrian Goins](https://www.youtube.com/watch?v=Ytc24Y0YrXE)


<!-- 
Pas confondre ingress et egress comme NetworkPolicy et Ingress resource -->


<!-- - MetalLB is simply a way to use LoadBalancer when not captive from big Google Microsoft or Amazon cloud (no official K8s solution) : https://metallb.universe.tf/  -->

<!-- 
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
 -->

<!--
Pitch MetalLB :
Kubernetes does not offer an implementation of network load-balancers (Services of type LoadBalancer) for bare metal clusters. The implementations of Network LB that Kubernetes does ship with are all glue code that calls out to various IaaS platforms (GCP, AWS, Azure…). If you’re not running on a supported IaaS platform (GCP, AWS, Azure…), LoadBalancers will remain in the “pending” state indefinitely when created.

Bare metal cluster operators are left with two lesser tools to bring user traffic into their clusters, “NodePort” and “externalIPs” services. Both of these options have significant downsides for production use, which makes bare metal clusters second class citizens in the Kubernetes ecosystem.

MetalLB aims to redress this imbalance by offering a Network LB implementation that integrates with standard network equipment, so that external services on bare metal clusters also “just work” as much as possible.


-->
