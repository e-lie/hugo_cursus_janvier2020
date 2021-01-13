---
title: 'TP2 - Déployer des conteneurs'
draft: false
---

La première partie de ce TP va consister à créer des objets Kubernetes pour déployer notre stack `monster_icon`.

Mais d'abord créons une application d'exemple Wordpress+MySQL.

### Installer un Wordpress+MySQL d'exemple

Suivez ce tutoriel : <https://kubernetes.io/docs/tutorials/stateful-application/mysql-wordpress-persistent-volume/>

### Pods

Les pods sont des ensembles de conteneurs toujours gardés ensembles.

Nous voudrions déployer notre stack `monster_stack`. Nous allons commencer par créer un pod avec seulement notre conteneur `monster_icon`.

- Créez un projet vide `monster_stack_k8s`.
- Copiez la définition de pod du cours dans un fichier `monster-pod.yaml`.
<!-- FIXME: uploader `monster_icon`  sur le docker hub -->
- Complétez-la avec l'image de conteneur `tecpi/monster_icon:0.1`, cela récupérera l'image préalablement uploadée sur le Docker Hub (à la version 0.1)
- Complétez en mettant `monster-pod` pour le nom du pod et `monster-icon` pour le conteneur (les `_` sont interdits dans les noms/hostnames)
- Complétez le port en mettant le port de production de notre application, `9090` <!-- FIXME: What? (cf docker stack lancement par défaut du conteneur) -->
- Vérifiez que l'application fonctionne bien en :
  - lançant `kc get pods` pour vérifier que le conteneur du pod tourne
  - lançant `kc logs <pod>`
  - forwardant le port de l'application avec `kc port-forward <pod> 19090:<port_interne>` puis en vous connectant à `localhost:19090`
- Monitorez les processus avec `kc top pods`.

<!-- #### Installer Kubematic

Kubematic est une interface graphique sympatique pour Kubernetes.

Elle se connecte en utilisant la configuration `~/.kube/config` par défaut et nous permettra d'éviter la configuration correcte d'une dashboard pour le moment. En plus est plus agréable à utiliser.

Elle est très récente et ne dispose par de paquet ubuntu/debian. Vous pouvez l'installer en suivant [ce tutoriel](https://thenewstack.io/install-the-kubernetic-desktop-gui-on-ubuntu-linux-19-10/) -->

<!-- 
#### Installer Lens

Lens est une interface graphique sympatique pour Kubernetes.

Elle se connecte en utilisant la configuration `~/.kube/config` par défaut et nous permettra d'accéder à un dashboard bien plus agréable à utiliser.

Vous pouvez l'installer à cette adresse : <https://k8slens.dev> -->

<!-- #### Ajoutons `dnmonster`

Maintenant que nous savons créer un pod nous pouvons ajouter à l'intérieur notre service `dnmonster` de backend d'icône. Les deux services sont peu couplés mais cela ne semble pas a priori stupide de les déployer et scaler ensemble. -->


- Ajoutez une variable d'environnement au conteneur dans le pod avec la syntaxe :

```yaml
    env:
    - name: CONTEXT
      value: "DEV"
```

<!-- - Ajoutez le conteneur au pod `monster-pod`. -->
- Appliquez la configuration avec `apply`
- Quel est le problème ? => le pod est la plus petite unité de déploiement de k8s. Les conteneurs dans un pod sont toujours déployés ou détruits ensembles. En bref, un pod est immutable.
<!-- - C'est un peu un problème si l'on veut déployer une nouvelle version de `dnmonster` indépendemment de `monster_icon`. Poursuivons malgré tout. -->
- Détruisez d'abord le pod avec `kc delete ...` (-f ou nom du pod)


<!-- TODO: Test this -->
- Changez le port pour `5000`.
- Appliquez les modifications en recréant le pod avec `apply`.
- vérifiez avec `kc logs monster-pod monster-icon` que le programme est lancé en mode DEV. En DEV l'application est servie sur `0.0.0.0:5000` c'est à dire sur toute les interfaces.
<!-- - C'est important car nous voulons essayer d'y accéder depuis le pod `dnmonster`. -->


