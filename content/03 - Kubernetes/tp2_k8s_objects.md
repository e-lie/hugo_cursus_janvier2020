---
title: 'TP2 - Déployer des conteneurs'
draft: true
---



## Kubernetes Objects



### Installez l'extension VSCode Kubernetes

### Pods

`kubectl get pods -n kube-system`

`kubectl describe -n kube-system pod/kubernetes-dashboard-<xxxx-xx>`

`kubectl logs <pod>`

`kubectl attach -n kube-system -it pod/kubernetes-dashboard-<xxxx-xx>`

Go into a container:

`kubectl get pods -n kube-system`

`kubectl exec -it pod/monitoring-influxdb-grafana-<xxxx-xx> -- sh`


`kubectl top nodes`


## Nginx ou Contour Ingress sur Digital Ocean

https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nginx-ingress-with-cert-manager-on-digitalocean-kubernetes


## Run your container in pods


### Setup a diagnostic application container : KUARD

```
cd ~/Bureau
git clone https://github.com/kubernetes-up-and-running/kuard`
```

```bash
cd kuard
docker build -t kuard .
docker run -p 8080:8080 --name kuard kuard
```

```bash
docker tag kuard <your_hub_login>/kuard:0.1
docker login
docker push <your_hub_login>/kuard:0.1
```


`kubectl run kuard --generator=run-pod/v1  --image=<your_hub_login>/kuard:0.1`

`kubectl delete pod/kuard`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: kuard
spec:
  containers:
    - image: gcr.io/kuar-demo/kuard-amd64:blue
      name: kuard
      ports:
        - containerPort: 8080
          name: http
          protocol: TCP
```
{{%expand "Réponse" %}}
```bash
kubectl apply -f kuard-pod.yaml`
```
{{% /expand%}}

`kubectl describe pods kuard`

`kubectl delete pod/kuard` ou à partir du fichier `kubectl delete -f kuard-pod.yml`

`kubectl cp $HOME/config.txt <pod-name>:/config.txt`



## Services et Déploiements

```bash
kubectl run alpaca-prod \
  --image=tecpi/kuard:0.1 \
  --replicas=3 \
  --port=8080 \
  --labels="ver=1,app=alpaca,env=prod"

kubectl expose deployment alpaca-prod

kubectl run bandicoot-prod \
  --image=tecpi/kuard:0.1 \
  --replicas=2 \
  --port=8080 \
  --labels="ver=2,app=bandicoot,env=prod"

kubectl expose deployment bandicoot-prod

kubectl get services -o wide
```