---
title: 'TP3 - Déployer Jenkins avec Helm'
draft: false
---

Helm est un gestionnaire de paquet k8s qui permet d'installer des paquets sans faire des copier-coller pénibles de YAML :

- pas de duplication de code
- des déploiements avancés avec un processus de mise à jour k8s intégré

Helm ne dispense pas de maîtriser l'administration de son cluster.

### Installer Helm

- Pour installer Helm sur Ubuntu, utilisez : `snap install helm --classic`

- Suivez le Quickstart : <https://helm.sh/docs/intro/quickstart/>

### Utiliser une chart Helm pour installer Mediawiki
<!-- ### Utiliser une chart Helm pour installer Jenkins -->

<!-- TODO: prendre autre chose que Jenkins -->
- Cherchez Mediawiki sur [https://hub.kubeapps.com](https://hub.kubeapps.com).
- Prenez la version de **Bitnami** et ajoutez le dépôt avec la première commande à droite (ajouter le dépôt et déployer une release).
- Installer une release `mediawiki-tp` de cette application (ce chart) avec `helm install --template-name mediawiki-tp bitnami/mediawiki`
- Suivez les instructions affichées
<!-- - Plutôt que de faire un port-forwarding, nous allons configurer le service k8s de `jenkins-master` pour être en mode `NodePort`. -->
  <!-- - Créez un fichier `config_jenkins.yaml` avec à l'intérieur: -->
<!-- 
```yaml
service:
    master:
        type: "NodePort"
``` -->
<!-- - Appliquez cette config à notre release avec `helm upgrade -f config_jenkins.yaml jenkins-tp codecentric/jenkins`. -->
<!-- - Cherchez le port d'exposition du service avec `kc get services | grep jenkins` -->
<!-- - Visitez [http://localhost:<node_port>](http://localhost:<node_port>) -->
<!-- - Récupérez le password d'inititalisation précédemment sauvegardé et collez-le dans le navigateur -->
- Notre Mediawiki est prêt. Connectez-vous-y avec les identifiants affichés.

- Explorez les différents objets k8s créés par Helm avec Lens.

<!-- - Cherchez Jenkins sur [https://hub.kubeapps.com](https://hub.kubeapps.com).
- Prenez la version de **codecentric** et ajoutez le dépot avec la première commande à droite (ajouter le dépot et déployer une release).
- Installer une release `jenkins-tp` de cette application (ce chart) avec `helm install --template-name jenkins-tp codecentric/jenkins`
- Cherchez le nom du pod `jenkins-master`
- Affichez les logs du pod avec `kc logs` et récupérez la clé d'initialisation qui se trouve entre les triples lignes d'étoiles. Notez-la dans un fichier texte (`gedit key.tmp` ?)
- Plutôt que de faire un port-forwarding, nous allons configurer le service k8s de `jenkins-master` pour être en mode `NodePort`.
  - Créez un fichier `config_jenkins.yaml` avec à l'intérieur:

```yaml
service:
    master:
        type: "NodePort"
```
- Appliquez cette config à notre release avec `helm upgrade -f config_jenkins.yaml jenkins-tp codecentric/jenkins`.
- Cherchez le port d'exposition du service avec `kc get services | grep jenkins`
- Visitez [http://localhost:<node_port>](http://localhost:<node_port>)
- Récupérez le password d'inititalisation précédemment sauvegardé et collez-le dans le navigateur
- Notre Jenkins est prêt.

- Explorez les différents objets k8s créés par Helm avec Lens. -->