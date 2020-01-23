---
title: 'TP3 - Déployer Jenkins avec Helm'
draft: false
---

Helm est un gestionnaire de paquet K8s qui permet d'installer des paquets sans faire des copier-coller pénibles de yaml :

- pas de duplication de code
- des déploiement avancés avec un processus de mise à jour k8s intégré

Helm ne dispense pas de maîtriser l'administration de son cluster.

### Ajoutons un nouvel alias

Nous allons utiliser la version de helm packagée dans microk8s:

- Activez la avec : `microk8s.enable helm`
- Initialisez helm en installant le composant serveur tiller dans le cluster avec `microk8s.helm init`
- créez un alias et ajoutez le au `~/.bashrc`: `alias helm='microk8s.helm'`
- Lancez `source ~/.bashrc` pour l'activer.



- Suivez le [Quickstart] (https://helm.sh/docs/intro/quickstart/) 


- Cherchez Jenkins sur https://hub.kubeapps.com
- Prenez la version de **codecentric** et installez la
- suivez les instruction affichez dans le terminal pour créer un port forwarding
- Visitez localhost:8080
- cherchez le nom du pod jenkins
- afficher les logs du pod avec `kc logs`
- récupérez le password admin d'init et collez le dans le navigateur
- Notre jenkins de travail est prêt

- Avant de continuer nous allons cependant Mettre un Loadbalancer Ingress en nous inspirant du TP3
