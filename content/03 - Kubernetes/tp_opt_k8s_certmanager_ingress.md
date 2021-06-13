---
title: TP optionnel - Exposer une application en HTTPS via certmanager et un ingress nginx
draft: true
weight: 2089
---

<!-- Suivre ce tutorial officiel: https://cert-manager.io/docs/tutorials/acme/ingress/#step-2-deploy-the-nginx-ingress-controller -->
Suivre ce tutorial : https://sysadmins.co.za/https-using-letsencrypt-and-traefik-with-k3s/
## Installer un certificat avec k3S

1. Aller sur https://sysadmins.co.za/https-using-letsencrypt-and-traefik-with-k3s/

2. installer K3S : 

`curl -sfL https://get.k3s.io | sh -`

3. installer cert manager (voir tutoriel) (en préfixant par`sudo k3s`)

4.
Objet à créer : 

```
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    email: cto@doxx.fr # replace this
    privateKeySecretRef:
      name: prod-issuer-account-key
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    http01: {}
    solvers:
      - http01:
          ingress:
            class: traefik
        selector: {}
        
    ```