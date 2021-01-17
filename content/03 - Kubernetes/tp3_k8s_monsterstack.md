---
title: 'TP 3 - Déployer des conteneurs de A à Z'
draft: false
weight: 51
---

<!-- Alternatives :
- Wordpress : https://kubernetes.io/docs/tutorials/stateful-application/mysql-wordpress-persistent-volume/

<!-- FIXME: oups la correction marche mais le TP non...
   -->

<!-- La première partie de ce TP va consister à créer des objets Kubernetes pour déployer un Wordpress avec stockage et base de données. -->
Ce TP va consister à créer des objets Kubernetes pour déployer une stack d'exemple : `monster_stack`.
Elle est composée :
- d'un front-end en Flask (Python),
- d'un backend qui génère des images (un avatar de monstre correspondant à une chaîne de caractères),
- et d'une base de données servant de cache pour ces images, Redis.

Vous pouvez utiliser au choix votre environnement Cloud ou Minikube.
 
### Rappel : Installer Lens

Lens est une interface graphique sympathique pour Kubernetes.

Elle se connecte en utilisant la configuration `~/.kube/config` par défaut et nous permettra d'accéder à un dashboard bien plus agréable à utiliser.

Vous pouvez l'installer en lançant ces commandes :
```bash
sudo apt-get update; sudo apt-get install libxss-dev
curl -fSL https://github.com/lensapp/lens/releases/download/v4.0.6/Lens-4.0.6.AppImage -o ~/Lens.AppImage
chmod +x ~/Lens.AppImage
~/Lens.AppImage
```



<!-- ## Partie 1 : Installer un Wordpress avec base MySQL

Suivons ensemble ce tutoriel : <https://kubernetes.io/docs/tutorials/stateful-application/mysql-wordpress-persistent-volume/> -->



## Déploiement de la stack `monsterstack`

Les pods sont des ensembles de conteneurs toujours gardés ensembles.

Nous voudrions déployer notre stack `monster_app`. Nous allons commencer par créer un pod avec seulement notre conteneur `monstericon`.

- Créez un projet vide `monster_app_k8s`.


- Créez le fichier de déploiement suivant:

`monstericon.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: monstericon 
  labels:
    <labels>
```

Ce fichier exprime un objet déploiement vide. 

- Ajoutez le label `app: monsterstack` à cet objet `Deployment`.

- Pour le moment notre déploiement n'est pas défini car il n'a pas de section `spec:`.
  
- La première étape consiste à proposer un modèle de `ReplicaSet` pour notre déploiement. Ajoutez à la suite (`spec:` doit être à la même hauteur que `kind:` et `metadata:`) :

```yaml
spec:
  template:
    spec:
```

Remplissons la section `spec` de notre pod `monstericon` à partir d'un modèle de pod lançant un conteneur Nginx :
```yaml
        containers:
        - name: nginx
          image: nginx:1.7.9
          ports:
            - containerPort: 80
```
<!-- - Récupérez  et collez la à la suite. Ainsi nous décrivons comme précédemment les pods que nous voulons mettre dans notre deploiement. -->

- Remplacez le nom du conteneur par `monstericon`, et l'image de conteneur par `tecpi/monster_icon:0.1`, cela récupérera l'image préalablement uploadée sur le Docker Hub (à la version 0.1)

<!-- - Complétez en mettant `monstericon-pod` pour le nom du déploiement,  pour le conteneur (les `_` sont interdits dans les noms/hostnames), et `app: monsterstack` pour le label (à 2 endroits) -->
  
- Complétez le port en mettant le port de production de notre application, `9090`

- Les objets dans Kubernetes sont hautement dynamiques. Pour les associer et les désigner on leur associe des `labels` c'est-à-dire des étiquettes avec lesquelles on peut les retrouver ou les matcher précisément. C'est grâce à des labels que k8s associe les `pods` aux `ReplicaSets`. Ajoutez à la suite au même niveau que la spec du pod :

```yaml
    metadata:
      labels:
        app: monsterstack
        partie: monstericon
```

A ce stade nous avons décrit les pods de notre déploiement avec leurs labels (un label commun à tous les objets de l'app, un label plus spécifique à la sous-partie de l'app).

Maintenant il s'agit de rajouter quelques options pour paramétrer notre déploiement  (à la hauteur de `template:`) :

```yaml
  selector:
    matchLabels:
      app: monsterstack
      partie: monstericon
  strategy:
    type: Recreate
```

Cette section indique les labels à utiliser pour repérer les pods de ce déploiement parmi les autres.

Puis est précisée la stratégie de mise à jour (rollout) des pods pour le déploiement : `Recreate` désigne la stratégie la plus brutale de suppression complète des pods puis de redéploiement.

Enfin, juste avant la ligne `selector:` et à la hauteur du mot-clé `strategy:`, ajouter `replicas: 3`. Kubernetes crééra 3 pods identiques lors du déploiement `monstericon`.


