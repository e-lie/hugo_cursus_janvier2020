---
title: TP optionnel - Bootstrapper un cluster multi-noeud avec Ansible (Kubeadm ou mode manuel)
draft: true
weight: 2090
---

<!-- Comme nous l'avons évoqué dans le cours précédent, pour installer Kubernetes soi-même (et dans sa version la plus "vanilla"), on utilise généralement `kubeadm` qui est une sorte d'opérateur d'installation et mise à jour des différents composants de Kubernetes ou on peut installer les composants à la main en suivant un tutoriel `Kubernetes the hard way` (ce qui est principalement utile a des fins d'apprentissage).

## Kubeadm : l'opérateur de cluster -->


## `Kubernetes the hard way` avec Ansible

![](../../images/kubernetes/shema-persos/k8s-archi.jpg)


La version la plus manuelle de l'installation de Kubernetes a été documentée à des fins d'apprentissage par Kelsey Hightower qui l'a nommé `Kubernetes the hard way`. On peut la retrouver à l'adresse https://github.com/kelseyhightower/kubernetes-the-hard-way/tree/master/docs.

La principale limite de cette méthode d'installation est le nombre très important de manipulations sur plusieurs serveurs et donc le temps d'installation conséquent, peu reproductible et qui favorise les erreurs. Pour remédier à cela, l'installation manuelle a été notamment reprise par `githubixx/RW` de tauceti.blog et intégré dans une série de tutoriels adossés à des roles Ansible qui documentent cette installation manuelle et sont d'après l'auteur utilisable en production à une échelle moyenne : https://www.tauceti.blog/posts/kubernetes-the-not-so-hard-way-with-ansible-the-basics/

Nous allons suivre étape par étapes cette installation manuelle Ansible pour observer et commenter concrêtement les différents composants et étapes d'installation de Kubernetes.

- Commencez par cloner le projet de base avec `git clone -b master https://github.com/e-lie/k8s_notsohardway_correction.git`

- Installer Terraform et Ansible avec `bash /opt/terraform.sh` et `sudo apt remove ansible && bash /opt/ansible.sh`

Nous allons maintenant ouvrir le projet et suivre le README pour créer l'infrastructure dans le cloud avec terraform puis exécuter les différents playbooks pour installer étape par étape cluster.

Chaque étape sera l'occasion de commenter le code Ansible et explorer notre cluster au cours de son installation.


### Créer les serveurs en IaC avec Terraform

- compléter le subdomain
- compléter les tokens infra et DNS

Observons et expliquons ensemble le code.

- `./cloud_init setup_terraform`

### Setup Ansible

L'inventaire Ansible Terraform

- `source env_file`
- `ssh-add ~/.ssh/id_stagiaire`
- `ansible all -K -m ping`
- `ansible-inventory --host controller-0`
### Setup PKI infrastructure

### Setup

### Correction par un script

Pour installer toute l'infrastructure en une seule commande : `bash deploy_all.sh`
## Détruire l'infra

- `./cloud_init destroy_infra`

## Cluster de 3 noeuds kubeadm avec metallb, rook, argoCD et BKPR

- `git clone -b base https://github.com/e-lie/provisionning.git`

- compléter le subdomain
- compléter les tokens infra et DNS

- `./cloud_init setup_terraform`. Si il y a une errur concernant le `remote exec` rexecutez `ssh-add ~/.ssh/id_stagiaire` et relancez l'installation.

- Modifiez la ligne `export KUBE1_DOMAIN=kube1.k8slab.dopl.uk` du fichier `get_k8s_admin_config.sh` en remplaçant k8slab par votre sous domaine et exécutez ce script avec `bash`.

- Testez la bonne installation du cluster avec `kubectl cluster-info` et `kubectl get nodes`. Vous pouvez également ajouter la kubeconfig `hobby-kube-connection.yaml` à Lens.

### Installer metallb

