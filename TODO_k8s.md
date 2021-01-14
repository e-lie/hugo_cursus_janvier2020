# TODO

- ajouter secrets and configmaps!!!

- panachage entre TP et cours

# 2 TP de plus sur K8S pur : secrets +  RBAC non ? PVC et storage classes, daemonset... (postgres avec helm ?), HPA ?
# 1 TP sur Azure (registries ? l'app cheloue ? Terraform ?) + les TP prémâchés de la doc Azure

# 1 TP plus dev ? TP Docker surtout

- imprimer des tas de schémas et de fiches plastifiées sur les concepts de k8s (idéalement qui "s'emboîtent" façon puzzle) et les distribuer dès le début, recréer les chémas avec ça au tableau

# TP registry : https://www.linuxtechi.com/setup-private-docker-registry-kubernetes/

- Faire du docker ?
- Faire une intro à K8S en commençant par Docker swarm ?
- Intro DevOps
- Rappels YAML
## Idées TP
- rbac 
- multinode avec terraform (ou k3sup ?), ou simplement https://microk8s.io/docs/clustering ?
- gestion des secrets
- gestion des configmaps
- storageclasses
- ingresses
- horizontal pod autoscaling avec montée en charge
- daemonsets
- statefulsets (chart postgres ?)
- 
---



# Notes en vrac


<!-- - imprimer des awesome-* surlignés et commentés
https://github.com/veggiemonk/awesome-docker#security -->
<!-- 
## Un netlify commun sur lequel on itère
- dépasser la juxtaposition de modules :
  - intro au cursus : le but est de faire un PaaS (ex. : pbmatique cloud hybride, multi cluster), et non seulement "une CI/CD correcte"
  - on part sur du ansible : infra as code et déploiement, l'approche bas niveau, pour saisir l'idempotence, infra as code, versioning (git), plutôt pet que cattle
  - sur les bases de docker : concepts "devops" : conteneurisation, cattle, immutabilité
  - k8s+rancher :
    - concepts k8s : théorie, liste de concepts, petites manips avec docker run rancher et rancher utilisé comme IDE pour du déploiement k8s, GUI k8s
    - rancher : problématiques de PaaS, cloud hybride / multi cluster et workloads + multi cluster apps (enhanced helm w/ rancher), auth et multiuser (RBAC), ouverture vers pbmatiques réseau distribués (canal/flannel/calico/istio) et volume distribué (ceph/gluster), debug des accès disque et réseau avec truc de tracing poussé par rancher jaeger -->


<!-- ## Scénarios TP fil rouge :

- Ansible : (Elie)
  - LXD (à la main) : lab Ansible ok
  - Ansible ad-hoc ok
  - Application Flask avec playbook (+ playbook LXD donné) ok : v0 hello world
  - Reprendre le playbook pour faire un rôle
    - déployer MySQL et intégrer flask v1 MySQL
  - Déploiement orchestré avec LXD Haproxy, lxc mysql et lxc flask : loadbalancer et backends (LXD)

