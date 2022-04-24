---
title: 04 - TP2 - Plusieurs installations simples de Kubernetes
draft: false
weight: 2025
---

## Une 2e installation : Mettre en place un cluster K8s managé chez le provider de cloud Scaleway

Le formateur peut louer pour vous montrer un cluster kubernetes managé. Vous pouvez également louez le votre si vous préférez en créant un compte chez ce provider de cloud.

- Créez un compte (ou récupérez un accès) sur [Scaleway](https://console.scaleway.com/).
- Créez un cluster Kubernetes avec [l'interface Scaleway](https://console.scaleway.com/kapsule/clusters/create)

La création prend environ 5 minutes.

- Sur la page décrivant votre cluster, un gros bouton en bas de la page vous incite à télécharger ce même fichier `kubeconfig` (*Download Kubeconfig*).

Ce fichier contient la **configuration kubectl** adaptée pour la connexion à notre cluster.

## Une 3e installation: `k3s` sur votre VPS

K3s est une distribution de Kubernetes orientée vers la création de petits clusters de production notamment pour l'informatique embarquée et l'Edge computing. Elle a la caractéristique de rassembler les différents composants d'un cluster kubernetes en un seul "binaire" pouvant s'exécuter en mode `master` (noeud du control plane) ou `agent` (noeud de calcul).

Avec K3s, il est possible d'installer un petit cluster d'un seul noeud en une commande ce que nous allons faire ici:

<!-- - Passez votre terminal en root avec la commande `sudo -i` puis: -->
- Lancez dans un terminal la commande suivante: `curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="--disable=traefik" sh - `

 La configuration kubectl pour notre nouveau cluster k3s est dans le fichier `/etc/rancher/k3s/k3s.yaml` et accessible en lecture uniquement par `root`. Pour se connecter au cluster on peut donc faire (parmis d'autre méthodes pour gérer la kubeconfig):

 - Copie de la conf `sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/k3s.yaml`
 - Changer les permission `sudo chown $USER ~/.kube/k3s.yaml`
 - activer cette configuration pour kubectl avec une variable d'environnement: `export KUBECONFIG=~/.kube/k3s.yaml`
 - Tester la configuration avec `kubectl get nodes` qui devrait renvoyer quelque chose proche de:

 ```
NAME                 STATUS   ROLES                  AGE   VERSION
vnc-stagiaire-...   Ready    control-plane,master   10m   v1.21.7+k3s1
```


## Merger la configuration kubectl


La/Les configurations de kubectl sont à déclarer dans la variable d'environnement `KUBECONFIG`. Nous allons déclarer deux fichiers de config et les merger automatiquement. 

- Téléchargeons le fichiers de configuration scaleway fourni par le formateur ou à récupérer sur votre espace Scaleway. Enregistrez le par exemple dans `~/.kube/scaleway.yaml`.

- Copiez le fichier de config k3s `/etc/rancher/k3s/k3s.yaml` dans `~/.kube`: `sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/ && sudo chown stagiaire ~/.kube/k3s.yaml`

- Changez la variable d'environnement pour déclarer la config par défaut avec en plus nos deux nouvelles configs: `export KUBECONFIG=~/.kube/config:~/.kube/scaleway.yaml:~/.kube/k3s.yaml`

- Pour afficher la configuration fusionnée des fichiers et l'exporter lancez: `kubectl config view --flatten >> ~/.kube/merged.yaml`.

- Pour sélectionner ensuite cette configuration mergée: `export KUBECONFIG='~/.kube/merged.yaml'`.

- Maintenant que nos trois configs sont fusionnées, observons l'organisation du fichier `~/.kube/config` en particulier les éléments des listes YAML de:
  - `clusters`
  - `contexts`
  - `users`

- Listez les contextes avec `kubectl config get-contexts` et affichez les contexte courant avec `kubectl config current-context`.

- Changez de contexte avec `kubectl config use-context <nom_contexte>`.

- Testons quelle connexion nous utilisons avec avec `kubectl get nodes`.

- Ajoutons ces nouvelles connexion à Lens


<!-- 
## Facultatif : installation d'un cluster avec `kubeadm`

TODO lien tuto sympa ? -->