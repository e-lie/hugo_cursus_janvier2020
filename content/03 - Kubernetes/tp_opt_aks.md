---
draft: true
---

## Exercices avec AKS
- Basique : https://docs.microsoft.com/fr-fr/azure/aks/kubernetes-walkthrough

- Difficile : https://github.com/Microsoft/RockPaperScissorsLizardSpock

- Juste comme il faut, avec des *charts* : https://docs.bitnami.com/kubernetes/get-started-aks/


### Bitnami Helm Wordpress avec AKS
```
# Install Azure CLI
# TODO:

# Login
az login --allow-no-subscriptions

# Create resource group
az group create --name aks-resource-group --location eastus

# Create AKS cluster
az aks create --name aks-cluster --resource-group aks-resource-group --node-count 2 --generate-ssh-keys

# Get AKS config
az aks get-credentials --name aks-cluster --resource-group aks-resource-group

# Install kubectl
# TODO:

# Create registry
az acr create --resource-group aks-resource-group  --name pommedeterrepoirekiwi --sku Basic
az acr login --name pommedeterrepoirekiwi

# Push image to it
docker pull docker.io/bitnami/wordpress:latest
docker tag docker.io/bitnami/wordpress:latest pommedeterrepoirekiwi.azurecr.io/bitnami/wordpress:latest
docker push pommedeterrepoirekiwi.azurecr.io/bitnami/wordpress:latest

# Create account in registry for K8S
ACR_LOGIN_SERVER=$(az acr show --name pommedeterrepoirekiwi --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name pommedeterrepoirekiwi --query id --output tsv)
SP_PASSWD=$(az ad sp create-for-rbac --name k8s-read-registry --role Reader --scopes $ACR_REGISTRY_ID --query password --output tsv)
CLIENT_ID=$(az ad sp show --id http://k8s-read-registry --query appId --output tsv)
kubectl create secret docker-registry read-registry-account \
--docker-server $ACR_LOGIN_SERVER \
--docker-username $CLIENT_ID \
--docker-password $SP_PASSWD \
--docker-email cto@example.org

# Install helm
# TODO:
# Add chart
helm repo add bitnami https://charts.bitnami.com/bitnami

# Run chart
helm install wordpress bitnami/wordpress \
--set serviceType=LoadBalancer \
--set image.registry="pommedeterrepoirekiwi.azurecr.io" \
--set image.pullSecrets={read-registry-account} \
--set image.repository=bitnami/wordpress \
--set image.tag=latest

# Puis suivez les instructions pour y accéder
```


## Documentation
### HPA

https://docs.microsoft.com/fr-fr/azure/aks/tutorial-kubernetes-scale

### Stockage

https://docs.microsoft.com/fr-fr/azure/aks/azure-files-dynamic-pv

<!-- https://docs.microsoft.com/fr-fr/azure/aks/azure-files-dynamic-pv
https://docs.microsoft.com/fr-fr/azure/aks/azure-disks-dynamic-pv
https://docs.microsoft.com/fr-fr/azure/aks/concepts-storage -->

### Registry

- https://docs.microsoft.com/fr-fr/azure/container-registry/container-registry-quickstart-task-cli

### Terraform

- https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/kubernetes_cluster
- https://github.com/terraform-providers/terraform-provider-azurerm/tree/master/examples/kubernetes

- https://registry.terraform.io/modules/Azure/appgw-ingress-k8s-cluster/azurerm/latest
- https://docs.microsoft.com/fr-fr/azure/aks/ingress-basic#create-an-ingress-controller

<!-- ### CRD
https://github.com/Azure/azure-service-operator -->

<!--
### Network
https://docs.microsoft.com/fr-fr/azure/aks/internal-lb
https://docs.microsoft.com/fr-fr/azure/aks/load-balancer-standard
https://docs.microsoft.com/fr-fr/azure/aks/http-application-routing
https://docs.microsoft.com/fr-fr/azure/aks/concepts-network
https://blog.crossplane.io/azure-secure-connectivity-for-aks-azure-db/
https://docs.microsoft.com/fr-fr/azure/mysql/concepts-aks
 -->

### Pour aller plus loin

https://docs.microsoft.com/fr-fr/azure/aks/
https://github.com/microsoft/kubernetes-learning-path
K8s Networking in Azure: https://www.youtube.com/watch?v=JyLtg_SJ1lo&list=PLoWxE_5hnZUZMWrEON3wxMBoIZvweGeiq&index=2
