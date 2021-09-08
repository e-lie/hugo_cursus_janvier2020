---
title: Cours 1 - Intégration continue et testing logiciel
draft: false
---

![](../../images/jenkins/unknown.png)
### Comment intégrer et réutiliser des composants logiciels ?

- Pour cela on sépare le code entre parties spécifiques à l'application et parties génériques.
- Les parties génériques sont ce qu'on appelle des librairies (par exemple une librairie pour gérer le cache de donnée d'une application web)
- Elles exposent des interfaces bien définies pour la compatibilité (les composants logiciel doivent pouvoir se faire confiance car ils dépendent les uns des autres)
- *Problème*: comme faire lorsque vous avez codé un composant logiciel / une vaste librairie pour garantir que vous maintenez la compatibilité ?

### La base : gérer finement les versions du code.

- On veut que chaque version du code soit identifiable par un numéro pour savoir exactement de quel **artefact logiciel** on parle.
- Le cerveau humain ne peut pas le faire seul (impossible de vérifier manuellement des centaines de fichiers pour chercher si il y a des modifications)
- C'est pourquoi on utilise un outil qu'on appelle **gestionnaire de version**. Le principal est bien sur **git**.
- Une installatino de CI/CD s'intègre autour d'une **forge logicielle** : une plateforme qui gère des dépôts versionnés de code et encadre un workflow de développement (Cf cours sur git) que doivent suivre les développeurs.

### Intégrer des composants logiciels ... de façon fiable  et sécurisée

- Le problème du code est qu'il doit évoluer.
- Mais la moindre modification dans la réponse d'une fonction peut créer un bug dans un programme utilisant la librairie.
- Si votre librairie est très utilisée l'impact peut être énorme (et ça arrive d'introduire un bug qui impacte des milliers de personnes Cf les issues github des gros projets)
- Il faut pouvoir contrôler précisément le code développé par une équipe et avoir une façon fiable de vérifier que l'interface (le "contrat" d'usage du composant) est stable.
- Le cerveau humain est mal adapté à la vérification systématique de l'interface d'un composant logiciel ou pire des chemins d'exécution d'un programme.

## Pourquoi les tests automatiques ?

- Pour automatiser la vérification d'un programme et surtout des libraires pour garantir que la réponse de leurs fonctions est bien ce qu'elle doit être (que le contrat de leur interface est respecté) on ajoute des tests automatiques.

- Les tests sont conçus pour utiliser un ou plusieurs composants logiciels toujours de la même façon et vérifier qu'ils se comportent comme d'habitude.

- Il y a plusieurs types de tests:
  - unitaire (composant par composant, fonction par fonction)
  - d'intégration (plusieurs composants ensembles)
  - fonctionnel (test extérieur qui valide le fonctionnement concret du logiciel)

- Une installation de CI **intègre** des **tests systématiques dès que le code change**.
## Test unitaires

le premier type de test applicatif classique est le **test unitaire**:

- il s'agit de **tester chaque fonction** interne du programme pour s'assurer que le changement d'une fonction ne va pas "casser" un autre endroit du programme.

- ce type de test cherche a garantir la maintenabilité du programme en permettant de repérer les incohérences interne qui apparaissent entre les parties

- les tests unitaires sont écris et utilisés quotidiennement par les développeur.e.s

- un jeu de tests unitaire contient très vite des milliers de petits tests et doit pouvoir s'executer le plus rapidement possible (moins de 15-20 secondes idéalement). il faut que les développeur.e.s puissent et veuille le lancer toutes les 10 min pour valider leurs modifications.

- ils sont donc importants **indépendamment du devops** pour valider la **cohérence "logique"** du programme.

- les tests unitaires ne valident pas le fonctionnement du programme juste la stabilités de ses fonctions internes.


le framework de test le plus populaire en `python` se nomme `pytest` : [documentation de pytest](https://docs.pytest.org/)
### Le `test driven development` avec des tests unitaires

Un mode de développement fortement conseillé qui implique d'écrire/modifier un test unitaire d'abord pour chaque fonction avant le code de la fonction:

- pour caractériser le comportement d'une fonction précisément et donc l'avoir en tête avant de commencer à coder.
- pour être sur que chaque fonction est raisonnablement tester plutôt que de compter sur la motivation après coup pour le faire.

## Tests d'intégration: tester si les différents composants de l'application sont bien intégrés

Les tests d'intégration sont des tests sur les fonctions du programme qui valident si les différents composants/fonctions d'une application fonctionnent toujours bien ensembles.

- Comme pour tests unitaires il s'agit de tests pour aider les développeurs dans leur travail de chasse aux bugs.
- Ils testent l'apparition de bugs impliquant plusieurs composants en interaction.
- Ils nécessitent souvent (pas toujours) de lancer l'application complètement.
- Les tests d'intégration sont plus lents généralement que les tests unitaires et on les lance moins souvent, généralement comme nous le verrons avec la CI.
- Plutôt que des fonction individuelles voire isolées grâce à du "mocking" ils testent des appels de fonctions impliquant des chaînes de composants/autre fonctions.
- Ils ne testent pas le bon déploiement de l'application mais sont fonctionnement interne "intégré"

## Tests fonctionnels: vérifier que l'application fonctionne d'un point de vue extérieur

Les fonctionnels sont des tests pour vérifier le bon fonctionnement d'une application en train de tourner.

- Ils sont lancés sur une instance de l'application déployée
- Ils sont lancés de l'extérieur un peu comme une interaction utilisateur
- Ils permettent de valider le bon déploiement
- Les tests fonctionnels sont donc typiques du **déploiement continu** et plus proche du DevOps (même si les autre tests sont souvent aussi la responsabilite du DevOps)
- Il sont notamment utilisés (avec d'autres critères) pour valider l'ensemble de l'application et du déploiement.

Exemple : si nous disposons d'une application web basique, une façon simple de vérifier son fonctionnement de l'extérieur est ainsi de lui envoyer des requêtes HTTP et de contrôler ses réponses.

Il existe aussi des framework de tests qui simulent des clics et interractions avec l'interface utilisateur.

Pour un backend, un test fonctionnel est par exemple un ensemble d'appel à l'API dans le protocole adapté.

### Qualité logicielle

Malgré les tests il est courant que du code vite fait et mal fait s'accumule au fil des années dans une entreprise. C'est ce qu'on appelle couramment la **dette technique**.

- Pour permettre de diagnostiquer et de résorber cette dette on utilise des outils de mesure de la qualité du code. Ex: **Sonarcube**.
- Ces outils intégrés dans la CI peuvent même interdire à un développeur de proposer du code ne remplissant pas certains critères.

### Sécurité

Il est également courant pour des développeurs en particulier s'ils ne sont pas formés en sécurité informatique d'**introduire certains bugs** dans un logiciel qui peuvent consituter **des failles** (Par exemple des comportements mémoire inadéquats qui permettent d'exploiter l'application)

- La revue automatique du code va également permettre de détecter une partie de ces failles grâce à une **analyse de sécurité statique**:
  - teste au maximum tous les chemins d'exécution du programme pour trouver ceux menant à des crash
  - examine l'usage de la mémoire et sa sécurisation pour mettre en valeur les possibilité d'injection.

- Ces tests n'éliminent pas les failles de sécurités humaines et dynamiques.


## TP1 - écrire des tests unitaires, d'intégration et fonctionnels pour une application flask