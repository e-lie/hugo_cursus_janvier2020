---
title: L'architecture MVC
weight: 5
draft: false
---

### L'architecture MVC
![](../../../images/mvc2.png)


  - Modèle = les données et la façon dont elles sont structurées...
  - Vue = affichage, mise en forme des données
  - Controleur = la logique qui gère la requête de l'utilisateur, va chercher les données qu'il faut, et les donne à manger à la vue

![](../../../images/mvc3.png)

Pour résumer:
  - D'abord un utilisateur envoie une requête pour voir une page en entrant une URL
  - Cette requête est reçue par le Controleur
  - Le Controleur utilise le modèle pour trouver toutes les données dont il a besoin
  - Puis envoie les données à la Vue qui rend une page web 
