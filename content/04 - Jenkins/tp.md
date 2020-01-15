
TP1 - Installation

- Jenkins de base :

- Avec docker : chercher sur docker hub et pull
- docker pull jenkins/jenkins:latest
- docker run jenkins -p 8080:8080 --name jenkins
- explorer Jenkins
- installer des plugins
- problème de scalabilité (peu de job en parallèles)

- Jenkins scalable avec kubernetes et le plugin jenkins
- créer un Dockerfile
- from jenkins/jenkins:latest
- installer les plugins
- configurer le plugin kubernetes
- vérifier que nos jobs se lance en parallèle
- installer blueocean

TP2 - Pipeline de test d'une application python

reprendre les supports TP cybermaker


