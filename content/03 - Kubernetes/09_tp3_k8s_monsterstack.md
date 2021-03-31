---
title: '09 - TP 3 - Déployer des conteneurs de A à Z'
draft: false
weight: 2055
---

Ce TP va consister à créer des objets Kubernetes pour déployer une application microservices plutôt simple : `monsterstack`.
Elle est composée :
- d'un front-end en Flask (Python) appelé `monstericon`,
- d'un service de backend qui génère des images (un avatar de monstre correspondant à une chaîne de caractères) appelé `dnmonster`
- et d'un datastore `redis` servant de cache pour les images de monstericon

## Déploiement de la stack `monsterstack`

- Créez un projet vide `TP3_monster_app_k8s`.
- Créez le fichier de déploiement `monstericon.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: monstericon
  labels:
    app: monsterstack
spec:
  selector:
    matchLabels:
      app: monsterstack
      partie: monstericon
  strategy:
    type: Recreate
  replicas: 3
  template:
    metadata:
      labels:
        app: monsterstack
        partie: monstericon
    spec:
      containers:
      - name: monstericon
        image: tecpi/monster_icon:0.1
        ports:
        - containerPort: 9090

```

- Appliquez cette ressource avec `kubectl` et vérifiez dans `Lens` que les 3 réplicats sont bien lancés.

- Ajoutons un healthcheck de type `readinessProbe` au conteneur dans le pod avec la syntaxe suivante (le mot-clé `readinessProbe` doit être à la hauteur du `i` de `image:`) :
```yaml
        readinessProbe:
          failureThreshold: 5 # Reessayer 5 fois
          httpGet:
            path: /
            port: 9090
            scheme: HTTP
          initialDelaySeconds: 30 # Attendre 30s avant de tester
          periodSeconds: 10 # Attendre 10s entre chaque essai
          timeoutSeconds: 5 # Attendre 5s la reponse
          
```

Ainsi, k8s sera capable de savoir si le conteneur fonctionne bien en appelant la route `/`. C'est une bonne pratique pour que le  `replicaset` Kubernetes sache quand redémarrer un pod et garantir que notre application se répare elle même (self-healing).

- Ajoutons aussi des contraintes sur l'usage du CPU et de la RAM, en ajoutant à la même hauteur que `image:` :
```yaml
      resources:
        requests:
          cpu: "100m"
          memory: "50Mi"
```

Nos pods auront alors **la garantie** de disposer d'un dixième de CPU et de 50 mégaoctets de RAM. Ce type d'indications permet de remplir au maximum les ressources de notre cluster tout en garantissant qu'aucune application ne prend toute les ressources à cause d'un fuite mémoire etc.  

- Lancer `kubectl apply -f monstericon.yaml` pour appliquer.
- Avec `kubectl get pods --watch`, observons en direct la stratégie de déploiement `type: Recreate`
- Avec `kubectl describe deployment monstericon`, lisons les résultats de notre `readinessProbe`, ainsi que comment s'est passée la stratégie de déploiement `type: Recreate`

#### Déploiement semblable pour dnmonster

Maintenant nous allons également créer un déploiement pour `dnmonster`:

- créez `dnmonster.yaml` et collez-y le code suivant :

`dnmonster.yaml` :
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: dnmonster 
  labels:
    app: monsterstack
spec:
  selector:
    matchLabels:
      app: monsterstack
      partie: dnmonster
  strategy:
    type: Recreate
  replicas: 5
  template:
    metadata:
      labels:
        app: monsterstack
        partie: dnmonster
    spec:
      containers:
      - image: amouat/dnmonster:1.0
        name: dnmonster
        ports:
        - containerPort: 8080
          name: dnmonster
```

Enfin, configurons un troisième deployment `redis` :

`redis.yaml`: 
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis 
  labels:
    app: monsterstack
spec:
  selector:
    matchLabels:
      app: monsterstack
      partie: redis
  strategy:
    type: Recreate
  replicas: 1
  template:
    metadata:
      labels:
        app: monsterstack
        partie: redis
    spec:
      containers:
      - image: redis:latest
        name: redis
        ports:
        - containerPort: 6379
          name: redis
```

#### Exposer notre stack avec des services

Les services K8s sont des endpoints réseaux qui balancent le trafic automatiquement vers un ensemble de pods désignés par certains labels.

Pour créer un objet `Service`, utilisons le code suivant, à compléter :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: <nom_service>
  labels:
    app: monsterstack
spec:
  ports:
    - port: <port>
  selector:
    app: <app_selector> 
    partie: <tier_selector>
  type: <type>
---
```

Ajoutez le code précédent au début de chaque fichier déploiement. Complétez pour chaque partie de notre application :
    <!-- - le nom du service et le nom du tier par le nom de notre programme (`monstericon` et `dnmonster`) -->
    - le nom du service et le nom de la `partie` par le nom de notre programme (`monstericon`, `dnmonster` et `redis`)
    - le port par le port du service
    <!-- - pourquoi pas selector = celui du deployment? -->
    - les selectors `app` et `partie` par ceux du pod correspondant.

Le type sera : `ClusterIP` pour `dnmonster` et `redis`, car ce sont des services qui n'ont à être accédés qu'en interne, et `LoadBalancer` pour `monstericon`.

Appliquez vos trois fichiers.

- Listez les services avec `kubectl get services`.
- Récupérez le NodePort de monstericon (port dans les 30000 à côté de 5000).
- Visitez votre application dans le navigateur avec `minikube service monstericon`.

### Rassemblons les trois objets avec une kustomisation.

Une kustomization permet de résumer un objet contenu dans de multiples fichiers en un seul lieu pour pouvoir le lancer facilement:

- Créez un dossier `monster_stack` pour ranger les trois fichiers:
    - monstericon.yaml
    - dnmonster.yaml
    - redis.yaml
  
- Créez également un fichier `kustomization.yaml` avec à l'intérieur:

```yaml
resources:
    - monstericon.yaml
    - dnmonster.yaml
    - redis.yaml
```

- Essayez d'exécuter la kustomization avec `kubectl apply -k .` depuis le dossier `monster_stack`.

### Ajoutons un ingress (~ reverse proxy) pour exposer notre application sur le port standard

Installons le contrôleur Ingress Nginx avec `minikube addons enable ingress`.
<!-- FIXME: 404... -->
<!-- Installons le contrôleur Ingress Nginx avec `kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/mandatory.yaml`. -->

Il s'agit d'une implémentation de reverse proxy dynamique (car ciblant et s'adaptant directement aux objets services k8s) basée sur nginx configurée pour s'interfacer avec un cluster k8s.

Ajoutez également l'objet de configuration du loadbalancer suivant dans le fichier `monster-ingress.yaml` :

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: monster-ingress 
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
        - path: /monstericon
          backend:
            serviceName: monstericon
            servicePort: 9090
```

- Ajoutez ce fichier à notre `kustomization.yaml`

- Relancez la kustomization.

<!-- Vous pouvez normalement accéder à l'application sur `http://localhost/monstericon` -->
<!-- FIXME: poor workflow -->
Vous pouvez normalement accéder à l'application en faisant `minikube service monstericon --url` et en ajoutant `/monstericon` pour y accéder.

### Solution

Le dépôt Git de la correction de ce TP est accessible ici : <https://github.com/Uptime-Formation/tp2_k8s_monsterstack_correction>

