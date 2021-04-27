---
title: '12 - TP 5 - Déployer Wordpress avec Helm'
draft: false
weight: 2071
---

Helm est un gestionnaire de paquet k8s qui permet d'installer des paquets sans faire des copier-coller pénibles de YAML :

- pas de duplication de code
- des déploiements avancés avec un processus de mise à jour k8s intégré

Helm ne dispense pas de maîtriser l'administration de son cluster.

### Installer Helm

- Pour installer Helm sur Ubuntu, utilisez : `sudo snap install helm --classic`

- Suivez le Quickstart : <https://helm.sh/docs/intro/quickstart/>

### Utiliser un chart Helm pour installer Wordpress

- Cherchez Wordpress sur [https://artifacthub.io/](https://artifacthub.io/) (vous pouvez prendre une autre chart si le cœur vous en dit).

- Prenez la version de **Bitnami** et ajoutez le dépôt avec la première commande à droite (ajouter le dépôt et déployer une release).

- Installer une release `wordpress-tp` de cette application (ce chart) avec `helm install wordpress-tp bitnami/wordpress`

- Suivez les instructions affichées dans le terminal pour trouver l'IP et afficher le login et password de notre installation. Dans minikube il faut également lancer `minikube service wordpress-tp`.

- Notre Wordpress est prêt. Connectez-vous-y avec les identifiants affichés (il faut passer les commandes indiquées pour récupérer le mot de passe stocké dans un secret k8s).

Vous pouvez constater que l'utilisateur est par default `user` ce qui n'est pas très pertinent. Un chart prend de nombreux paramètres de configuration qui sont toujours listés dans le fichier `values.yaml` à la racine du Chart.

- Visitez le code de ce chart ici: https://github.com/bitnami/charts/tree/master/bitnami

- Regardez en particulier les fichiers `templates` et le fichier de paramètres `values.yaml`, Cherchez comment modifier l'username et le password wordpress d'installation ?

- Désinstallez la release avec `helm uninstall wordpress-tp`

- Créez un dossier TP5 avec à l'intérieur un fichier `values.yaml` contenant:

```
wordpressUsername: <votrenom>
wordpressPassword: <easytoguesspasswd>
```

- En utilisant ces paramètres auxquels vous pouvez en ajouter d'autres identifiés dans le dépot du projet, installez e chart à nouveau avec `helm install wordpress-tp bitnami/wordpress --values=values.yaml`

- Visitez le site `minikube service wordpress-tp`

- Pour savoir qu'est-ce que nous venons d'installer, faites un rendu (templating) des fichiers du chart dans un grand fichier à la racine du  projet en lançant: `helm template wordpress-tp bitnami/wordpress --values=values.yaml >> wordpress-tp-fullresources.yaml`.

- On peut maintenant lire les fichier kubernetes déployés et ainsi apprendre de nouvelles techniques et syntaxes. En le parcourant on peut contstater que la plupart des objets abordés pendant cette formation y sont présent plus certains autres.

- Installez un autre chart comme par exemple `gitlab` en le cherchant dans le menu apps de Lens et cliquant sur installer...
<!-- 
## Installer un certificat HTTPS avec certmanager

- installer k3s sur l'hote (soit en desactivant traefik et installant nginx soit en gardant traefik)
- installer certmanager avec helm dans le nouveau cluster
- créer deux issuers letsencrypt (ou seulement letsencrypt-prod)
- créer un ingress pour wordpress avec à peu pret:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: wordpress-ingress
  annotations:
    kubernetes.io/ingress.class: "nginx"    
    cert-manager.io/issuer: "letsencrypt-prod"
    nginx.ingress.kubernetes.io/force-ssl-redirect: "true"
spec:
  tls:
  - hosts:
    - wordpress.kluster.ptych.net
    secretName: wordpress.kluster.ptych.net-tls
  rules:
  - host: wordpress.kluster.ptych.net
    http:
      paths:
      - path: /
        pathType: Exact
        backend:
          service:
            name: wordpress-test
            port:
              number: 80
``` -->