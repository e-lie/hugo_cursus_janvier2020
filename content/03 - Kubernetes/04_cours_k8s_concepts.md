---
title: 04 - Cours - Concepts de Kubernetes
draft: false
weight: 2030
---

## Principes d'orchestration

### Haute disponibilité

- Faire en sorte qu'un service ait un "uptime" élevé.

On veut que le service soit tout le temps accessible même lorsque certaines ressources manquent :

- elles tombent en panne
- elles sont sorties du service pour mise à jour, maintenance ou modification

Pour cela on doit avoir des ressources multiples...

- Plusieurs serveurs
- Plusieurs versions des données
- Plusieurs accès réseau

Il faut que les ressources disponibles prennent automatiquement le relais des ressources indisponibles.
Pour cela on utilise en particulier:

- des "load balancers" : aiguillages réseau intelligents
- des "healthchecks" : une vérification de la santé des applications

Nous allons voir que Kubernetes intègre automatiquement les principes de load balancing et de healthcheck dans l'orchestration de conteneurs

### Répartition de charge (load balancing)

- Un load balancer : une sorte d'**"aiguillage" de trafic réseau**, typiquement HTTP(S) ou TCP.
- Un aiguillage **intelligent** qui se renseigne sur plusieurs critères avant de choisir la direction.

Cas d'usage :

- Éviter la surcharge : les requêtes sont réparties sur différents backends pour éviter de les saturer.

L'objectif est de permettre la haute disponibilité : on veut que notre service soit toujours disponible, même en période de panne/maintenance.

- Donc on va dupliquer chaque partie de notre service et mettre les différentes instances derrière un load balancer.

- Le load balancer va vérifier pour chaque backend s'il est disponible (**healthcheck**) avant de rediriger le trafic.
- Répartition géographique : en fonction de la provenance des requêtes on va rediriger vers un datacenter adapté (+ proche).

#### Solutions de load balancing externe

- **HAProxy** : Le plus répandu en load balancing.
- **Traefik** : Simple à configurer et se fond dans l'écosystème des conteneurs Docker et Kubernetes.
- **NGINX** : Serveur web central qui a depuis quelques années des fonctions puissantes de load balancing et TCP forwarding.

### Healthchecks

Fournir à l'application une façon d'indiquer qu'elle est disponible, c'est-à-dire :

- qu'elle est démarrée (_liveness_)
- qu'elle peut répondre aux requêtes (_readiness_).

<!-- #### Exemple: le load balancing de Swarm

- load balancer intégré : Ingress
- Permet de router automatiquement le traffic d'un service vers les noeuds qui l'hébergent et sont disponibles.
- Pour héberger une production il suffit de rajouter un load balancer externe qui pointe vers un certain nombre de noeuds du cluster et le traffic sera routé automatiquement à partir de l'un des noeuds. -->

### Découverte de service (service discovery)

Classiquement, les applications ne sont pas informées du contexte dans lequel elles tournent : la configuration doit être opérée de l'extérieur de l'application.

- par exemple avec des fichiers de configuration fournie via des volumes
- ou via des variables d'environnement

Mais dans un environnement hautement dynamique comme Kubernetes, la configuration externe ne suffit pas pour gérer des applications complexes distribuées qui doivent se déployer régulièrement, se parler et parler avec l'extérieur.

La découverte de service désigne généralement les méthodes qui permettent à un programme de chercher autour de lui (généralement sur le réseau ou dans l'environnement) ce dont il a besoin.

- La mise en place d'un système de **découverte de service** permet de rendre les applications plus autonomes dans leur (auto)configuration.
- Elles vont pouvoir récupérer des informations sur leur contexte (dev ou prod, Etats-Unis ou Europe ?)
- Ce type d'automatisation permet de limiter la complexité du déploiement.

Concrètement, au sein d'un orchestrateur, un système de découverte de service est un serveur qui est au courant automatiquement :

- de chaque conteneur lancé.
- du contexte dans lequel chaque conteneur a été lancé.

Ensuite il suffit aux applications de pouvoir interroger ce serveur pour s'autoconfigurer.

Un exemple historique de découverte de service est le DNS : on fait une requête vers un serveur spécial pour retrouver une adresse IP (on découvre le serveur dont on a besoin).
Cependant le DNS n'a pas été pensé pour ça :

- certaines application ne rafraichissent pas assez souvent leurs enregistrements DNS en cache
- le DNS devient trop complexe à partir de quelques dizaines d'enregistrements
<!-- Le service DNS du réseau overlay de Docker Swarm avec des stacks permet une forme extrêmement simple et implicite de service discovery. Avec le DNS automatique de Swarm votre application microservice docker compose est automatiquement distribuée. -->

#### Solutions de découverte de service 
- **Consul** (Hashicorp) : assez simple d'installation et fourni avec une sympathique interface web.
- **etcd** : a prouvé ses performances à plus grande échelle mais un peu plus complexe
<!-- - (Kubernetes l'utilise en interne pour son control pane, mais pas pour ses applications) -->

<!-- - Kubernetes propose un service discovery extrêmement flexible grâce aux `deployments` et aux `services`. -->

### Les stratégies de déploiement

Il existe deux types de stratégies de *rollout* native à Kubernetes :
- `Recreate` : arrêter les pods avec l'ancienne version en même temps et créer les nouveaux simultanément
- `RollingUpdate` : mise à jour continue, arrêt des anciens pods les uns après les autres et création des nouveaux au fur et à mesure (paramétrable)

Mais il existe un panel de stratégies plus large pour updater ses apps :
- *blue/green* : publier une nouvelle version à côté de l'ancienne puis changer de trafic
- *canary* : diffuser une nouvelle version à un sous-ensemble d'utilisateurs, puis procéder à un déploiement complet
- *A/B testing*: diffusion d'une nouvelle version à un sous-ensemble d'utilisateurs de manière précise (en-têtes HTTP, cookie, région, etc.).
  - pas possible par défaut avec Kubernetes, implique une infrastructure plus avancée avec reverse proxy (Istio, Traefik, nginx/haproxy personnalisé, etc.).

*Source :* <https://blog.container-solutions.com/kubernetes-deployment-strategies>