- Docker : (Hadrien)
  - tp1 existant à améliorer (pas debian sleep) et à la fin portainer
  - TP2 : partir d'un dockerfile du projet knowledge/search d'hadrien qui utiliserait mysql (flask v1) : améliorer TP 2 d'Elie existant et changer l'app, et gestion volumes et réseau en CLI pou rlancer mysql
  -  TP3 : docker-compose avec flask v1 + mysql, puis la passer en elk (flask v2)
  -  TP4 : Swarm vite fait (app web qui loadbalance où tu sais quel nœud t'a servi ou voting app) et intro à K8s en montrant les limites de swarm -->

- Kubernetes + Rancher
  - Concepts K8S 
  - petites manips avec docker run rancher et rancher utilisé comme IDE pour du déploiement k8s, GUI k8s
  - chart helm
  - déployer une CI en mettant non seulement gitlab dans k8s mais aussi utiliser un node comme CI
  - rancher intro à la logique : 
     problématiques de PaaS, cloud hybride / multi cluster et workloads + multi cluster apps (enhanced helm w/ rancher), auth et multiuser (RBAC)
  - ouverture vers pbmatiques réseau distribués (canal/flannel/calico/istio) et volume distribué (ceph/gluster), debug des accès disque et réseau avec truc de tracing poussé par rancher jaeger

<!-- TP wordpress simple : https://github.com/kubernetes/examples/tree/master/mysql-wordpress-pd -->

# Ressources K8S

## Cours

Bouquin Learn openshift très cool

## TP

https://github.com/GoogleCloudPlatform/kubernetes-workshops

<!-- ### Gitlab
* https://rancher.com/blog/2019/connecting-gitlab-autodevops-authorized-cluster-endpoints/
* https://docs.gitlab.com/12.8/runner/install/kubernetes.html
* https://docs.gitlab.com/charts/installation/index.html
* https://blog.kubernauts.io/ci-cd-with-rancher-pipelines-and-self-hosted-gitlab-b17248294fda

https://medium.com/@abenahmed1/pipeline-de-ci-cd-simplifi%C3%A9-dans-un-cluster-kubernetes-avec-gitlab-et-rancher-602a01029bae

Il y a aussi Rancher's own CI/CD pipeline: https://rancher.com/docs/rancher/v2.x/en/project-admin/pipelines/


NOTE: Gitlab operator? https://docs.gitlab.com/charts/installation/operator.html


  - TP : Gitlab-CI runner + gitlab ? https://rancher.com/blog/2019/connecting-gitlab-autodevops-authorized-cluster-endpoints/
  - https://docs.gitlab.com/12.8/runner/install/kubernetes.html
  - https://docs.gitlab.com/charts/installation/index.html

-->

## General

* https://kubernetes.io/blog/2018/07/18/11-ways-not-to-get-hacked/
* https://coreos.com/operators/
* https://kubernetes.io/docs/concepts/extend-kubernetes/operator/
<!-- ## Kubernetes avec DB répliquée PostgreSQL
* https://github.com/CrunchyData/postgres-operator
* https://access.crunchydata.com/documentation/postgres-operator/latest/run-ha-postgresql-rancher-kubernetes-engine/
* https://portworx.com/
* https://portworx.com/ha-postgresql-kubernetes/
* https://hackernoon.com/postgresql-cluster-into-kubernetes-cluster-f353cde212de
* https://info.crunchydata.com/blog/crunchy-postgresql-operator-with-rook-ceph-storage
* https://rook.io/docs/rook/v1.2/quickstart.html -->



* https://vitess.io/

<!-- ## TODO: à étudier

https://github.com/kubernetes/kubernetes/blob/master/cluster/kube-up.sh
https://rexray.readthedocs.io/en/stable/user-guide/storage-providers/ceph/#ceph-rbd

* https://linkerd.io/ -->

## Sécurité K8S
super tp pour sécuriser la stack elastic sur k8s avec cilium : http://docs.cilium.io/en/stable/gettingstarted/elasticsearch/

* https://github.com/falcosecurity/falco
* vidéos youtube + ressources rootme
* APT the future of k8s attacks
* favs github

# Rancher
* a project is a group of namespaces: https://rancher.com/docs/rancher/v2.x/en/cluster-admin/projects-and-namespaces/


- Ajout part sécurité avec le TP de rootme en mode simplifié : guider la partie reverse shell ou faire en mode démo

- Faire une partie des slides qu'il y a dans ressources

- AJouts schemas networks avec appui sur cours lexsi

- me faire kubernetes the hard way 

- parler de Autoscaling des pods et des clusters


- smoketest : https://github.com/kelseyhightower/kubernetes-the-hard-way/blob/master/docs/13-smoke-test.md

- check https://github.com/projectcontour/contour


- mettre la bd d'initiation à K8s de Google ?


- comprendre différentes CNI et cce qui est mis en prod


- ajouter un tuto CRD


## Idées TD :
- https://github.com/dockersamples/k8s-wordsmith-demo
- https://github.com/microservices-demo/microservices-demo
- https://docs.microsoft.com/fr-fr/azure/aks/tutorial-kubernetes-app-update
- https://docs.microsoft.com/fr-fr/azure/aks/tutorial-kubernetes-scale
- reprendre éléments de tutos katakoda officiels : https://kubernetes.io/docs/tutorials/kubernetes-basics/update/update-interactive/
- https://kubernetes.io/docs/tutorials/configuration/configure-redis-using-configmap/
- https://kubernetes.io/docs/tutorials/stateful-application/mysql-wordpress-persistent-volume/
- k8s elastic + petclinic : https://www.elastic.co/fr/blog/kubernetes-observability-tutorial-k8s-cluster-setup-demo-app-deployment
- https://github.com/michaelhyatt/k8s-o11y-workshop (voir ci dessus)
- istio + bookinfo : https://istio.io/latest/docs/examples/bookinfo/ https://www.digitalocean.com/community/tutorials/an-introduction-to-service-meshes
---

- schémas pods et nodes : https://kubernetes.io/docs/tutorials/kubernetes-basics/explore/explore-intro/


- parler des PersistentVolumeClaims et des plugins

- tuto rolling update


- tuto rancher


- kustomization

- on fait un docker stack deploy --orchestrator=kubernetes de transition ?


- INSTALL LA VERSION DE HOBBYKUBE + KBPR avec Terraform automatique

- weavenet par "défaut" donc ?

- trouver outil en ligne pour faire quick polls qd formation àdistance
- donner olein d'apps d'exemple pour toutes les propriétés intéressantes + parler des CRD via ELasticsearches par exemple ? ou des prometheuses ou etcd operators

- Ingresses : Envoy-based comme Istio (qui est bien plus), standard official K8s ingress based on nginx, or Traefik or HAProxy for example https://medium.com/flant-com/comparing-ingress-controllers-for-kubernetes-9b397483b46b
Ne pas confondre avec network solutions comme Cilium qui sont un bunch of other things (?)

- Reprendre ça lentement: https://kubernetes.io/fr/docs/concepts/services-networking/service/

- et/ou ça : https://medium.com/google-cloud/kubernetes-nodeport-vs-loadbalancer-vs-ingress-when-should-i-use-what-922f010849e0

- enseigner NetworkPolicy et la networking solution qui le suit : By default, pods are non-isolated; they accept traffic from any source. Pods become isolated by having a NetworkPolicy that selects them. Once there is any NetworkPolicy in a namespace selecting a particular pod, that pod will reject any connections that are not allowed by any NetworkPolicy. 

Pas confondre ingress et egress comme NetworkPolicy et Ingress resource


Toutes les networking solutions : https://kubernetes.io/docs/concepts/cluster-administration/networking/

From dumb iptables or GCE route stuff to Cilium or Canal (Calico + Flannel)

- Canal deprecated: should configure both (to confirm)

- Is kubenet the default network solution of k8s?

- prendre le gif de https://github.com/ahmetb/kubernetes-network-policy-recipes

- simple official docs are sometimes outdated...


- MetalLB is simply a way to use LoadBalancer when not captive from big Google Microsoft or Amazon cloud (no official K8s solution) : https://metallb.universe.tf/


- Idée TP HPA : https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/


<!-- - trouver comment évaluer acquis à la fin : QCM par mail ou quiz !!! -->

- https://github.com/hobby-kube/guide

- network policy tp : https://kubernetes.io/docs/tasks/administer-cluster/declare-network-policy/
  - à combne avec calico / cilium / weavenet tuto : https://kubernetes.io/docs/tasks/administer-cluster/network-policy-provider/
  - avec les example policies à copier coller (dont schema) : https://github.com/ahmetb/kubernetes-network-policy-recipes


## Network Plugins
Network plugins in Kubernetes come in a few flavors:

* CNI plugins: adhere to the Container Network Interface (CNI) specification, designed for interoperability.
* Kubenet plugin: implements basic cbr0 using the bridge and host-local CNI plugins

---

- **Network policies** : Faire un petit tableau avec Cilium / Calico / Kubenet etc et ce qu'ils font et si CNI ou non
- Dire que pas confondre avec **services meshes** Istio / Pilot etc.
- Ni avec **ingress controllers** !!! https://medium.com/flant-com/comparing-ingress-controllers-for-kubernetes-9b397483b46b


- Service Topology enables a service to route traffic based upon the Node topology of the cluster : https://kubernetes.io/docs/concepts/services-networking/service-topology/


- Ne pas arrêter de répéter que tout est alpha dans k8s et les third party stuff et que l'écosystème change tout le temps. par contre bcp d'entreprises dedans et ne sera pas abandonné, et c'est en fait si on trouve l'expression déclarative des objets + les API CRD qui fait qu'on trouve un intérêt à K8s fondamentalement.

- Se baser sur https://victorops.com/blog/how-to-use-vanilla-kubernetes

- Rappel layer 2/3 layer 4 layer 7 ?

Ressource vidéo : Kubernetes Services networking https://www.youtube.com/watch?v=NFApeJRXos4&list=PLoWxE_5hnZUZMWrEON3wxMBoIZvweGeiq&index=4

---

## Explain nodeport-vs-loadbalancer-vs-ingress (schémas à recréer/créditer)
https://medium.com/google-cloud/kubernetes-nodeport-vs-loadbalancer-vs-ingress-when-should-i-use-what-922f010849e0

---

## Pour ressources :
- https://docs.projectcalico.org/networking/determine-best-networking
- https://metallb.universe.tf/
- Bitnami Helm : https://github.com/bitnami/charts/tree/master/bitnami
- BKPR : https://github.com/bitnami/kube-prod-runtime
- Istio : https://www.digitalocean.com/community/tutorials/an-introduction-to-service-meshes

- Vitess : A database clustering system for horizontal scaling of MySQL : https://vitess.io
-  metlallb ? nginx ingress ??

- https://www.youtube.com/watch?v=iqVt5mbvlJ0 + github

- CronJobs!

- faire un truc avec secrets

- RBAC : https://kubernetes.io/docs/reference/access-authn-authz/rbac/

## TD idées
https://github.com/GoogleCloudPlatform/kubernetes-workshops/tree/master/state
https://github.com/GoogleCloudPlatform/kubernetes-workshops/tree/master/advanced et https://github.com/GoogleCloudPlatform/kubernetes-workshops/blob/master/advanced/local.md

---
- https://12factor.net/fr/

Pitch MetalLB :
Kubernetes does not offer an implementation of network load-balancers (Services of type LoadBalancer) for bare metal clusters. The implementations of Network LB that Kubernetes does ship with are all glue code that calls out to various IaaS platforms (GCP, AWS, Azure…). If you’re not running on a supported IaaS platform (GCP, AWS, Azure…), LoadBalancers will remain in the “pending” state indefinitely when created.

Bare metal cluster operators are left with two lesser tools to bring user traffic into their clusters, “NodePort” and “externalIPs” services. Both of these options have significant downsides for production use, which makes bare metal clusters second class citizens in the Kubernetes ecosystem.

MetalLB aims to redress this imbalance by offering a Network LB implementation that integrates with standard network equipment, so that external services on bare metal clusters also “just work” as much as possible.


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


## Apps de demo
https://github.com/oskapt/rancher-demo

## Ressources

(https://www.mirantis.com/blog/multi-container-pods-and-container-communication-in-kubernetes/).

https://github.com/cyberark/KubiScan

https://github.com/ramitsurana/awesome-kubernetes/blob/master/docs/managed-kubernetes/managed-kubernetes.md


## Azure Terraform
az ad sp create-for-rbac --skip-assignment


https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/guides/service_principal_client_secret
https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs
https://docs.microsoft.com/fr-fr/azure/developer/terraform/get-started-cloud-shell#authenticate-via-azure-service-principal