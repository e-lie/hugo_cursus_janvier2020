---
title: Exercice 2.3 - Lecture itérative avec la library externe lxml
draft: false
weight: 20
---

- Installez `lxml` grâce à `pip3`, et récupérez le "gros" fichier XML, `copyright.xml` à l'adresse `https://dl.google.com/rights/books/renewals/google-renewals-20080516.zip`. Attention à ne pas tenter d'ouvrir "brutalement" ce fichier avec un éditeur ou avec la méthode utilisée en 1 : cela consommera beaucoup trop de RAM !

- En utilisant des commandes comme `head -n 50 copyright.xml`, analyser visuellement la structure du fichier d'après ses premières lignes.

- Initialiser un itérateur destiné à itérer sur ce fichier, et en particulier sur les tags `Title`. Créer une boucle à partir de cet itérateur et afficher tous les titres qui contiennent la chaîne `"Pyth"`. **On prendra soin de nettoyer les éléments trouvés avant de passer à chaque nouvelle itération sous peine de remplir la RAM très vite !**

- Pour chaque titre trouvé, remonter au parent 'Record' pour trouver le 'Holder Name' correspondant à ce titre. S'aider du debug VSCode, `ipython` et/ou `ipdb` pour tester et expérimenter en interactif.