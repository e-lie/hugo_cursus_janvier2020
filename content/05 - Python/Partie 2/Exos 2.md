---
title: Exos 2 - Collections, Fichiers et XML

draft: false
weight: 20
---

## 2.1 - Fichiers, JSON et dictionnaires

- Écrire une fonction qui prends un nom de fichier en argument et retourne le contenu si elle a été capable de le récupérer. Sinon, elle doit déclencher une exception qui explique en français pourquoi elle n'a pas pu.

- Écrire une fonction qui remplace un mot par un autre dans un fichier. On pourra pour cela se servir de `une_chaine.replace("mot", "nouveau_mot")` qui renvoie une version modifiée de `une_chaine` en ayant remplacé "mot" par "nouveau mot".

- Télécharger le fichier `https://app.yunohost.org/community.json` (avec votre navigateur ou `wget` par exemple). Écrire une fonction qui lit ce fichier, le charge en tant que données json. Écrire une autre fonction capable de filtrer le dictionnaire pour ne garder que les apps d'un level `n` donné en argument. Écrire une fonction similaire pour le status (`working`, `inprogress`, `broken`).

- Améliorez le programme précédent pour récupérer la liste directement depuis le programme avec `requests`. Gérer les différentes exceptions qui pourraient se produire (afficher un message en français) : syntaxe json incorrecte, erreur 404, time-out du serveur, erreur SSL

## 2.2 - Utilisation de la librairie XML intégrée ElementTree

- En utilisant la module ElementTree de Python, charger le fichier `country.xml` fourni par le formateur. Boucler sur les différents éléments `country` et afficher pour chaque élément la valeur du `gdppc` et le nom des voisins.

- Ajouter un element `country` pour la France et l'Espagne en suivant la même structure.

- Sauvegarder la version modifiée en `country_extended.xml`

## 2.3 - Lecture itérative avec la library externe lxml

- Installez `lxml` grâce à `pip3`, et récupérez le "gros" fichier XML, `copyright.xml`. Attention à ne pas tenter d'ouvrir "brutalement" ce fichier avec un éditeur ou avec la méthode utilisée en 1 : cela consommera beaucoup trop de RAM !

- En utilisant des commandes comme `head -n 50 copyright.xml`, analyser visuellement la structure du fichier d'après ses premières lignes.

- Initialiser un itérateur destiné à itérer sur ce fichier, et en particulier sur les tags `Title`. Créer une boucle à partir de cet itérateur et afficher tous les titres qui contiennent la chaîne `"Pyth"`. **On prendra soin de nettoyer les éléments trouvés avant de passer à chaque nouvelle itération sous peine de remplir la RAM très vite !**

- Pour chaque titre trouvé, remonter au parent 'Record' pour trouver le 'Holder Name' correspondant à ce titre. S'aider de `ipython` et/ou `ipdb` pour tester et expérimenter en interactif.