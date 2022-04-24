---
title: 10 - Principes de conception et architecture détaillée de Kubernetes
draft: true
weight: 2100
---

Le concept de configuration déclarative est l'un des principaux fondements du développement de Kubernetes : un utilisateur déclare un état souhaité du système pour produire un résultat.

Exemple : "Je veux qu'il y ait 3 instances de mon serveur en fonctionnement à tout moment". Kubernetes, prend cette information déclarative et se charge de la rendre effective.

La puissance de cette approche déclarative (qui est plus complexe au départ que l'approche impérative) est qu'en donnant au système une déclaration de l'état que vous souhaitez plutôt qu'une série d'instructions de bas niveau vous lui permettez de prendre des mesures autonomes. Il peut mettre en œuvre des comportements automatiques pour se réparer lui-même sans vous réveiller au milieu de la nuit.

Et ce cadre déclaratif de haut niveau peut servir de base à une automatisation extrêmement dynamique de nombreuses tâche opérationnelles. (nous parlerons des opérateurs plus tard)

## Contrôleurs et boucles de réconciliation

Kubernetes est basé une approche décentralisée (plutôt que monolithique) : Au lieu d'un unique contrôleur monolithique, il est composé d'un grand nombre de contrôleurs, chacun effectuant sa propre boucle de réconciliation indépendante pour autocorriger l'état du système.

- Chaque boucle individuelle n'est responsable que d'une petite partie du système (par exemple, la mise à jour de la liste des `endpoints` pour une ressource `Service/Endpoint` particulière)

- Chaque contrôleur `ignore l'état global du système` ce qui rend l'ensemble beaucoup `plus stable` : chaque contrôleur n'est pas affecté par des problèmes ou des changements sans rapport direct avec lui.

- Un inconvénient de cette approche distribuée est que le comportement global peut être `plus difficile à comprendre`, car il n'y a pas d'endroit unique où chercher une explication : il est nécessaire d'examiner le fonctionnement coordonné d'un grand nombre de processus indépendants.

Une boucle de réconciliation répète sans arrêt les étapes suivante :

1. Obtenir l'état souhaité.
2. Observer le système.
3. Trouver les différences entre l'observation et l'état souhaité.
4. Prendre des mesures pour que le système corresponde à l'état souhaité.

Concrêtement : "Je veux trois instances de ce serveur " => Le contrôleur de réplication (des `replicatsets`) prend cet état souhaité observe ensuite le système => il y a actuellement trois instances du conteneur de serveur => Le contrôleur
Le contrôleur constate la différence entre l'état actuel et l'état souhaité (un serveur manquant)  => il prend des mesures pour que l'état actuel corresponde en créant un quatrième conteneur de serveur

## Regroupement dynamique par `labels`

Le défi dans cette configuration de contrôleurs dynamiques et distribués est de `déterminer l'ensemble des ressources auxquelles la boucle de réconciliation doit prêter attention`. Lorsqu'il s'agit de regrouper des éléments dans un ensemble deux approches possibles : le regroupement `explicite/statique` ou `implicite/dynamique`.

Avec le regroupement explicite chaque groupe est défini par une liste concrète "Les membres du groupe sont A, B, et C". Mais cette méthode ne peut pas répondre facilement à un système qui change de façon dynamique. 

Avec les groupes implicites, au lieu d'une liste de membres, le groupe est défini par `une déclaration telle que "Les membres du groupe sont les personnes qui portent des vêtements en coton"`.Ils sont implicites récupérés en `évaluant la définition du groupe` par rapport à un ensemble de personnes présentes.

Comme l'ensemble des objets du groupe peut toujours changer, la composition du groupe est dynamique et changeante. `Bien que cela puisse introduire de la complexité` de devoir évaluer à chaque fois les membres, cette méthode est beaucoup `plus flexible et stable dans un environnement changeant` sans nécessiter d'ajustements constants.

Dans Kubernetes chaque objet d'API peut avoir `un nombre arbitraire de  "labels" clé/valeur` qui sont associées aux objets d'API. Vous pouvez ensuite utiliser une requête ou un sélecteur de labels pour identifier un ensemble logique d'objets correspondant à cette requête.

# Retour détaillé sur les composants du `Control Plane`

Kubernetes adhère à la philosophie (Unix) de modularité de petits composants qui font bien leur travail. Kubernetes n'est pas une application d'un seul tenant qui implémente les diverses fonctionnalités du système dans un seul binaire. Il s'agit plutôt d'une collection de différentes applications qui travaillent toutes ensemble, en grande partie en s'ignorant les unes les autres, pour mettre en œuvre le système global.

Même lorsqu'il existe un binaire (par exemple, le `controller manager`) qui regroupe un grand nombre de fonctions différentes, ces fonctions sont maintenues indépendantes les unes des autres dans ce binaire. Elles sont compilées ensemble pour faciliter la tâche de déploiement et de gestion réseaux de Kubernetes.

## etcd

Le composant etcd est au cœur de tout cluster Kubernetes. Il s'agit d'un `key/value store` où tous les objets d'un cluster Kubernetes sont conservés.

Les serveurs etcd utilisent un algorithme de `consensus distribué Raft` qui garantit que même si un des serveurs tombe en panne, la réplication est suffisante pour maintenir les données stockées et pour récupérer automatiquement les données lorsqu'un serveur etcd redevient sain et se réintègre au cluster.

Les serveurs etcd fournissent également deux autres fonctionnalités importantes dont Kubernetes fait un usage intensif: 

- La `concurrence optimiste`. Chaque valeur stockée dans etcd a une version de ressource correspondante. Lorsqu'une paire clé-valeur est écrite sur un serveur etcd, elle peut être conditionnée à une version de ressource particulière. Cela signifie qu'en utilisant etcd, vous pouvez implémenter le principe de `compare and swap`, qui est au cœur de tout système de concurrence. La comparaison et l'échange permettent à un utilisateur de lire une valeur et de la mettre à jour en sachant qu'aucun autre composant du système n'a également mis à jour cette valeur. Ces assurances permettent au système d'avoir en toute sécurité plusieurs threads manipulant des données dans etcd sans avoir besoin de recourir à des `pessimistic locks`, ce qui peut réduire de manière significative le débit du serveur. L'idée qui sous-tend la concurrence optimiste est la capacité d'effectuer la plupart des opérations sans utiliser de verrous (concurrence pessimiste) et, au lieu de cela, de détecter l'apparition d'une écriture concurrente et de rejeter la dernière des deux écritures concurrentes.

- Le protocole `watch`. L'intérêt de `watch` est qu'il permet aux clients de surveiller efficacement les changements dans le `key/value store` pour un grand ensemble de valeurs. Par exemple, tous les objets objets d'un `Namespace` sont stockés dans un répertoire dans etcd. L'utilisation d'une veille permet à un client d'attendre et de réagir efficacement aux changements dans ce `Namespace` sans avoir à interroger en permanence le serveur etcd.

# L'API comme gateway pour toutes les interactions

Un autre concept structurel central de Kubernetes est que toutes les interactions entre les composants sont pilotées à travers une API centralisée : l'API que les composants utilisent est exactement la même API que celle utilisée par tous les autres utilisateurs du cluster:

- Donc aucune partie du système n'est plus privilégiée et aucun composant n'a accès à directement aux données internes.

- Et aussi chaque composant peut être remplacé pour une implémentation alternative sans avoir à reconstruire les composants de base. Même le `Scheduler` peut être échangé ou simplement augmenté par des implémentations alternatives.

Le serveur d'API est la plaque tournante du cluster Kubernetes. actions entre les clients et les objets API stockés dans etcd. Par conséquent, il est le point de rencontre central pour tous les différents composants.



# `Scheduler` : assigner des pods aux noeuds

Avec etcd et le serveur API fonctionnant correctement, un cluster Kubernetes est, d'une certaine manière, fonctionnellement complet. d'une certaine manière, fonctionnellement complet. Vous pouvez créer tous les différents objets de l'API, tels que Deployments et Pods. Cependant, vous constaterez qu'il ne commence jamais à fonctionner. Trouver un emplacement pour l'exécution d'un Pod est le travail de l'ordonnanceur de Kubernetes. Le planificateur scanne le serveur d'API à la recherche de Pods non programmés et détermine ensuite les meilleurs nœuds sur lesquels les exécuter. Comme le serveur d'API, le `scheduler` est un sujet complexe et riche qui est couvert plus en profondeur dans le chapitre 5. plus en profondeur dans le chapitre 5.

# `Controller Manager` : les boucles de réconciliations

Une fois qu'`etcd`, le serveur API et l'ordonnanceur sont opérationnels, vous pouvez créer des pods et les voir programmés. Pods et les voir programmés sur les nœuds, mais vous constaterez que les ReplicaSets, les déploiements et les services ne fonctionnent pas comme vous l'attendez. Cela est dû au fait que toutes les boucles de contrôle de réconciliation nécessaires à la mise en œuvre de cette fonctionnalité ne sont pas actuellement en cours d'exécution. L'exécution de ces boucles est le travail du gestionnaire du contrôleur. Le gestionnaire de contrôleur est le plus varié de tous les composants Kubernetes, puisqu'il possède en son sein de nombreuses boucles de contrôle de réconciliation différentes pour mettre en œuvre cette fonctionnalité. boucles de contrôle de réconciliation différentes pour implémenter de nombreuses parties du système du système Kubernetes global.

# Composants des nœuds de travail

## Kubelet

Le kubelet est le démon de nœud pour toutes les machines qui font partie d'un cluster Kubernetes. Le kubelet est le pont qui relie le CPU, le disque et la mémoire disponibles d'un nœud à la grande grappe Kubernetes. dans le grand cluster Kubernetes. Le kubelet communique avec le serveur API pour pour trouver les conteneurs qui devraient être exécutés sur son nœud. De même, le kubelet communique. De même, le kubelet communique l'état de ces conteneurs au serveur API afin que d'autres boucles de contrôle de réconciliation puissent observer l'état des conteneurs. boucles de contrôle de réconciliation puissent observer l'état actuel de ces conteneurs. En plus d'ordonnancer et de communiquer l'état des conteneurs s'exécutant dans des Pods sur sur leurs machines, les kubelets sont également responsables de la vérification de l'état de santé et du redémarrage des conteneurs qui sont censés s'exécuter sur leurs machines. Il serait assez inefficace de repousser toutes les informations sur l'état de santé vers le serveur API afin que les boucles de réconciliation puissent prendre des mesures pour corriger l'état d'un conteneur sur une machine particulière. Au lieu de cela, le kubelet court-circuite cette interaction et exécute lui-même la boucle de réconciliation. réconciliation elle-même. Ainsi, si un conteneur exécuté par le kubelet meurt ou échoue à son contrôle de santé, le kubelet redémarre. de santé, le kubelet le redémarre, tout en communiquant cet état de santé (et le redémarrage) à l'API.

## Heapster

L'autre composant planifié est un binaire appelé Heapster, qui est chargé de collecter des mesures telles que l'utilisation du CPU, du réseau et du disque de tous les conteneurs s'exécutant dans le cluster Kubernetes. Ces métriques peuvent être poussées vers un système de surveillance, comme InfluxDB, pour l'alerte et la surveillance générale de la santé des applications dans le cluster. De plus, et c'est important, ces mesures sont utilisées pour mettre en œuvre la mise à l'échelle automatique des Pods au sein du cluster Kubernetes. Kubernetes dispose d'une implémentation d'autoscaling, qui, par exemple, qui, par exemple, peut automatiquement augmenter la taille d'un déploiement lorsque l'utilisation du CPU des containers du déploiement dépasse 80 %. Heapster est le composant qui recueille et agrège ces métriques pour alimenter la boucle de réconciliation mise en œuvre par l'autoscaler. L'autoscaler observe l'état actuel du monde à travers des appels API à Heapster.

## kube-proxy et KubeDNS

Voir la partie réseau avancé.


## Serveur API

Bien que etcd soit au cœur d'un cluster Kubernetes, il n'y a en fait qu'un seul serveur qui est autorisé à avoir un accès direct au cluster Kubernetes, et c'est le serveur API. Le serveur API est la plaque tournante du cluster Kubernetes ; il sert de médiateur pour toutes les interactions entre les clients et les objets API stockés dans etcd. Par conséquent, il est le point de rencontre central de tous les différents composants. Le serveur d'API met en œuvre une API RESTful sur HTTP, exécute toutes les opérations d'API et est responsable du stockage des objets d'API dans un backend de stockage persistant.

L'exploitation du serveur d'API Kubernetes implique trois fonctions principales :

- Gestion des API : Le processus par lequel les API sont exposées et gérées par le serveur.
- Traitement des demandes : le plus grand ensemble de fonctionnalités qui traite les demandes d'API individuelles d'un client.
- Boucles de contrôle internes : Internes responsables des opérations de fond nécessaires au bon fonctionnement du serveur d'API.

### Les routes web de et la découverte de l'API

L'API a des routes systématiques pour les ressources :

Voici les composants des deux chemins différents pour les types de ressources namespaced :

- `/api/v1/namespaces/<namespace-name>/<resource-type-name>/<resource-name>`.
- `/apis/<api-group>/<api-version>/namespaces/<namespace-name>/<resource-type-name>/<resource-name>`.

Voici les composants des deux chemins différents pour les types de ressources sans espace-nom :

- `/api/v1/<nom-de-la-ressource>/<nom-de-la-ressource>`
- `/apis/<api-group>/<api-version>/<resource-type-name>/<resource-name>`


Maintenant, nous aimerions explorer l'API et voir comment elle donne des informations pour sa propre exploration. Pour explorer une API REST, l'outil classique `curl` peut suffire.

Pour faciliter l'exploration du serveur API, exécutez l'outil kubectl en mode proxy pour exposer un serveur API non authentifié sur localhost:8001 en utilisant la commande suivante : `kubectl proxy`

Ensuite pour voir comment l'API vous donne de nombreuses informations comme toutes les ressources dans chaque `api-group` vous pouvez lancer : `curl localhost:8001/api` ou `curl localhost:8001/api/v1`

Pour accéder à la page web pour l'ensemble des spécifications de l'api (comme tout point de terminaison swagger/openAPI), ouvrez dans un navigateur : `localhost:8001/openapi/v2`

### Version d'un groupe d'API

- `v1alpha` : instable et ne convient pas aux cas d'utilisation en production. La surface de l'API peut changer entre les versions de Kubernetes et la mise en œuvre de l'API elle-même peut être instable, voire déstabiliser l'ensemble du cluster Kubernetes.

- `v1beta` : La désignation bêta indique que l'API est généralement stable mais qu'elle peut comporter des bogues ou des affinements finaux de sa surface. En général, les API bêta sont supposées être stables entre les versions de Kubernetes, et la compatibilité ascendante est un objectif mais pas toujours atteint. Ces fonctionnalités sont activées dans de nombreux clusters mais doivent être utilisées avec précaution.

- `v1` Disponibilité générale (GA) : indique que l'API est stable. Ces API sont accompagnées d'une garantie de compatibilité ascendante et d'une garantie de dépréciation. Après qu'une API soit marquée comme devant être supprimée, Kubernetes conserve l'API pendant au moins trois versions.

### Vie d'une requête

Pour mieux comprendre ce que fait le serveur d'API pour chacune de ces différentes demandes, nous allons décomposer et décrire le traitement d'une seule demande au serveur d'API.

#### Authentification

La première étape du traitement des demandes est l'authentification, qui établit l'identité associée à la demande. Le serveur API prend en charge plusieurs modes différents d'établissement de l'identité, notamment les certificats clients, les jetons porteurs et l'authentification de base HTTP. En général, les certificats de client ou les jetons de porteur doivent être utilisés pour l'authentification ; l'utilisation de l'authentification de base HTTP est déconseillée.

En plus de ces méthodes locales d'établissement de l'identité, l'authentification est enfichable, et il existe plusieurs implémentations de plug-in qui utilisent des fournisseurs d'identité distants. Il s'agit notamment de la prise en charge du protocole OpenID Connect (OIDC), ainsi que d'Azure Active Directory. Ces plug-ins d'authentification sont compilés à la fois dans le serveur API et dans les bibliothèques client. Cela signifie que vous devrez peut-être vous assurer que les outils de ligne de commande et le serveur API ont à peu près la même version.

#### RBAC/Autorisation

Après que le serveur API ait déterminé l'identité d'une requête, il passe à l'autorisation de celle-ci. Chaque demande adressée à Kubernetes suit un modèle RBAC traditionnel. Pour accéder à une demande, l'identité doit avoir le rôle approprié associé à la demande. Le RBAC de Kubernetes est un sujet riche et compliqué, et nous avons donc consacré un chapitre entier aux détails de son fonctionnement. Aux fins de ce résumé du serveur API, lors du traitement d'une demande, le serveur API détermine si l'identité associée à la demande peut accéder à la combinaison du verbe et du chemin HTTP dans la demande. Si l'identité de la demande a le rôle approprié, elle est autorisée à poursuivre. Dans le cas contraire, une réponse HTTP 403 est renvoyée. Ce point est abordé de manière beaucoup plus détaillée dans un chapitre ultérieur.

#### Contrôle d'admission

Une fois qu'une demande a été authentifiée et autorisée, elle passe au contrôle d'admission. L'authentification et le RBAC déterminent si la demande est autorisée à se produire, et ceci est basé sur les propriétés HTTP de la demande (en-têtes, méthode et chemin). Le contrôle d'admission détermine si la demande est bien formée et applique éventuellement des modifications à la demande avant qu'elle ne soit traitée. Le contrôle d'admission définit une interface enfichable :

`apply(request) : (transformedRequest, error)`

Si un contrôleur d'admission trouve une erreur, la requête est rejetée. Si la demande est acceptée, la demande transformée est utilisée à la place de la demande initiale. Les contrôleurs d'admission sont appelés en série, chacun recevant le résultat du précédent. Comme le contrôle d'admission est un mécanisme si général et pluggable, il est utilisé pour une grande variété de fonctionnalités différentes dans le serveur API. Par exemple, il est utilisé pour ajouter des valeurs par défaut aux objets. Il peut également être utilisé pour appliquer une politique (par exemple, exiger que tous les objets aient une certaine étiquette). En outre, il peut être utilisé pour faire des choses comme injecter un conteneur supplémentaire dans chaque pod. Le service mesh Istio utilise cette approche pour injecter son conteneur sidecar de manière transparente. Les contrôleurs d'admission sont assez génériques et peuvent être ajoutés dynamiquement au serveur API via le contrôle d'admission basé sur les webhooks.

### Demandes spécialisées

En plus des requêtes RESTful standard, le serveur API dispose d'un certain nombre de modèles de requête spécialisés qui fournissent des fonctionnalités étendues aux clients :

`/proxy`, `/exec`, `/attach`, `/logs`.

La première catégorie importante d'opérations est constituée par les connexions ouvertes, de longue durée, au serveur d'API. Ces requêtes fournissent des données en continu plutôt que des réponses immédiates.

`logs` est la demande de streaming la plus facile à comprendre car elle laisse simplement la demande ouverte et fournit plus de données en continu. Les autres opérations tirent parti du protocole WebSocket pour la diffusion bidirectionnelle des données.

En plus de ces flux, le serveur API Kubernetes introduit en fait un protocole de flux multiplexé supplémentaire. La raison en est que, pour de nombreux cas d'utilisation, il est très utile que le serveur API soit capable de gérer plusieurs flux d'octets indépendants. Prenons, par exemple, l'exécution d'une commande dans un conteneur. Dans ce cas, il y a en fait trois flux qui doivent être gérés (stdin, stderr et stdout).

#### Opérations de veille

En plus des données en continu, le serveur API prend en charge une API de surveillance. Une veille surveille un chemin à la recherche de changements. Ainsi, au lieu d'interroger à un certain intervalle pour d'éventuelles mises à jour, ce qui introduit soit une charge supplémentaire (en raison d'une interrogation rapide), soit une latence supplémentaire (en raison d'une interrogation lente), l'utilisation d'une surveillance permet à un utilisateur d'obtenir des mises à jour à faible latence avec une seule connexion. Lorsqu'un utilisateur établit une connexion de veille au serveur API en ajoutant le paramètre de requête `?watch=true` à une demande du serveur API, il peut obtenir des mises à jour à faible latence avec une seule connexion.

#### Les CRD et leur boucle de contrôle

Les définitions de ressources personnalisées (CRD) sont des objets API dynamiques qui peuvent être ajoutés à un serveur API en cours d'exécution. Étant donné que l'acte de création d'une CRD crée intrinsèquement de nouveaux chemins HTTP que le serveur d'API doit savoir comment servir, le contrôleur responsable de l'ajout de ces chemins est situé au même endroit que le serveur d'API. Avec l'ajout des serveurs d'API délégués (décrits dans un chapitre ultérieur), ce contrôleur a en fait été en grande partie abstrait du serveur d'API. Actuellement, il s'exécute toujours en processus par défaut, mais il peut également être exécuté hors processus.

## Débogage du serveur API

Bien sûr, comprendre la mise en œuvre du serveur d'API est une bonne chose, mais le plus souvent, ce dont vous avez vraiment besoin est de pouvoir déboguer ce qui se passe réellement avec le serveur d'API (ainsi que les clients qui appellent le serveur d'API). Le moyen le plus simple d'y parvenir est d'utiliser les journaux que le serveur d'API écrit.

### Journaux de base
Par défaut, le serveur d'API enregistre chaque requête envoyée au serveur d'API. Ce journal inclut l'adresse IP du client, le chemin de la requête et le code que le serveur a renvoyé. Si une erreur inattendue entraîne une panique du serveur, le serveur détecte également cette panique, renvoie un message 500 et consigne cette erreur.

I0803 19:59:19.929302
 1 trace.go:76] Trace [1449222206] :
"Créer /api/v1/namespaces/default/events" (démarré : 2018-08-03
19:59:19.001777279 +0000 UTC m=+25.386403121) (durée totale : 927.484579ms) :
Trace[1449222206] : [927.401927ms] [927.279642ms] Objet stocké dans la base de données
I0803 19:59:20.402215
 1 controller.go:537] admission de quota ajouté
