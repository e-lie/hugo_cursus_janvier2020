---
title: '12 - TP 5 - Déployer Wordpress avec Helm'
draft: true
weight: 2071
---

Helm est un gestionnaire de paquet k8s qui permet d'installer des paquets sans faire des copier-coller pénibles de YAML :

- pas de duplication de code
- des déploiements avancés avec un processus de mise à jour k8s intégré

Helm ne dispense pas de maîtriser l'administration de son cluster.

### Installer Helm

- Pour installer Helm sur Ubuntu, utilisez : `snap install helm --classic`

- Suivez le Quickstart : <https://helm.sh/docs/intro/quickstart/>

### Utiliser un chart Helm pour installer Wordpress

- Cherchez Wordpress sur [https://hub.kubeapps.com](https://hub.kubeapps.com) (vous pouvez prendre une autre chart si le cœur vous en dit).

- Prenez la version de **Bitnami** et ajoutez le dépôt avec la première commande à droite (ajouter le dépôt et déployer une release).

- Installer une release `wordpress-tp` de cette application (ce chart) avec `helm install wordpress-tp bitnami/wordpress`

- Suivez les instructions affichées dans le terminal pour trouver l'IP et afficher le login et password de notre installation.

- Notre Wordpress est prêt. Connectez-vous-y avec les identifiants affichés (il faut passer les commandes indiquées pour récupérer le mot de passe stocké dans un secret k8s).

Vous pouvez constater que l'utilisateur est par default `user` ce qui n'est pas très pertinent. Un chart prend de nombreux paramètres qui sont toujours listés dans le fichier `values.yaml` à la racine du Chart.

- Visitez le code de ce chart ici: https://github.com/bitnami/charts/tree/master/bitnami

- Regardes en particulier les fichiers `templates` et le fichier de paramètre `values.yaml`, Cherchez comment modifier l'username et le password wordpress d'installation ?

- Désinstallez la release avec `helm uninstall wordpress-tp`

- Créez un dossier TP5 avec à l'intérieur un fichier `values.yaml` contenant:

```
wordpressUsername: <votrenom>
wordpressPassword: <easytoguesspasswd>
```

- En utilisant ces paramètres auxquels vous pouvez en ajouter d'autres identifié dans le dépot du projet, faites un rendu (templating) des fichiers du chart dans un grand fichier à la racine du  projet en lançant: `helm template wordpress-tp bitnami/wordpress --values=values.yaml >> wordpress-tp-fullresources.yaml`

- Vous pouvez maintenant explorer ce grand fichier pour comprendre comment wordpress sera installé. En le parcourant on peut contstater que la plupart des objets abordés pendant cette formation y sont présent plus certains autres.

- Pour installer notre wordpress avec ces ressources en mode fichier vous pouvez simplement faire `kubectl apply -f wordpress-tp-fullresources.yaml`.