- Recréez le pod avec `apply`.
- Lorsque `kc get pods | grep monster-pod` affiche `2/2`, refaites le port-forward et chargez l'application dans le navigateur.

<!-- FIXME: WTF?? faire un autre déploiement du coup ?-->
<!-- L'icône n'apparait toujours pas.

- pour debugger, connectez-vous au conteneur monster-icon dans le pod avec `kc exec -it monster-pod -c monster-icon -- bash`
  - lancez `wget http://dnmonster:8080` effectivement dnmonster n'est pas accessible car les deux conteneurs partage la même interface et la même IP.
  - deconnectez vous avec `exit` et connectez vous à `dnmonster`.
  - lancez `wget http://localhost:5000` : la page se télécharge => les différents processus du conteneur sont bien accessibles sur localhost.

C'est un problème car notre application a été conçue en mode microservice et le nom de domaine de `dnmonster` est écrit en dur dans notre code. Nous pourrions modifier l'application pour se connecter à l'autre sur localhost mais ce serait du travail inutile.

Conclusion: un Pod a été conçu pour héberger les différents processus d'une même instance d'exécution (par exemple le processus principal et un processus du nettoyage des fichiers de cache ou un processus de monitoring du premier processus) et non pas les différents microservices d'une application distribuée comme pour monsterstack. -->

<!-- - Supprimez `dnmonster` du pod -->

- Ajoutez le code suivant à la fin de `monster-pod.yaml` : alignez le avec `image`

```yaml
      resources:
        requests:
          cpu: "100m"
          memory: "50Mi"
      livenessProbe:
        httpGet:
          path: /
          port: 5000
        initialDelaySeconds: 5
        timeoutSeconds: 1
        periodSeconds: 10
        failureThreshold: 3
```

Notre pod aura alors **la garantie** de disposer d'un dixième de CPU et de 50 mégaoctets de RAM. De plus, k8s sera capable de savoir si le conteneur fonctionne bien en appelant la route `/`. C'est une bonne pratique pour que Kubernetes sache quand redémarrer un pod.

Pour déployer notre stack de microservices nous allons utiliser des **services k8s**. Mais d'abord, passons à l'échelle supérieure avec les déploiements.

##### Correction de `monster-pod.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: monster-pod
spec:
  containers:
    - image: tecpi/monster_icon:0.1
      name: monster-icon
      ports:
        - containerPort: 9090
          name: http
          protocol: TCP
      env:
        - name: CONTEXT
          value: "DEV"
      resources:
        requests:
          cpu: "100m"
          memory: "50Mi"
      livenessProbe:
        httpGet:
          path: /
          port: 5000
        initialDelaySeconds: 5
        timeoutSeconds: 1
        periodSeconds: 10
        failureThreshold: 3
```

#### Déploiements et ReplicaSets

Pour répliquer notre application nous pourrions créer plusieurs instances de pod à la main. Mais bien sur ce n'est pas du tout la philosophie de l'orchestration et ce serait vite complètement contreproductif.

Kubernetes utilise les `ReplicaSets` pour gérer la multiplication d'un même type de pod. Ces ReplicaSets ne sont pas faits pour être créés à la main mais grâce à un objet de type `Deployment`.

Les déploiements emballent des `ReplicaSets` et servent à gérer le déploiement et l'update de versions de l'application à l'aide d'une *rollout policy* (stratégie de mise à jour).

- Créez le fichier de déploiement suivant:

`monster-icon-deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: monstericon 
  labels:
    <labels>
```

Ce fichier exprime un objet déploiement vide. Les objets dans Kubernetes sont hautement dynamiques. Pour les associer et les manipuler on leur associe des `label` c'est à dire des étiquettes avec lesquelles on peut les retrouver ou les matcher précisément. C'est grâce à des labels que k8s associe les `pods` aux `ReplicaSets`.

- Ajoutez le label `app: monsterstack` à cet objet `Deployment`.

- Pour le moment notre déploiement n'est pas défini car il n'a pas de section `spec:`.
  
- La première étape consiste à proposer un modèle de `ReplicaSet` pour notre déploiement. Ajoutez à la suite :

```yaml
spec:
  template:
    spec:
```

- Récupérez la section `spec` de notre pod `monster-pod` et collez la à la suite. Ainsi nous décrivons comme précédemment les pods que nous voulons mettre dans notre deploiement.

- Pour désigner ces pods et les associer à un ReplicaSet il faut ajouter des labels. Ajoutez à la suite au même niveau que la spec du pod :

```yaml
    metadata:
      labels:
        app: monsterstack
        tier: monstericon
```

- Pour créer un ReplicaSet il faut aussi préciser le nombre de replicas. Ajoutez au même niveau d'indentation que `template:` une ligne `replicas: 3`

A ce stade nous avons décrit les pods de notre déploiement avec leurs étiquettes.

Maintenant il s'agit de rajouter quelques options pour paramétrer notre ReplicaSet :

```yaml
  replicas: 3
  selector:
    matchLabels:
      app: monsterstack
      tier: monstericon
  strategy:
    type: Recreate
```

Cette section indique le nombre de réplicas de notre pod et les labels à utiliser pour repérer les pods du ReplicaSet parmi les autres.

Enfin est précisée la stratégie de mise à jour (rollout) des pods pour le déploiement : `Recreate` désigne la stratégie la plus brutale de suppression complète des pods puis de redéploiement.


#### Appliquer notre déploiement

- Avec la commande `apply -f` appliquez notre fichier de déploiement.
- Affichez les déploiements avec `kc get deploy -o wide`.
- Affichez également les replicasets avec `kc get replicasets -o wide`.
- Listez également les pods et faites `describe` sur l'un des pods monstericon. On peut constater que les annotations ont bien été transmises à chaque pod de notre déploiement.

##### Correction du déploiement monstericon

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
```

#### Déploiement semblable pour dnmonster

Maintenant nous allons également créer un déploiement pour `dnmonster`:

- copiez `monster-icon-deployment.yaml` en `dnmonster-deployment.yaml`
- modifiez tous les `monstericon` en `dnmonster` avec un copier et remplacer.
- Changeons également la section `containers` pour qu'elle s'adapte au conteneur `dnmonster`.
  - changez le port en `8080`
  - supprimez la section `env` inutile
- Enfin mettez le nombre de `replicas` à `5`.

<!-- TODO: test -->
<!-- Avec le même procédé, configurez le déploiement redis. -->

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
    tier: <tier_selector>
  type: <type>
---
```

Ajoutez le code suivant au début de chaque fichier déploiement. Complétez pour chaque partie de notre application :
    - le nom du service et le nom du tier par le nom de notre programme (`monstericon` et `dnmonster`)
    <!-- - le nom du service et le nom du tier par le nom de notre programme (`monstericon`, `dnmonster` et `redis`) -->
    - le port par le port du service
    - les selector app et tier par ceux du déploiement correspondant.

Le type sera : `ClusterIP` pour `dnmonster`<!-- et redis--> et `LoadBalancer` pour `monstericon`.

Appliquez vos trois fichiers.

- Listez les services avec `kc get services`.
- Récupérez le port de monstericon.
- Visitez votre application en localhost sur ce port dans le navigateur.

### Rassemblons les trois objets avec une kustomisation.

Une kustomization permet de résumer un objet contenu dans de multiples fichiers en un seul lieu pour pouvoir le lancer facilement:

- Créez un dossier `monster_stack` pour ranger les trois fichiers:
    - monster-icon-deployment.yaml
    - dnmonster-deployment.yaml
    - redis-deployment.yaml
  
- Créez également un fichier `kustomization.yaml` avec à l'intérieur:

```yaml
resources:
    - monster-icon-deployment.yaml
    - dnmonster-deployment.yaml
    - redis-deployment.yaml
```

- Essayez d'exécuter la kustomization avec `kc apply -k .` depuis le dossier `monster_stack`.

### Ajoutons un loadbalancer ingress pour exposer notre application sur le port standard

Installons le contrôleur Ingress Nginx avec `kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/mandatory.yaml`.

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
            servicePort: 5000
```

- Ajoutez ce fichier à notre `kustomization.yaml`

- Relancez la kustomization.

Vous pouvez normalement accéder à l'application sur `http://localhost/monstericon`

### Correction

Le dépôt Git de correction est accessible ici : <https://github.com/Uptime-Formation/tp2_k8s_monsterstack_correction>


