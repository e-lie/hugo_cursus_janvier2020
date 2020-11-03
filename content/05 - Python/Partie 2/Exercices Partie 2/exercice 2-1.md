---
title: Exercice 2.1 - Fichiers, JSON et dictionnaires
weight: 5
draft: false
---

## 2.1 - Fichiers, JSON et dictionnaires

- Écrire une fonction qui prends un nom de fichier en argument et retourne le contenu si elle a été capable de le récupérer. Sinon, elle doit déclencher une exception qui explique en français pourquoi elle n'a pas pu.

- Écrire une fonction qui remplace un mot par un autre dans un fichier. On pourra pour cela se servir de `une_chaine.replace("mot", "nouveau_mot")` qui renvoie une version modifiée de `une_chaine` en ayant remplacé "mot" par "nouveau mot".

- Télécharger le fichier `https://app.yunohost.org/community.json` (avec votre navigateur ou `wget` par exemple). Écrire une fonction qui lit ce fichier, le charge en tant que données json et renvoie un dictionnaire Python. Écrire une autre fonction capable de filtrer le dictionnaire pour ne garder que les apps d'un level supérieur ou égal à un level `n` donné en argument. Essayez votre fonction avec le niveau 8.

- Améliorez le programme précédent pour récupérer la liste directement depuis le programme avec `requests`. Gérer les différentes exceptions qui pourraient se produire (afficher un message en français) : syntaxe json incorrecte, erreur 404, time-out du serveur, erreur SSL