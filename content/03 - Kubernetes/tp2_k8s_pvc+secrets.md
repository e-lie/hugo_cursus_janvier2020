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


## Facultatif : la stack *Wordsmith*

Etudions et lançons ensemble ce YAML :

`wordsmith.yml` :
```yaml
apiVersion: v1
kind: Service
metadata:
  name: db
  labels:
    app: words-db
spec:
  ports:
    - port: 5432
      targetPort: 5432
      name: db
  selector:
    app: words-db
  clusterIP: None
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: db
  labels:
    app: words-db
spec:
  selector:
    matchLabels:
      app: words-db
  template:
    metadata:
      labels:
        app: words-db
    spec:
      containers:
      - name: db
        image: dockersamples/k8s-wordsmith-db
        ports:
        - containerPort: 5432
          name: db
---
apiVersion: v1
kind: Service
metadata:
  name: words
  labels:
    app: words-api
spec:
  ports:
    - port: 8080
      targetPort: 8080
      name: api
  selector:
    app: words-api
  clusterIP: None
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: words
  labels:
    app: words-api
spec:
  selector:
    matchLabels:
      app: words-api
  replicas: 5
  template:
    metadata:
      labels:
        app: words-api
    spec:
      containers:
      - name: words
        image: dockersamples/k8s-wordsmith-api
        ports:
        - containerPort: 8080
          name: api
---
apiVersion: v1
kind: Service
metadata:
  name: web
  labels:
    app: words-web
spec:
  ports:
    - port: 8081
      targetPort: 80
      name: web
  selector:
    app: words-web
  type: LoadBalancer
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
  labels:
    app: words-web
spec:
  selector:
    matchLabels:
      app: words-web
  template:
    metadata:
      labels:
        app: words-web
    spec:
      containers:
      - name: web
        image: dockersamples/k8s-wordsmith-web
        ports:
        - containerPort: 80
          name: words-web
```