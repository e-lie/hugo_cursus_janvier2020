---
title: TP4 - Stratégies de déploiement (rollouts) et monitoring
draft: no
---

<!-- https://kubernetes.io/docs/tutorials/kubernetes-basics/update/update-interactive/

https://blog.container-solutions.com/kubernetes-deployment-strategies

https://github.com/ContainerSolutions/k8s-deployment-strategies -->


## Installer Prometheus pour monitorer le cluster Minikube

Pour comprendre les stratégies de déploiement et mise à jour d'application dans Kubernetes (deployment and rollout strategies) nous allons installer puis mettre à jour une application d'exemple et observer comment sont gérées les requêtes vers notre application en fonction de la stratégie de déploiement choisie.

Pour cette observation nous avons besoin d'un outil de monitoring et nous utiliserons donc une des stack les plus populaires et intégrée avec kubernetes : Prometheus et Grafana. Prometheus est un projet de la Cloud Native Computing Foundation.

Prometheus est un serveur de métriques c'est à dire qu'il enregistre des informations précises (de petite taille) sur différents aspects d'un système informatique et ce de façon périodique en effectuant généralement des requêtes vers les composant du système (polling).

![](../../images/prometheus/overview.jpg)

### Installer Prometheus avec Helm

Installez Helm si ce n'est pas déjà fait. Sur Ubuntu : `sudo snap install helm --classic`


```bash
kubectl create namespace monitoring
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add kube-state-metrics https://kubernetes.github.io/kube-state-metrics
helm repo update
helm install \
  --namespace=monitoring \
  --version=13.2.1 \
  prometheus \
  prometheus-community/prometheus
```

### kube-state-metrics et le monitoring du cluster

Le chart officiel installe par défaut en plus de Prometheus, kube-state-metrics qui est une intégration automatique de kubernetes et prometheus.

Une fois le chart installé vous pouvez visualisez les informations dans Lens, dans la premiere section du menu de gauche  `Cluster`.

### Déployer notre application d'exemple et la connecter à prometheus

<!-- Prometheus deploie les services suivants... -->

- Télécharger le dépôt git
- Changez le contexte de docker cli pour pointer vers minikube.
- Builder l'image de l'application.
- Choisissez la première stratégie et appliquez le fichier déploiement.
- Exposer l'interface de prometheus (9090).
- Vérifiez que prometheus récupère bien les métriques de l'application.

### Installer et configurer Grafana pour visualiser les requêtes

Grafana est une interface de dashboard de monitoring facilement intégrable avec Prometheus. Elle va nous permettre d'afficher un histogramme en temps réel du nombre de requêtes vers l'application.

Créez un secret Kubernetes pour stocker le loging admin de grafana.

```bash
cat <<EOF | kubectl apply -n monitoring -f -
apiVersion: v1
kind: Secret
metadata:
  namespace: monitoring
  name: grafana-auth
type: Opaque
data:
  admin-user: $(echo -n "admin" | base64 -w0)
  admin-password: $(echo -n "admin" | base64 -w0)
EOF
```

Ensuite, installez le chart Grafana en précisant quelques paramètres:

```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install \
  --namespace=monitoring \
  --version=6.1.17 \
  --set=admin.existingSecret=grafana-auth \
  --set=service.type=NodePort \
  --set=service.nodePort=32001 \
  grafana \
  grafana/grafana
```

Maintenant Grafana est installé vous pouvez y acccéder en forwardant le port du service grace à Minikube:

```
$ minikube service grafana
```

Pour vous connectez utilisez, username: `admin`, password: `admin`.

Il faut ensuite connecter Grafana à Prometheus, pource faire ajoutez une `DataSource`:

```
Name: prometheus
Type: Prometheus
Url: http://prometheus-server
Access: Server
```

Créer une dashboard avec un Graphe. Utilisez la requête prometheus (champ query suivante):

```
sum(rate(http_requests_total{app="my-app"}[5m])) by (version)
```

Pour avoir un meilleur aperçu de la version de l'application accédée au fur et à mesure du déploiement, ajoutez `{{version}}` dans le champ `legend`.