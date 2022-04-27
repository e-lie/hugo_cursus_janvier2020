---
title: 14 -Réseau Kubernetes avancé 
draft: false
weight: 2100
---

## Le réseau Pod-to-Pod et les CNI plugins

La base du réseau Kubernetes consiste dans les communication entre les pods car tout est pod dans un cluster (même les éléments réseau comme les ingress et souvent le kube-proxy)

Sous Linux les conteneurs (quelle que soit la runtime choisie, Docker, containerd etc) tirent partie d'une fonctionnalité du noyau appelée `Network namespace` c'est à dire des espaces réseau isolés du réseau principal du serveur grâce à une forme de virtualisation. On peut s'en rendre compte en créant soit même des interface vituelle dans un namespace (cf https://linuxhint.com/use-linux-network-namespace/).

Les pods sont composés de plusieurs conteneurs qui partagent le même namespace réseau. Au moment de la création d'un pod, le premier conteneur créé est appelé `pause`, il est vide mais sert de support à la création de l'interface réseau virtuelle pour le pod. Les conteneurs démarrés ensuite récupèrent cette interface commune. On peut constater la création de ce conteneur en utilisant la cli docker ou ctr.

Dans Kubernetes l'interface virtuelle d'un pod est créée et routée automatiquement par le plugin CNI (Container Network Interface, une norme standard qui permet à toutes les runtime de conteneurs et tous les plugins d'être compatibles.).

Ce plugin doit être présent dans le cluster pour pouvoir automatiser la communication entre les pods. À l'installation (manuelle) du cluster on configure donc un CIDR pour les pods généralement un /16, et un plugin CNI.

Il existe de nombreux plugin CNI du plus simple au plus sophistiqué pour pouvoir répondre à des edge cases (notamment la communication interne/externe du cluster) des problématiques de performance, sécurité, observabilité  (le meilleur si vous avez le choix est probablement Cilium: https://cilium.io avec son interface hubble). On peut même implémenter un plugin simple en bash a des fins pédagogiques : https://www.altoros.com/blog/kubernetes-networking-writing-your-own-simple-cni-plug-in-with-bash/.

Une fois cette configuration mise en place les pods sont capables de communiquer avec tous les autres pods du cluster sans NAT, c'est à dire sans modification de l'adresse IP au cours de la communication.

## Traffic réseau "North-South-East-West"

- Lorsque vous avez un cluster Kubernetes avec vos services en cours d'exécution, comment l'utilisateur final extérieur ou un service extérieur entrent-t-ils en contact avec votre service ?. C'est ce que l'on appelle le trafic nord, c'est-à-dire une requête extérieure qui arrive sur votre cluster. Pour gérer ce trafic entrant, vous pouvez mettre en place un loadbalancer. Un loadbalancer kubernetes (LB) donne une seule adresse IP externe qui transmettra tout votre trafic entrant à votre service.

- Le trafic sud désigne les requêtes qui quittent votre cluster (vous retournez peut-être une réponse à une requête, ou vous devez appeler une API externe). Bien qu'il existe un type de ressource d'entrée (Ingress) fourni par l'API Kubernetes, il n'y a pas de type de ressource de sortie. <!-- C'est là que quelque chose comme la ressource Egress d'Istio est utile. -->

Le trafic est-ouest désigne la communication de vos services entre eux. D'une manière générale, il s'agit du trafic au sein d'un datacenter de serveur à serveur.

## Communication inter-services avec le kube-proxy

Une fois que l'on a mis en place un moyen de communication inter-pod la communication est-ouest de notre cluster n'est pas encore opérationnelle. En effet Kubernetes étant un environnement dynamique avec des pods crées et détruit automatiquement par des Deployment ou autre controller, gérer les IP des pods manuellement est en réalité impraticable. Pour cela nous avons besoin de la gestion automatique d'une ip virtuelle associée fournissant le loadbalancing proposé par les resources de type Service.

Cette gestion d'IP virtuelle est réalisée par le composant Kubernetes appelé `kube-proxy`. Le kube-proxy est généralement installé dans le cluster en temps que pod privilégié sur chaque noeud (mais peut également être un service systemd) et manipule `iptable`. On peut s'en rendre compte par exemple avec la commande `sudo iptables-save | grep KUBE | grep "kubernetes-dashboard" # ou autre nom de service`.

Ainsi, traditionnellement `kube-proxy` créé dynamiquement des règles `iptable` qui indiquent que tout paquet venant du CIDR des pods et à destination de l'IP virtuelle du service (en réalité l'IP de l'objet endpoint) doit être redirigé aléatoirement vers l'un des pods de backend d'un groupe désignés comme nous l'avons vu grace à un selecteur de labels.

kube-proxy peut également être configuré pour utiliser à la place de iptable la fonctionnalité d'IP virtuelle du noyau Linux appelé IPVS. Cela amène surtout de meilleures performances mais seulement dans le cas d'un très grand nombre de pods (<100 000).

<!-- # Découverte de service avec kube-DNS


Deux types de services moins connus :

On peut également créé des service de type `headless` avec `ClusterIP=None` pour implémenter soit même d'une manière ou d'une autre ce load balancing

Un 4e type existe, il est moins utilisé :
- `ExternalName`: utilise CoreDNS pour mapper le service au contenu du champ `externalName` (par exemple `foo.bar.example.com`), en renvoyant un enregistrement `CNAME` avec sa valeur. Aucun proxy d’aucune sorte n’est mis en place. -->


<!-- TODO DNS

TODO move and extend Network Policies

TODO move and extend Service Mesh -->


## Le mesh networking et les *service meshes*

Un **service mesh** est un type d'outil réseau pour connecter un ensemble de pods, généralement les parties d'une application microservices de façon encore plus intégrée que ne le permet Kubernetes.

En effet opérer une application composée de nombreux services fortement couplés discutant sur le réseau implique des besoins particuliers en terme de routage des requêtes, sécurité et monitoring qui nécessite l'installation d'outils fortement dynamique autour des nos conteneurs.

Un exemple de service mesh est `https://istio.io` qui, en ajoutant en conteneur "sidecar" à chacun des pods à supervisés, ajoute à notre application microservice un ensemble de fonctionnalités d'intégration très puissant. 

## Les network policies : des firewalls dans le cluster

![](../../images/kubernetes/ahmetb_networkpolicies.gif)
*Crédits [Ahmet Alp Balkan](https://medium.com/@ahmetb)*

**Par défaut, les pods ne sont pas isolés au niveau réseau** : ils acceptent le trafic de n'importe quelle source.

Les pods deviennent isolés en ayant une NetworkPolicy qui les sélectionne. Une fois qu'une NetworkPolicy (dans un certain namespace) inclut un pod particulier, ce pod rejettera toutes les connexions qui ne sont pas autorisées par cette NetworkPolicy.

- Des exemples de Network Policies : [Kubernetes Network Policy Recipes](https://github.com/ahmetb/kubernetes-network-policy-recipes)

## Ressources sur le réseau

- Documentation officielle : <https://kubernetes.io/fr/docs/concepts/services-networking/service/>

- [An introduction to service meshes - DigitalOcean](ttps://www.digitalocean.com/community/tutorials/an-introduction-to-service-meshes)

- [Kubernetes NodePort vs LoadBalancer vs Ingress? When should I use what?](https://medium.com/google-cloud/kubernetes-nodeport-vs-loadbalancer-vs-ingress-when-should-i-use-what-922f010849e0)

- [Determine best networking option - Project Calico](https://docs.projectcalico.org/networking/determine-best-networking)

- [Doc officielle sur les solutions de networking](https://kubernetes.io/docs/concepts/cluster-administration/networking/)




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
