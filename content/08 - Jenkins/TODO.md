# TODO Jenkins

Add examples of public Jenkins Servers, eg https://jenkins.linuxcontainers.org/


### Ordre des choses à faire durant les 4j Jenkins

- Expliquer la CI/CD
- Faire un cours de bilan de ce qu'on a besoin pour une CI/CD
    - un (ou plusieurs) cluster ou déployer
    - un Jenkins (ou autre CI/CD) connecté à Kubernetes (dans Kubernetes ou non)
    - un endroit ou pousser nos images (ne pas oublier de les pruner automatiquement sinon serveur plein en 5jours)
    - une gestion des certificats

1. revoir l'installation de k3s/plus compliqué
2. revoir Kubernetes et le déploiement de l'application Python
3. installer cert-manager avec digitalocean token dans un env.dist
4. Installer Jenkins dans kubernetes
5. Lancer un pipeline python de test, build
6. Installer docker-repository
7. Ajouter le build et push au pipeline (unsecure method)
8. 