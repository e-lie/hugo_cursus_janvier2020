---
title: TP opt. - Gestion d'un cluster de production
draft: true
weight: 2100
---


## Intro: Installer un serveur K8s de production


![](../../images/kubernetes/kubernetes-production-layers.png)


- aws configure avec les AWS ID et secret de mon compte

- terraform init && apply dans packtcluster-vpc puis terraform output pour copier les outputs de config du VPC

- collez les outputs dans les vars de packtcluster

- terraform init and apply wait for 9 min

- aws eks --region "eu-west-3" update-kubeconfig --name packtclusters-default -> va maj la kubeconfig

- `terraform output authconfig` à copier dans un fichier authconfig.yaml puis `kubectl apply -n kube-system -f authconfig.yaml` (pour permettre aux noeuds worker AWS de s'authentifier )


## Configurer des namespaces et comptes uilisateurs avec Ansible




```bash
cd terraform/packtcluster
virtualenv $HOME/k8s-ansible-env
source $HOME/k8s-ansible-env/bin/activate
pip install ansible==2.9 openshift pyyaml requests
ansible-playbook -i \
    ../../ansible/inventories/packtclusters/ \
    -e "worker_iam_role_arn=$(terraform output worker_iam_role_arn)" \
    ../../ansible/cluster.yaml
```