---
title: Bibliographie
draft: false
---

## Linux

- Shotts 2012 - The Linux Command Line - A complete introduction
- Yao 2014 - Linux command line - A beginner's guide

### Ressources

- OpenClassrooms - Reprenez le contrôle à l'aide de Linux ! https://openclassrooms.com/fr/courses/43538-reprenez-le-controle-a-l-aide-de-linux

## Ansible


- Jeff Geerling - Ansible for DevOps - Leanpub

##### Pour aller plus loin

- Keating2017 - Mastering Ansible - Second Edition - Packt

##### Cheatsheet

- [https://www.digitalocean.com/community/cheatsheets/how-to-use-ansible-cheat-sheet-guide](https://www.digitalocean.com/community/cheatsheets/how-to-use-ansible-cheat-sheet-guide)

## Docker

- McKendrick, Gallagher 2017 Mastering Docker - Second Edition

### Pour aller plus loin

- Miell,Sayers2019 - Docker in Practice

### Cheatsheet

- [https://devhints.io/docker](https://devhints.io/docker)

### Ressources

- Doc officielle : https://docs.docker.com/
- Référence officielle : https://docs.docker.com/reference/
- Awesome Docker, liste de ressources sur Docker : https://github.com/veggiemonk/awesome-docker
- Portainer, GUI pour Docker : https://www.portainer.io/installation/
- Lazy Docker, terminal pour Docker : https://github.com/jesseduffield/lazydocker
- Convoy, driver pour volumes Docker : https://github.com/rancher/convoy
- Accéder à ses containers dans Minecraft : https://github.com/docker/dockercraft
  <!-- https://jpetazzo.github.io/2017/01/20/docker-logging-gelf/ -->
- Doc officielle sur la sécurité dans Docker : https://docs.docker.com/engine/security/
- Documentation sur le système de filesystem overlay de Docker : https://docs.docker.com/storage/storagedriver/overlayfs-driver/
- Tutoriels officiels sur la sécurité dans Docker : https://github.com/docker/labs/tree/master/security
- Vidéo sur les bonnes pratiques dans Docker : https://noti.st/aurelievache/PrttUh
- Vidéo "Containers, VMs... Comment ces technologies fonctionnent et comment les différencier ?" (Quentin Adam) https://www.youtube.com/watch?v=wG4_JQXvZIc
- Diapositives sur Docker, Swarm, Kubernetes : https://container.training/
  - en particulier les prolèmes du stateful : https://container.training/swarm-selfpaced.yml.html#450

## Kubernetes

- Kubernetes Up and Running, O'Reilly 2019

### Ressources

- [Awesome Kubernetes](https://github.com/ramitsurana/awesome-kubernetes)


<!-- - https://cloud.google.com/kubernetes-engine/docs/tutorials/persistent-disk/
- https://github.com/GoogleCloudPlatform/kubernetes-workshops/blob/master/state/local.md
- https://github.com/kubernetes/examples/blob/master/staging/persistent-volume-provisioning/README.md -->


#### Réseau

- Documentation officielle : <https://kubernetes.io/fr/docs/concepts/services-networking/service/>

- [An introduction to service meshes - DigitalOcean](ttps://www.digitalocean.com/community/tutorials/an-introduction-to-service-meshes)

- [Kubernetes NodePort vs LoadBalancer vs Ingress? When should I use what?](https://medium.com/google-cloud/kubernetes-nodeport-vs-loadbalancer-vs-ingress-when-should-i-use-what-922f010849e0)

- [Determine best networking option - Project Calico](https://docs.projectcalico.org/networking/determine-best-networking)

- [Doc officielle sur les solutions de networking](https://kubernetes.io/docs/concepts/cluster-administration/networking/)

- Comparatif de solutions réseaux (fr) : https://www.objectif-libre.com/fr/blog/2018/07/05/comparatif-solutions-reseaux-kubernetes/#Flannel

-  Comparatif de solutions réseaux (en) :https://rancher.com/blog/2019/2019-03-21-comparing-kubernetes-cni-providers-flannel-calico-canal-and-weave/


##### Vidéos sur le réseau

Des vidéos assez complètes sur le réseau, faites par Calico :
- [Kubernetes Ingress networking](https://www.youtube.com/watch?v=40VfZ_nIFWI&list=PLoWxE_5hnZUZMWrEON3wxMBoIZvweGeiq&index=5)
- [Kubernetes Services networking](https://www.youtube.com/watch?v=NFApeJRXos4&list=PLoWxE_5hnZUZMWrEON3wxMBoIZvweGeiq&index=4)
- [Kubernetes networking on Azure](https://www.youtube.com/watch?v=JyLtg_SJ1lo&list=PLoWxE_5hnZUZMWrEON3wxMBoIZvweGeiq&index=2)

Sur MetalLB, les autres vidéos de la chaîne sont très bien :
- [Why you need to use MetalLB -  Adrian Goins](https://www.youtube.com/watch?v=Ytc24Y0YrXE)


#### Stockage

- [Rook et Ceph (fr)](https://www.cloudops.com/fr/blog/guide-de-survie-rook-et-ceph/)
- [Longhorn](https://github.com/longhorn/longhorn)
- 

#### Autres

- Bitnami Helm : https://github.com/bitnami/charts/tree/master/bitnami
- BKPR : https://github.com/bitnami/kube-prod-runtime
- Vitess : A database clustering system for horizontal scaling of MySQL : https://vitess.io
- [Rancher](https://rancher.com/)
- Charts Helm : [https://hub.kubeapps.com](https://hub.kubeapps.com) 
- Stratégies de déploiement : <https://blog.container-solutions.com/kubernetes-deployment-strategies>

#### Azure AKS

##### Documentation

- https://docs.microsoft.com/fr-fr/azure/aks/
- https://github.com/microsoft/kubernetes-learning-path

##### Scaling d'application dans Azure

- https://docs.microsoft.com/fr-fr/azure/aks/tutorial-kubernetes-scale

##### Stockage dans Azure

- https://docs.microsoft.com/fr-fr/azure/aks/azure-files-dynamic-pv

<!-- https://docs.microsoft.com/fr-fr/azure/aks/azure-files-dynamic-pv
https://docs.microsoft.com/fr-fr/azure/aks/azure-disks-dynamic-pv
https://docs.microsoft.com/fr-fr/azure/aks/concepts-storage -->

##### Registry dans Azure

- https://docs.microsoft.com/fr-fr/azure/container-registry/container-registry-quickstart-task-cli


##### Le réseau dans Azure
- Vidéo "K8s Networking in Azure" : https://www.youtube.com/watch?v=JyLtg_SJ1lo&list=PLoWxE_5hnZUZMWrEON3wxMBoIZvweGeiq&index=2
  
- https://docs.microsoft.com/fr-fr/azure/aks/internal-lb
- https://docs.microsoft.com/fr-fr/azure/aks/load-balancer-standard
- https://docs.microsoft.com/fr-fr/azure/aks/http-application-routing
- https://docs.microsoft.com/fr-fr/azure/aks/concepts-network
- https://blog.crossplane.io/azure-secure-connectivity-for-aks-azure-db/
- https://docs.microsoft.com/fr-fr/azure/mysql/concepts-aks

##### Terraform avec Azure
Terraform est un outil permettant de décrire des ressources cloud dans un fichier pour utiliser le concept d'infrastructure-as-code avec tous les objets des fournisseurs de Cloud.

- https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/kubernetes_cluster
- https://github.com/terraform-providers/terraform-provider-azurerm/tree/master/examples/kubernetes

- https://registry.terraform.io/modules/Azure/appgw-ingress-k8s-cluster/azurerm/latest
- https://docs.microsoft.com/fr-fr/azure/aks/ingress-basic#create-an-ingress-controller

##### Autres
- Les CRD : utiliser des objets Kubernetes pour définir des ressources Azure : https://github.com/Azure/azure-service-operator
- Demo : <https://github.com/Microsoft/RockPaperScissorsLizardSpock>

### Pour aller plus loin

- Luksa, Kubernetes in Action, 2018

### Cheatsheets

- <https://kubernetes.io/fr/docs/reference/kubectl/cheatsheet/>
- Short names in k8s : <https://blog.heptio.com/kubectl-resource-short-names-heptioprotip-c8eff9fb7202>


<!-- ## AKS

https://docs.microsoft.com/fr-fr/azure/aks/kubernetes-walkthrough
https://docs.microsoft.com/fr-fr/azure/aks/
https://github.com/microsoft/kubernetes-learning-path

### HPA
https://docs.microsoft.com/fr-fr/azure/aks/tutorial-kubernetes-scale

### Stockage
https://docs.microsoft.com/fr-fr/azure/aks/azure-files-dynamic-pv
https://docs.microsoft.com/fr-fr/azure/aks/azure-disks-dynamic-pv
https://docs.microsoft.com/fr-fr/azure/aks/concepts-storage

### CRD
https://github.com/Azure/azure-service-operator

### Network
https://docs.microsoft.com/fr-fr/azure/aks/internal-lb
https://docs.microsoft.com/fr-fr/azure/aks/load-balancer-standard
https://docs.microsoft.com/fr-fr/azure/aks/http-application-routing
https://docs.microsoft.com/fr-fr/azure/aks/concepts-network
https://blog.crossplane.io/azure-secure-connectivity-for-aks-azure-db/
https://docs.microsoft.com/fr-fr/azure/mysql/concepts-aks

### Terraform
https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/kubernetes_cluster
https://github.com/terraform-providers/terraform-provider-azurerm/tree/master/examples/kubernetes
https://docs.microsoft.com/fr-fr/azure/aks/ingress-basic#create-an-ingress-controller
https://registry.terraform.io/modules/Azure/appgw-ingress-k8s-cluster/azurerm/latest
https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/kubernetes_cluster

### Demo
https://github.com/Microsoft/RockPaperScissorsLizardSpock -->


<!-- ## Jenkins

- Leszko2017 - Continuous Delivery with Docker and Jenkins - Packt
- Le wiki Jenkins

## Terraform

- Brikman2019 - Terraform Up&Running - O Reilly -->

<!-- ## Linux -->

## DevOps

- Krief - Learning DevOps - The complete guide (Azure Devops, Jenkins, Kubernetes, Terraform, Ansible, sécurité) - 2019
- The DevOps Handbook

<!-- ### Sécurité et DevOps

- Madhu,Akash2017 - Security automation with Ansible 2 - Packt -->