<!-- - Copiez la définition d'un deployment issue du cours (ici initialement pour un déploiement de Nginx) dans un fichier `monstericon-pod.yaml` :
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  strategy:
    type: Recreate
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:1.7.9
          ports:
            - containerPort: 80
``` -->

<!--  expand "Le fichier `monstericon.yaml` jusqu'à présent :" -->
Le fichier `monstericon.yaml` jusqu'à présent :
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: monstericon
  labels:
    app: monsterstack
spec:
  template:
    spec:
      containers:
      - name: monstericon
        image: tecpi/monster_icon:0.1
        ports:
        - containerPort: 9090
    metadata:
      labels:
        app: monsterstack
        partie: monstericon
  selector:
    matchLabels:
      app: monsterstack
      partie: monstericon
  strategy:
    type: Recreate
  replicas: 3
```
<!-- /expand -->
#### Appliquer notre déploiement

- Avec la commande `apply -f` appliquez notre fichier de déploiement.
- Affichez les déploiements avec `kubectl get deploy -o wide`.
<!-- - Affichez également les replicasets avec `kubectl get replicasets -o wide`. -->
- Listez également les pods en lançant `kubectl get pods --watch` pour vérifier que les conteneurs tournent.
<!-- - en faisant `describe` sur l'un des pods, on peut constater que les annotations ont bien été transmises à chaque pod de notre déploiement. -->
<!-- - En lançant `kubectl logs <pod>` (utiliser l'autocomplete avec `<TAB>`), vérifiez que les pods s'initialisent bien. -->

<!-- - Appliquez votre fichier avec : `kubectl apply -f monstericon-pod.yaml`
- Vérifiez que l'application fonctionne bien en :
  - lançant `kubectl get deployments` pour vérifier que le déploiement du pod a bien été demandé au cluster
  - forwardant le port de l'application avec `kubectl port-forward <pod> 19090:<port_interne>` puis en vous connectant à `localhost:19090`
- Monitorez les processus avec `kubectl top pods`. -->


<!-- #### Ajoutons `dnmonster`

Maintenant que nous savons créer un pod nous pouvons ajouter à l'intérieur notre service `dnmonster` de backend d'icône. Les deux services sont peu couplés mais cela ne semble pas a priori stupide de les déployer et scaler ensemble. -->


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

Ainsi, k8s sera capable de savoir si le conteneur fonctionne bien en appelant la route `/`. C'est une bonne pratique pour que Kubernetes sache quand redémarrer un pod.

- Ajoutons aussi des contraintes sur l'usage du CPU et de la RAM, en ajoutant à la même hauteur que `image:` :
```yaml
      resources:
        requests:
          cpu: "100m"
          memory: "50Mi"
```

Nos pods auront alors **la garantie** de disposer d'un dixième de CPU et de 50 mégaoctets de RAM.

<!-- 
- Ajoutez une variable d'environnement au conteneur dans le pod avec la syntaxe :

```yaml
    env:
    - name: CONTEXT
      value: "DEV"
``` -->

- Lancer `kubectl apply -f monstericon.yaml` pour appliquer.
- Avec `kubectl get pods --watch`, observons en direct la stratégie de déploiement `type: Recreate`
- Avec `kubectl describe deployment monstericon`, lisons les résultats de notre `readinessProbe`, ainsi que comment s'est passée la stratégie de déploiement `type: Recreate`


<!-- - Ajoutez le conteneur au pod `monster-pod`. -->
<!-- - Appliquez la configuration avec `apply` -->
<!-- - Quel est le problème ? => le pod est la plus petite unité de déploiement de k8s. Les conteneurs dans un pod sont toujours déployés ou détruits ensembles. En bref, un pod est immutable. -->
<!-- - C'est un peu un problème si l'on veut déployer une nouvelle version de `dnmonster` indépendemment de `monster_icon`. Poursuivons malgré tout. -->
<!-- - Détruisez d'abord le pod avec `kubectl delete ...` (-f ou nom du pod) -->


<!-- TODO: Test this -->
<!-- - Changez le port pour `5000`. -->
<!-- - Appliquez les modifications en recréant le pod avec `apply`. -->
<!-- - vérifiez avec `kubectl logs monster-pod monstericon` que le programme est lancé en mode DEV. En DEV l'application est servie sur `0.0.0.0:5000` c'est à dire sur toute les interfaces. -->
<!-- - C'est important car nous voulons essayer d'y accéder depuis le pod `dnmonster`. -->


<!-- - Recréez le pod avec `apply`. -->
<!-- - Lorsque `kubectl get pods | grep monster-pod` affiche `2/2`, refaites le port-forward et chargez l'application dans le navigateur. -->

<!-- L'icône n'apparait toujours pas.

- pour debugger, connectez-vous au conteneur monstericon dans le pod avec `kubectl exec -it monster-pod -c monstericon -- bash`
  - lancez `wget http://dnmonster:8080` effectivement dnmonster n'est pas accessible car les deux conteneurs partage la même interface et la même IP.
  - deconnectez vous avec `exit` et connectez vous à `dnmonster`.
  - lancez `wget http://localhost:5000` : la page se télécharge => les différents processus du conteneur sont bien accessibles sur localhost.

C'est un problème car notre application a été conçue en mode microservice et le nom de domaine de `dnmonster` est écrit en dur dans notre code. Nous pourrions modifier l'application pour se connecter à l'autre sur localhost mais ce serait du travail inutile.

Conclusion: un Pod a été conçu pour héberger les différents processus d'une même instance d'exécution (par exemple le processus principal et un processus du nettoyage des fichiers de cache ou un processus de monitoring du premier processus) et non pas les différents microservices d'une application distribuée comme pour monsterstack. -->

<!-- - Supprimez `dnmonster` du pod -->



<!-- Pour déployer notre stack de microservices nous allons utiliser des **services k8s**. Mais d'abord, passons à l'échelle supérieure avec les déploiements. -->

`monstericon.yaml` final :
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: monstericon
  labels:
    app: monsterstack
spec:
  template:
    spec:
      containers:
      - name: monstericon
        image: tecpi/monster_icon:0.1
        ports:
        - containerPort: 9090
        readinessProbe:
          failureThreshold: 5 # Reessayer 5 fois
          httpGet:
            path: /
            port: 9090
            scheme: HTTP
          initialDelaySeconds: 30 # Attendre 30s avant de tester
          periodSeconds: 10 # Attendre 10s entre chaque essai
          timeoutSeconds: 5 # Attendre 5s la reponse
        resources:
          requests:
            cpu: "100m"
            memory: "50Mi"
    metadata:
      labels:
        app: monsterstack
        partie: monstericon
  selector:
    matchLabels:
      app: monsterstack
      partie: monstericon
  strategy:
    type: Recreate
  replicas: 5
```

<!-- #### Déploiements et ReplicaSets

Pour répliquer notre application nous pourrions créer plusieurs instances de pod à la main. Mais bien sur ce n'est pas du tout la philosophie de l'orchestration et ce serait vite complètement contreproductif. -->

<!-- Kubernetes utilise les `ReplicaSets` pour gérer la multiplication d'un même type de pod. Ces ReplicaSets ne sont pas faits pour être créés à la main mais grâce à un objet de type `Deployment`.

Les déploiements emballent des `ReplicaSets` et servent à gérer le déploiement et l'update de versions de l'application à l'aide d'une *rollout policy* (stratégie de mise à jour). -->


<!-- ##### Correction du déploiement monstericon

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
      tier: monstericon
  strategy:
    type: Recreate
  replicas: 3
  template:
    metadata:
      labels:
        app: monsterstack
        tier: monstericon
    spec:
      containers:
      - image: tecpi/monster_icon:0.1
        name: monstericon
        env:
        - name: CONTEXT
          value: DEV
        ports:
        - containerPort: 5000
          name: monstericon
``` -->

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
```
<!-- - modifiez tous les `monstericon` en `dnmonster` avec un copier et remplacer.
- Changeons également la section `containers` pour qu'elle s'adapte au conteneur `dnmonster`.
  - changez le port en `8080`
  - supprimez la section `env` inutile
- Enfin mettez le nombre de `replicas` à `3`. -->

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
```

<!-- On pourrait à la place mettre un deuxième conteneur `redis` dans le même pod que `dnmonster`. Pourquoi dans le même pod ? La philosophie est que ces deux conteneurs seraient toujours déployés et mis à l'échelle ensemble.
Ici, Redis est un service de cache, donc c'est logique de pouvoir scaler les services Redis et `dnmonster` indépendamment. -->

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

Ajoutez le code suivant au début de chaque fichier déploiement. Complétez pour chaque partie de notre application :
    <!-- - le nom du service et le nom du tier par le nom de notre programme (`monstericon` et `dnmonster`) -->
    - le nom du service et le nom de la `partie` par le nom de notre programme (`monstericon`, `dnmonster` et `redis`)
    - le port par le port du service
    <!-- - pourquoi pas selector = celui du deployment? -->
    - les selectors `app` et `partie` par ceux du ReplicaSet correspondant.

Le type sera : `ClusterIP` pour `dnmonster` et `redis`, car ce sont des services qui n'ont à être accédés qu'en interne, et `LoadBalancer` pour `monstericon`.

Appliquez vos trois fichiers.

- Listez les services avec `kubectl get services`.
<!-- - Récupérez le port de monstericon. -->
- Visitez votre application dans le navigateur avec `minikube service <nom-du-service-monstericon>`.

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

### Ajoutons un loadbalancer ingress pour exposer notre application sur le port standard

Installons le contrôleur Ingress Nginx avec `minikube addons enable ingress`.
<!-- FIXME: 404... -->
<!-- Installons le contrôleur Ingress Nginx avec `kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/mandatory.yaml`. -->

Il s'agit d'une implémentation de loadbalancer dynamique basée sur nginx configurée pour s'interfacer avec un cluster k8s.

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

Le dépôt Git des solutions est accessible ici : <https://github.com/Uptime-Formation/tp2_k8s_monsterstack_correction>

