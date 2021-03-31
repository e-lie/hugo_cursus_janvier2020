---
draft: false
title: "09 - TP 4 - Déployer Wordpress Avec une base de donnée persistente"
weight: 2055
---

## Déployer Wordpress et MySQL avec du stockage et des Secrets

Nous allons suivre ce tutoriel pas à pas : https://kubernetes.io/docs/tutorials/stateful-application/mysql-wordpress-persistent-volume/

Il faut :
- Créez un projet TP4.
- Créer la `kustomization.yaml` avec le générateur de secret.
- Copier les 2 fichiers dans le projet.
- Les ajouter comme resources à la `kustomization.yaml`.

Commentons un peu le contenu des deux fichier `mysql-deployment.yaml` et `wordpress-deployment.yaml`.

- Vérifier que le stockage et le secret ont bien fonctionnés.
- Exposez et visitez le service avec `minikube service wordpress`. Faite la configuration de base de wordpress.

### Observer le déploiement du secret à l'intérieur des pods

- Entrez dans le pod de mysql grâce au terminal de `Lens`.
- Cherchez la variable d'environnement `MYSQL_ROOT_PASSWORD` à l'aide des commande `env | grep MYSQL`. Le conteneur mysql a utilisé cette variable accessible de lui seul pour se configurer.

### Observez la persistence

- Supprimez et recréer les deux déploiements (mais pas le total). En rechargeant le site on constate que les données ont été conservées.

- Allez observer la section stockage dans `Lens`. Commentons ensemble.

- Supprimer tout avec `kubectl delete -k .`. Que s'est-il passé ? (côté storage)

En l'état les `PersistentVolumes` générés par la combinaise du `PersistentVolumeClaim` et de la `StorageClass` de minikube sont également supprimés en même tant que les PVC. Les données sont donc perdues et au chargement du site on doit relancer l'installation.

Pour éviter cela il faut que la `storageClass` standard soit configurée avec une `Reclaim Policy` à `retain` (conserver) et non `delete`.

- Supprimez la `storageClass standard`.
- Créez-en une nouvelle en cliquant sur le `+ > create resource` en bas à gauche de Lens et collez le code suivant:

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: standard
provisioner: docker.io/hostpath
reclaimPolicy: Retain
```



<!-- - https://cloud.google.com/kubernetes-engine/docs/tutorials/persistent-disk/
- https://github.com/GoogleCloudPlatform/kubernetes-workshops/blob/master/state/local.md
- https://github.com/kubernetes/examples/blob/master/staging/persistent-volume-provisioning/README.md -->

<!-- TODO: add configmap for wordpress ou alors tp mysql avec configmaps -->