Premier élément indispensable d'un cluster on premise, être capable de faire rentrer le traffic depuis l'extérieur (nord). Par défault les services de type `LoadBalancer` ne fonctionnerons pas et resterons des `NodePort`. Il est alors possible de provisionner manuellement des loadbalancer externes vers le bon nodeport. Mais cette méthode est peu efficace et provoque vite des erreurs liées à des conflits de ports et problèmes de mise à jour manuelle.

La solution adapté est probablement d'installer la solution générique `metallb` qui peut fournir des loadbalancer internes au cluster.

- Compléter `k8s-bootstrap/metallb-values.yaml` avec les liste des ips des noeuds récupérées avec `ping kube1-3.<subdomain>.dopl.uk`
- Installer `metallb` avec le chart helm grâce à la commande : 

```bash
helm upgrade --install metallb metallb \
  --repo https://metallb.github.io/metallb \
  --namespace metallb-system --create-namespace \
  --version 0.12.1 --values=k8s-bootstrap/metallb-values.yaml
```

- Par défaut nous l'avons installé en mode IP : les agents speakers vont répondre aux requêtes ARP pour assigner les IP que nous avons fournie aux noeuds et rediriger le traffic vers le bon service endpoint.
### Installer le `Ingress Nginx`

Installons le Ingress Nginx pour exposer des services HTTP et immédiatement vérifier que les services `LoadBalancer` fonctionnent:

```bash
helm upgrade --install ingress-nginx ingress-nginx \
  --repo https://kubernetes.github.io/ingress-nginx \
  --namespace ingress-nginx --create-namespace \
  --version 4.1.0
```

- Si metallb est bien configuré, le service qui expose le ingress controller devrait se voir attribuer une IP externe. On peut le vérifier avec la commande: `kubectl get svc -n ingress-nginx -o wide`.

### Installer l'opérateur `CertManager`

voir le début du TP `CI/CD avec Gitlab et ArgoCD`
### Installer ArgoCD pour superviser les applications

voir le début du TP `CI/CD avec Gitlab et ArgoCD`


### Installer un storage provisionner

kubectl apply -f https://raw.githubusercontent.com/rancher/local-path-provisioner/v0.0.22/deploy/local-path-storage.yaml

<!-- ### Installer Rook et Rook CephFS pour du stockage distribué sur notre Cluster -->
























<!-- ## Liste de prérequis pour un cluster de production
### Infrastructure du cluster

- Exécuter un control plane hautement disponible : Vous pouvez y parvenir en exécutant les composants du control plane sur trois nœuds ou plus. Une autre bonne pratique recommandée est de déployer les composants maîtres Kubernetes et etcd sur deux groupes de nœuds distincts. Cela permet généralement de faciliter les opérations etcd, telles que les mises à niveau et les sauvegardes, et de diminuer le rayon des défaillances du control plane. De plus, pour les grands clusters Kubernetes, cela permet à etcd de bénéficier d'une ressources en l'exécutant sur certains types de nœuds qui répondent à ses besoins d'E/S étendus. Enfin, évitez de déployer des pods sur les nœuds du control plane.

- Exécutez un groupe de workers hautement disponibles : Vous pouvez y parvenir en exécutant un groupe ou plus de nœuds workers avec trois instances ou plus. Si vous exécutez ces groupes de workers en utilisant un fournisseur de cloud, vous devez les déployer dans un groupe d'auto-scaling et dans différentes availability zones.

- Une autre condition pour garantir la haute disponibilité même sous une charge anormalement élevée et/ou pendant les opération de mise à jour est de déployer l'auto-scaler de cluster de Kubernetes, qui permet aux groupes de workers de s'agrandir et se réduire automatiquement en fonction des besoins.


TODO :

Diagnostic: parler de dnsutils et comment debugger le DNS: https://stackoverflow.com/questions/52109039/nslookup-cant-resolve-kubernetes-default -->