---
draft: false
title: "TP 2 - Déployer Wordpress rapidement"
weight: 2045
---

## Déployer Wordpress et MySQL avec du stockage et des Secrets

Nous allons suivre ce tutoriel pas à pas : https://kubernetes.io/docs/tutorials/stateful-application/mysql-wordpress-persistent-volume/

Il faut :
- copier les 2 fichiers et les appliquer
- vérifier que le stockage a bien fonctionné
- découvrir ce qui manque pour que cela fonctionne
- le créer à la main ou suivre le reste du tutoriel qui passe par l'outil Kustomize (attention, Kustomize ajoute un suffixe aux ressources qu'il créé)
<!-- - ne pas oublier de relancer les déploiements qui sont restés bloqués à cause de la ressource manquante -->
<!-- generatorOptions:
 disableNameSuffixHash: true -->
On peut ensuite observer les différents objets créés, et optimiser le process avec un fichier `kustomzation.yaml` plus complet.

- Entrez dans un des pods, et de l'intérieur, lisez le secret qui lui a été rendu accessible.

<!-- - https://cloud.google.com/kubernetes-engine/docs/tutorials/persistent-disk/
- https://github.com/GoogleCloudPlatform/kubernetes-workshops/blob/master/state/local.md
- https://github.com/kubernetes/examples/blob/master/staging/persistent-volume-provisioning/README.md -->

<!-- TODO: add configmap for wordpress ou alors tp mysql avec configmaps -->
