---
title: '07 - TP 3 - Déployer des conteneurs de A à Z'
draft: true
weight: 2050
---

Ce TP va consister à créer des objets Kubernetes pour déployer une application microservices plutôt simple : `monsterstack`.
Elle est composée :
- d'un front-end en Flask (Python) appelé `monstericon`,
- d'un service de backend qui génère des images (un avatar de monstre correspondant à une chaîne de caractères) appelé `dnmonster`
- et d'un datastore `redis` servant de cache pour les images de monstericon

## Déploiement du frontend `monstericon`

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
        - containerPort: 5000

```

- Appliquez cette ressource avec `kubectl` et vérifiez dans `Lens` que les 3 réplicats sont bien lancés.

#### Santé du service avec les `Probes`

- Ajoutons un healthcheck de type `livenessProbe` au conteneur dans le pod avec la syntaxe suivante (le mot-clé `livenessProbe` doit être à la hauteur du `i` de `image:`) :

```yaml
        livenessProbe:
          httpGet:
            path: /
            port: 5000
          initialDelaySeconds: 5
          timeoutSeconds: 1
          periodSeconds: 10
          failureThreshold: 3
          
```

La **livenessProbe** est un test qui s'assure que l'application est bien en train de tourner. S'il n'est pas remplit le pod est automatiquement supprimé et recréé en attendant que le test fonctionne.

Ainsi, k8s sera capable de savoir si notre conteneur applicatif fonctionne bien en appelant la route `/`. C'est une bonne pratique pour que le  `replicaset` Kubernetes sache quand redémarrer un pod et garantir que notre application se répare elle même (self-healing).

Cependant une application peut être en train de tourner mais indisponible pour cause de surcharge ou de mise à jour par exemple. Dans ce cas on voudrait que le pod ne soit pas détruit mais que le traffic évite l'instance indisponible pour être renvoyé vers un autre backend `ready`.

#### Configuration d'une application avec des variables d'environnement

- Notre application monstericon doit être configurée en mode DEV pour fonctionner dans le contexte de ce TP exposée sur le port 5000 (sinon par défaut elle tourne sur 9090). Pour cela elle attend une variable d'environnement `CONTEXT` pour lui indiquer si elle doit se lancer en mode `PROD` ou en mode `DEV`. Ici nous devons mettre l'environnement `DEV` en ajoutant (aligné avec la livenessProbe):


```yaml
        env:
        - name: CONTEXT
          value: DEV
```

#### Ajouter des indications de ressource nécessaires pour garantir la qualité de service

- Ajoutons aussi des contraintes sur l'usage du CPU et de la RAM, en ajoutant à la même hauteur que `env:` :

```yaml
      resources:
        requests:
          cpu: "100m"
          memory: "50Mi"
```

Nos pods auront alors **la garantie** de disposer d'un dixième de CPU (100/1000) et de 50 mégaoctets de RAM. Ce type d'indications permet de remplir au maximum les ressources de notre cluster tout en garantissant qu'aucune application ne prend toute les ressources à cause d'un fuite mémoire etc.  

- Lancer `kubectl apply -f monstericon.yaml` pour appliquer.
- Avec `kubectl get pods --watch`, observons en direct la stratégie de déploiement `type: Recreate`.
- Avec `kubectl describe deployment monstericon`, lisons les résultats de notre `readinessProbe`, ainsi que comment s'est passée la stratégie de déploiement `type: Recreate`

## Un déploiement pour le backend d'image `dnmonster`

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

- Enfin, configurons un troisième deployment `redis.yaml`:

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

Les services K8s sont des endpoints réseaux qui balancent le trafic automatiquement vers un ensemble de pods désignés par certains labels. Ils sont un peu la pierre angulaire des applications microservices qui sont composées de plusieurs sous parties elles même répliquées.

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

- Appliquez à nouveau vos trois fichiers.
- Listez les services avec `kubectl get services`.
- Visitez votre application dans le navigateur avec `minikube service monstericon`.

### Rassemblons les trois objets avec une kustomisation.

Une kustomization permet:

- De résumer un objet contenu dans de multiples fichiers en un seul lieu pour pouvoir les manipuler facilement (mais sans avoir des fichiers à rallonge):
- De surcharger la description yaml de certaines ressources sans modifier le fichier original (en gros de patcher nos ressource à la volée au moment du `apply`).

Créez un fichier `kustomization.yaml` avec à l'intérieur:

```yaml
resources:
    - monstericon.yaml
    - dnmonster.yaml
    - redis.yaml
```
- Essayez d'exécuter la kustomization avec `kubectl apply -k .`.


On pourrait utiliser ici la fonctionnalité de surcharge de `kustomize` pour passer monstericon en mode `PROD` sur le port `9090` en remplaçant la variable d'environnement les numéros de port avec un patch (sans toucher au fichier monstericon.yaml). (Facultatif: trouvez comment utiliser cette fonctionnalité)

### Ajoutons un ingress (~ reverse proxy) pour exposer notre application sur le port standard

- Installons le contrôleur Ingress Nginx avec `minikube addons enable ingress`.

Il s'agit d'une implémentation de reverse proxy dynamique (car ciblant et s'adaptant directement aux objets services k8s) basée sur nginx configurée pour s'interfacer avec un cluster k8s.

- Repassez le service `monstericon` en mode `ClusterIP`. Le service n'est plus accessible sur un port. Nous allons utilisez l'ingress à la place pour afficher la page

- Ajoutez également l'objet `Ingress` de configuration du loadbalancer suivant dans le fichier `monster-ingress.yaml` :

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
            servicePort: 5000
```

- Ajoutez ce fichier à notre `kustomization.yaml` et appliquez le. Il y a un warning: l'API (ie la syntaxe) de kubernetes a changé depuis l'écriture du TP et il faudrait réécrire ce fichier ingress pour intégrer de petites modifications de syntaxe.

- Allez observer l'objet `Ingress` dans `Lens` dans la section `Networking`. Sur cette ligne, récupérez l'ip de minikube en `192.x.x.x.`.

- Visitez la page `http://192.x.x.x/monstericon` pour constater que notre Ingress (reverse proxy) est bien fonctionnel.

### Solution

Le dépôt Git de la correction de ce TP est accessible ici : <https://github.com/Uptime-Formation/tp2_k8s_monsterstack_correction>

