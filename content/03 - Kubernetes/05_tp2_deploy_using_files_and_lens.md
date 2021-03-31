---
draft: false
title: "05 - TP 2 - Déployer en utilisant des fichiers ressource et Lens"
weight: 2045
---

Dans ce court TP nous allons redéployer notre application `rancher-demo` du TP1 mais cette fois en utilisant `kubectl apply -f` et en visualisant le résultat dans `Lens`.

- Changez de contexte pour minikube avec `kubectl config use-context minikube`
- Chargez également la configurationde minikube dans `Lens` en cliquant à nouveau sur plus et en selectionnant `minikube`
- Commencez par supprimer les ressources `rancher-demo` et `rancher-demo-service` du TP1
- Créez un dossier `TP2_deploy_using_files_and_Lens` sur le bureau de la machine distante et ouvrez le avec `VSCode`.

Nous allons d'abord déployer notre application comme un simple **Pod**

- Créez un fichier `rancher-demo-pod.yml` avec à l'intérieur le code d'exemple du cours.