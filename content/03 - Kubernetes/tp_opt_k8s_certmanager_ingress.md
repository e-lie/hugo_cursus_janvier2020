---
title: TP optionnel - Exposer une application en HTTPS via certmanager et un ingress nginx
draft: false
weight: 2089
---

<!-- Suivre ce tutorial officiel: https://cert-manager.io/docs/tutorials/acme/ingress/#step-2-deploy-the-nginx-ingress-controller -->

Suivre ce tutorial : https://sysadmins.co.za/https-using-letsencrypt-and-traefik-with-k3s/

## Installer un certificat avec k3S

1. Installer certmanager avec la commande `kubectl apply -f https://github.com/jetstack/cert-manager/releases/download/v0.11.0/cert-manager.yaml`

2. Objet de type `ClusterIssuer` à créer pour configurer Let's Encrypt :

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  #   name: letsencrypt-staging
  name: letsencrypt-prod
spec:
  acme:
    email: cto@doxx.fr
    privateKeySecretRef:
      name: prod-issuer-account-key
    #   name: staging-issuer-account-key
    server: https://acme-v02.api.letsencrypt.org/directory
    # server: https://acme-staging-v02.api.letsencrypt.org/directory
    http01: {}
    solvers:
      - http01:
          ingress:
            class: traefik
        selector: {}
```

3. Créer un objet Ingress en adaptant celui donné dans le tutoriel (il faudra qu'il soit lié à un Service existant, lui-même lié à un objet Deployment existant) :

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: rancher-logo-ingress
  namespace: logos
  annotations:
    kubernetes.io/ingress.class: traefik
    cert-manager.io/cluster-issuer: letsencrypt-prod
spec:
  tls:
    - secretName: rancher-logo-k3s-ruan-dev-tls
      hosts:
        - rancher-logo.k3s.ruan.dev
  rules:
    - host: rancher-logo.k3s.ruan.dev
      http:
        paths:
          - path: /
            backend:
              serviceName: rancher-logo-service
              servicePort: 80
```
