---
title: 'TP3 - Déployer Jenkins avec Helm'
draft: true
---

Helm est un gestionnaire de paquet K8s qui permet d'installer des paquets sans faire des copier-coller pénibles de yaml :

- pas de duplication de code
- des déploiement avancés avec un processus de mise à jour k8s intégré

Helm ne dispense pas de maîtriser l'administration de son cluster.

### Ajoutons un nouvel alias

- Pour installer Helm sur ubuntu, utilisez : `snap install helm --classic`

- Suivez le [Quickstart] (https://helm.sh/docs/intro/quickstart/) 


- Cherchez Jenkins sur [https://hub.kubeapps.com](https://hub.kubeapps.com).
- Prenez la version de **codecentric** et ajoutez le dépot avec la première commande à droite (ajouter le dépot et déployer une release).
- Installer une release jenkins-tp de cette application (ce chart) avec `helm install --template-name jenkins-tp codecentric/jenkins`
- Cherchez le nom du pod jenkins-master
- Affichez les logs du pod avec `kc logs` et récupérez la clé d'initialisation qui se trouve entre les triples lignes d'étoiles. Notez là dans un fichier texte (`gedit key.tmp` ?)
- Plutôt que de faire un port-forwarding, nous allons configurer le service k8s de jenkins-master pour être en mode NodePort.
  - Créez un fichier `config_jenkins.yaml` avec à l'intérieur:

```yaml
service:
    master:
        type: "NodePort"
```
- Appliquez cette config à notre release avec `helm upgrade -f config_jenkins.yaml jenkins-tp codecentric/jenkins`.
- Cherchez le port d'exposition du service avec `kc get services | grep jenkins`
- Visitez [http://localhost:<node_port>](http://localhost:<node_port>)
- Récupérez le password d'inititalisation précédemment sauvegardé et collez le dans le navigateur
- Notre Jenkins de travail est prêt.

