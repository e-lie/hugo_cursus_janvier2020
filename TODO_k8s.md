# TODO



- imprimer des tas de schémas et de fiches plastifiées sur les concepts de k8s (idéalement qui "s'emboîtent" façon puzzle) et les distribuer dès le début, recréer les chémas avec ça au tableau



---



# Notes en vrac



- imprimer des awesome-* surlignés et commentés
https://github.com/veggiemonk/awesome-docker#security

## Un netlify commun sur lequel on itère
- dépasser la juxtaposition de modules :
  - intro au cursus : le but est de faire un PaaS (ex. : pbmatique cloud hybride, multi cluster), et non seulement "une CI/CD correcte"
  - on part sur du ansible : infra as code et déploiement, l'approche bas niveau, pour saisir l'idempotence, infra as code, versioning (git), plutôt pet que cattle
  - sur les bases de docker : concepts "devops" : conteneurisation, cattle, immutabilité
  - k8s+rancher :
    - concepts k8s : théorie, liste de concepts, petites manips avec docker run rancher et rancher utilisé comme IDE pour du déploiement k8s, GUI k8s
    - rancher : problématiques de PaaS, cloud hybride / multi cluster et workloads + multi cluster apps (enhanced helm w/ rancher), auth et multiuser (RBAC), ouverture vers pbmatiques réseau distribués (canal/flannel/calico/istio) et volume distribué (ceph/gluster), debug des accès disque et réseau avec truc de tracing poussé par rancher jaeger

DevOps :
- conteneurisation
- infra as code
- CI/CD
- cloud (IAAS+PAAS)

## Scénarios TP fil rouge :

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
  -  TP4 : Swarm vite fait (app web qui loadbalance où tu sais quel nœud t'a servi ou voting app) et intro à K8s en montrant les limites de swarm

- Kubernetes + Rancher
  - Concepts K8S (s'inspirer du bouquin openshift)
  - petites manips avec docker run rancher et rancher utilisé comme IDE pour du déploiement k8s, GUI k8s
  - chart helm
  - déployer une CI en mettant non seulement gitlab dans k8s mais aussi utiliser un node comme CI
  - rancher intro à la logique : 
     problématiques de PaaS, cloud hybride / multi cluster et workloads + multi cluster apps (enhanced helm w/ rancher), auth et multiuser (RBAC)
  - chart helm spécifiques à rancher
  - ouverture vers pbmatiques réseau distribués (canal/flannel/calico/istio) et volume distribué (ceph/gluster), debug des accès disque et réseau avec truc de tracing poussé par rancher jaeger

(Hadrien)
  - TP : Gitlab-CI runner + gitlab ? https://rancher.com/blog/2019/connecting-gitlab-autodevops-authorized-cluster-endpoints/
  - https://docs.gitlab.com/12.8/runner/install/kubernetes.html
  - https://docs.gitlab.com/charts/installation/index.html

TP wordpress simple : https://github.com/kubernetes/examples/tree/master/mysql-wordpress-pd

# Ressources K8S

## Cours

Bouquin Learn openshift très cool

https://kubernetes.io/fr/docs/reference/kubectl/cheatsheet/

https://codelabs.developers.google.com/codelabs/cloud-orchestrate-with-kubernetes/#0

https://www.objectif-libre.com/fr/blog/2018/07/05/comparatif-solutions-reseaux-kubernetes/#Flannel

https://rancher.com/blog/2019/2019-03-21-comparing-kubernetes-cni-providers-flannel-calico-canal-and-weave/

## TP

https://github.com/GoogleCloudPlatform/kubernetes-workshops

### Gitlab
* https://rancher.com/blog/2019/connecting-gitlab-autodevops-authorized-cluster-endpoints/
* https://docs.gitlab.com/12.8/runner/install/kubernetes.html
* https://docs.gitlab.com/charts/installation/index.html
* https://blog.kubernauts.io/ci-cd-with-rancher-pipelines-and-self-hosted-gitlab-b17248294fda

https://medium.com/@abenahmed1/pipeline-de-ci-cd-simplifi%C3%A9-dans-un-cluster-kubernetes-avec-gitlab-et-rancher-602a01029bae

Il y a aussi Rancher's own CI/CD pipeline: https://rancher.com/docs/rancher/v2.x/en/project-admin/pipelines/


NOTE: Gitlab operator? https://docs.gitlab.com/charts/installation/operator.html

## General

* https://kubernetes.io/blog/2018/07/18/11-ways-not-to-get-hacked/
* https://coreos.com/operators/
* https://kubernetes.io/docs/concepts/extend-kubernetes/operator/
## Kubernetes avec DB répliquée PostgreSQL
* https://github.com/CrunchyData/postgres-operator
* https://access.crunchydata.com/documentation/postgres-operator/latest/run-ha-postgresql-rancher-kubernetes-engine/
* https://portworx.com/
* https://portworx.com/ha-postgresql-kubernetes/
* https://hackernoon.com/postgresql-cluster-into-kubernetes-cluster-f353cde212de
* https://info.crunchydata.com/blog/crunchy-postgresql-operator-with-rook-ceph-storage
* https://rook.io/docs/rook/v1.2/quickstart.html



* https://vitess.io/

## TODO: à étudier

https://github.com/kubernetes/kubernetes/blob/master/cluster/kube-up.sh
https://rexray.readthedocs.io/en/stable/user-guide/storage-providers/ceph/#ceph-rbd

* https://linkerd.io/

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