évaluateur pour : { espaces de noms}

Dans ce journal, vous pouvez voir qu'il commence par l'horodatage I0803 19:59 :... quand la ligne de journal a été émise, suivi du numéro de la ligne qui l'a émise, trace.go:76, et enfin le message du journal lui-même.

### Journaux d'audit

Le journal d'audit est destiné à permettre à un administrateur de serveur de récupérer de manière forensique l'état du serveur et la série d'interactions avec les clients qui ont abouti à l'état actuel des données dans l'API Kubernetes. Par exemple, il permet à un utilisateur de répondre à des questions telles que : " Pourquoi ce ReplicaSet a-t-il été mis à l'échelle à 100 ? ", " Qui a supprimé ce Pod ? ", entre autres. Les journaux d'audit ont un backend enfichable pour l'endroit où ils sont écrits.

### Activation de journaux supplémentaires

Kubernetes utilise le paquet github.com/golang/glog leveled logging pour sa journalisation. En utilisant le drapeau --v sur le serveur API, vous pouvez ajuster le niveau de verbosité de la journalisation. En général, le projet Kubernetes a défini le niveau de verbosité de la journalisation à 2 (--v=2).

En plus du débogage du serveur d'API via les journaux, il est également possible de déboguer les interactions avec le serveur d'API, via l'outil de ligne de commande kubectl. Comme le serveur d'API, l'outil de ligne de commande kubectl enregistre les journaux via le paquet github.com/golang/glog et prend en charge l'indicateur de verbosité --v. Définir la verbosité au niveau 10 (`--v=10`)

## Scheduler détaillé

- Positionner un pod avec les affinity

## Manipuler son Cluster

- Mise à jour du cluster

- Drain node et poddisruptionbudget

## Mise en place de l'autoscaling

## Backup du Cluster avec Velero

