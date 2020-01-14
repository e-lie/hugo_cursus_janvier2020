---
title: 'TP Kubernetes'
draft: false
---

## Démarrage: créer un cluster Kubernetes avec microk8s

### Installer **microk8s**

sudo snap install microk8s --edge --classic

### Enable microk8s features
microk8s.enable dashboard

## Installer le client Kubernetes Kubectl

- Ajouter le dépot officiel kubernetes pour Ubuntu : `echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list`
- Mettre à jour les dépôts et installer **kubectl** : `sudo apt update && sudo apt install -y kubectl`

## Configurer kubectl pour se connecter au cluster microk8s

`microk8s.config > ~/.kube/config`


<!-- jx install --provider=kubernetes --external-ip 10.2.3.4 \
--ingress-service=default-http-backend \
--ingress-deployment=default-http-backend \
--ingress-namespace=default \
--on-premise \
--domain=devlab.rs -->


### Bash completion

```bash
apt-get install bash-completion
source <(kubectl completion bash)
echo "source <(kubectl completion bash)" >> ${HOME}/.bashrc
```

## Explorer un cluster k8s

`kubectl get nodes`

`kubectl describe node/<votrenode>`

`kubectl get all`

`kubectl get namespaces`

`kubectl get all -n kube-system`

`kubectl get all --all-namespaces`

`kubectl describe namespace/kube-system`

### Pods

`kubectl get pods -n kube-system`

`kubectl describe -n kube-system pod/kubernetes-dashboard-<xxxx-xx>`

`kubectl logs <pod>`

`kubectl attach -n kube-system -it pod/kubernetes-dashboard-<xxxx-xx>`

Go into a container:

`kubectl get pods -n kube-system`

`kubectl exec -it pod/monitoring-influxdb-grafana-<xxxx-xx> -- sh`


`kubectl top nodes`


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