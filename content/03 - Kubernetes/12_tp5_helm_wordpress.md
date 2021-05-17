---
title: '12 - TP 5 - Déployer Wordpress avec Helm et ArgoCD'
draft: true
weight: 2071
---

Helm est un "gestionnaire de paquet" ou vu autrement un "outil de templating avancé" pour k8s qui permet d'installer des applications sans faire des copier-coller pénibles de YAML :

- Pas de duplication de code
- Possibilité de créer du code générique et flexible avec pleins de paramètres pour le déploiement.
- Des déploiements avancés avec plusieurs étapes

Inconvénient: Helm ajoute souvent de la complexité non nécessaire car les Charts sur internet sont très paramétrables pour de multiples cas d'usages (plein de code qui n'est utile que dans des situations spécifiques).

Helm ne dispense pas de maîtriser l'administration de son cluster.

### Installer Helm

- Pour installer Helm sur Ubuntu, utilisez : `sudo snap install helm --classic`

- Suivez le Quickstart : <https://helm.sh/docs/intro/quickstart/>

### Utiliser un chart Helm pour installer Wordpress

- Pour installer argocd sur notre cluster k3s lancez: `kubectl config use-context default` puis `kubectl get nodes` pour vérifier.

- Cherchez Wordpress sur [https://artifacthub.io/](https://artifacthub.io/).

- Prenez la version de **Bitnami** et ajoutez le dépôt avec la première commande à droite (ajouter le dépôt et déployer une release).

- Installer une **"release"** `wordpress-tp` de cette application (ce chart) avec `helm install wordpress-tp bitnami/wordpress`

- Suivez les instructions affichées dans le terminal pour trouver l'IP et afficher le login et password de notre installation. Dans minikube il faut également lancer `minikube service wordpress-tp`.

- Notre Wordpress est prêt. Connectez-vous-y avec les identifiants affichés (il faut passer les commandes indiquées pour récupérer le mot de passe stocké dans un secret k8s).

Vous pouvez constater que l'utilisateur est par default `user` ce qui n'est pas très pertinent. Un chart prend de nombreux paramètres de configuration qui sont toujours listés dans le fichier `values.yaml` à la racine du Chart.

On peut écraser certains de ces paramètres dans un nouveau fichier par exemple `myvalues.yaml` et installer la release avec l'option `--values=myvalues.yaml`. Nous allons faire cela avec Argocd à la place de la CLI helm.

- Désinstallez Wordpress avec `helm uninstall wordpress-tp`

### Installer ArgoCD

Argocd est une solution de "Continuous Delivery" dédiée au **GitOps** avec Kubernetes. Elle fourni une interface assez géniale pour détecter et monitorer les ressources d'un cluster.

ArgoCD s'installe grâce à une série de manifestes Kubernetes. Pour récupérer ces manifestes d'installation nous allons utiliser git et le dépôt de correction : `cd ~/Desktop && git clone -b argocd_installation https://github.com/Uptime-Formation/corrections_tp.git argocd_installation`.

L'installation comporte plusieurs étapes qui doivent être exécutées dans l'ordre et en vérifiant s'il n'y a pas d'erreurs à chaque étape.

- Pour être sur d'installer argocd sur notre cluster k3s lancez: `kubectl config use-context default` puis `kubectl get nodes` pour vérifier.
- Les resources Kubernetes d'installation sont dans le dossier cloné précédemment et la partie kubernetes: `cd ~/Desktop/argocd_installation/kubernetes`
- Commençons par créer quelques namespaces (`argocd` et `cert-manager`) pour installer nos différentes applications: `kubectl apply -f argocd-kluster/namespaces.yaml`
- Puis installation de l'ingress controller nginx dans le namespace `kube-system`: `kubectl apply -n kube-system -f argocd-kluster/ingress-nginx`
- Dans Lens vérifiez que le pod `ingres-nginx-controller-xxx` est bien lancé (vert)

Ensuite installons l'application cert-manager qui permet de générer **automatiquement** des certificats TLS pour nos applications web HTTPS (notamment avec letsencrypt et ACME). Argocd à une interface web qui nécessite un accès https.

- Lancez : `kubectl apply -n cert-manager -f argocd-kluster/cert-manager/cert-manager-manifests.yaml`.
- Créons également les **"Issuers"** c'est à dire les composants qui vont permettre d'émettre des certificats avec la commande: `kubectl apply -n cert-manager -f argocd-kluster/cert-manager/issuers`.

Vos serveurs VNC qui sont aussi désormais des clusters k3s on déjà deux sous-domaines configurés: `<votrelogin>.formation.dopl.uk` et `*.<votrelogin>.formation.dopl.uk`. Le sous domaine `argocd.<login>.formation.dopl.uk` pointe donc déjà sur le serveur <(Wildcard DNS). Celà va permettre à `cert-manager` de créer automatiquement un `ACME HTTP Challenge` pour enregistrer un certificat TLS.

- Dans le fichier Changez `argocd-kluster/argocd/argocd-ingress.yaml`, changez `<yourname>` par votre nom (le login guacamole) pour configurer l'ingress sur le nom de domaine de votre cluster personnel.

- Ensuite installez **ArgoCD** avec la commande: `kubectl apply -f argocd-kluster/argocd/manifests`
- Enfin `kubectl apply -f argocd-kluster/argocd/argocd-ingress.yaml`

<!-- ### TODO: Installer et visualiser le chart wordpress avec argocd -->

### La fonction `template` de Helm pour étudier les ressources d'un Chart

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
