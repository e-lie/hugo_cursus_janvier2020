---
draft: false
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

![](../../images/kubernetes/nodeport.png?width=400px)
*Crédits à [Ahmet Alp Balkan](https://medium.com/@ahmetb) pour les schémas*

- `LoadBalancer`: expose le service en externe à l’aide d'un Loadbalancer de fournisseur de cloud. Les services NodePort et ClusterIP, vers lesquels le Loadbalancer est dirigé sont automatiquement créés.

![](../../images/kubernetes/loadbalancer.png?width=400px)
*Crédits [Ahmet Alp Balkan](https://medium.com/@ahmetb)*

### Fournir des services LoadBalancer on premise avec `MetalLB`

Dans un cluster managé provenant d'un fournisseur de cloud, la création d'un objet Service Lodbalancer entraine le provisionning d'une nouvelle machine de loadbalancing à l'extérieur du cluster avec une IPv4 publique grâce à l'offre d'IaaS du provideur (impliquant des frais supplémentaires).

Cette intégration n'existe pas par défaut dans les clusters de dev comme minikube ou les cluster on premise (le service restera pending et fonctionnera comme un NodePort). Le projet [*MetalLB*](https://metallb.universe.tf/) cherche à y remédier en vous permettant d'installer un loadbalancer directement dans votre cluster en utilisant une connexion IP classique ou BGP pour la haute disponibilité.


## Les objets Ingresses

![](../../images/kubernetes/ingress.png)
*Crédits [Ahmet Alp Balkan](https://medium.com/@ahmetb)*


Un Ingress est un objet pour gérer dynamiquement le **reverse proxy** HTTP/HTTPS dans Kubernetes. Documentation: https://kubernetes.io/docs/concepts/services-networking/ingress/#what-is-ingress

Exemple de syntaxe d'un ingress:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-wildcard-host
spec:
  rules:
  - host: "domain1.bar.com"
    http:
      paths:
      - pathType: Prefix
        path: "/bar"
        backend:
          service:
            name: service1
            port:
              number: 80
      - pathType: Prefix
        path: "/foo"
        backend:
          service:
            name: service2
            port:
              number: 80
  - host: "domain2.foo.com"
    http:
      paths:
      - pathType: Prefix
        path: "/"
        backend:
          service:
            name: service3
            port:
              number: 80
```

Pour pouvoir créer des objets ingress il est d'abord nécessaire d'installer un **ingress controller** dans le cluster:

- Il s'agit d'un déploiement conteneurisé d'un logiciel de reverse proxy (comme nginx) et intégré avec l'API de kubernetes
- Le controlleur agit donc au niveau du protocole HTTP et doit lui-même être exposé (port 80 et 443) à l'extérieur, généralement via un service de type LoadBalancer.
- Le controlleur redirige ensuite vers différents services (généralement configurés en ClusterIP) qui à leur tour redirigent vers différents ports sur les pods selon l'URL del a requête.

Il existe plusieurs variantes d'**ingress controller**:

- Un ingress basé sur Nginx plus ou moins officiel à Kubernetes et très utilisé: https://kubernetes.github.io/ingress-nginx/
- Un ingress Traefik optimisé pour k8s.
- il en existe d'autres : celui de payant l'entreprise Nginx, Contour, HAProxy...

Chaque provider de cloud et flavour de kubernetes est légèrement différent au niveau de la configuration du controlleur ce qui peut être déroutant au départ:

- minikube permet d'activer l'ingress nginx simplement (voir TP)
- autre example: k3s est fourni avec traefik configuré par défaut
- On peut installer plusieurs `ingress controllers` correspondant à plusieurs `IngressClasses`

Comparaison des controlleurs: <https://medium.com/flant-com/comparing-ingress-controllers-for-kubernetes-9b397483b46b>

## Gestion dynamique des certificats à l'aide de `certmanager`

`Certmanager` est une application kubernetes (un `operator`) plus ou moins officielle  capable de générer automatiquement des certificats TLS/HTTPS pour nos ingresses.

- Documentation d'installation: https://cert-manager.io/docs/installation/kubernetes/
- Tutorial pas à pas pour générer un certificat automatiquement avec un ingress et letsencrypt: https://cert-manager.io/docs/tutorials/acme/ingress/

Exemple de syntaxe d'un ingress utilisant `certmanager`:

```yaml
apiVersion: networking.k8s.io/v1 
kind: Ingress
metadata:
  name: kuard
  annotations:
    kubernetes.io/ingress.class: "nginx"    
    cert-manager.io/issuer: "letsencrypt-prod"
spec:
  tls:
  - hosts:
    - example.example.com
    secretName: quickstart-example-tls
  rules:
  - host: example.example.com
    http:
      paths:
      - path: /
        pathType: Exact
        backend:
          service:
            name: kuard
            port:
              number: 80
```

## Le mesh networking et les *service meshes*

Un **service mesh** est un type d'outil réseau pour connecter un ensemble de pods, généralement les parties d'une application microservices de façon encore plus intégrée que ne le permet Kubernetes.

En effet opérer une application composée de nombreux services fortement couplés discutant sur le réseau implique des besoins particuliers en terme de routage des requêtes, sécurité et monitoring qui nécessite l'installation d'outils fortement dynamique autour des nos conteneurs.

Un exemple de service mesh est `https://istio.io` qui, en ajoutant en conteneur "sidecar" à chacun des pods à supervisés, ajoute à notre application microservice un ensemble de fonctionnalités d'intégration très puissant. 

## CNI (container network interface) : Les implémentations du réseau Kubernetes

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
