---
title: 10 - Principes de conception et architecture détaillée de Kubernetes
draft: true
weight: 2100
---

# Kubernetes

- rappels docker
- cours presentation:
- TP CLI
- cours objets simples: pods, deploy, services
- TP monsterstack simple fichiers
- Cours différentes solutions pour louer ou installer un cluster kubernetes
- Cours Facultatif : Design et architecture Kubernetes en détail
    - Control loops
    - L'API
    - Scheduler : placement des pods
    - 
- TP Installer un cluster multi noeuds (simple ou avancé)
- Cours cycle de vie d'une requête
    - authentication
    - authorization
    - admission control


TODOOOO

faire un TP monsterstack avancé ou on :

- met un statefulset et un volume pour un redis multinstance
- ajoute un paramétrage par variables d'env et configmap de l'application
- découvre kustomize avec les base/overlay suffix etc pour installer deux fois l'application avec des paramètres différents en dev/prod

cleaner la partie skaffold / minikube docker-env pour utiliser skaffold avec mon registry toujours dispo