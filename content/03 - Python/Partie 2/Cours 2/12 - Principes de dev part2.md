---
title: 12. Principes de développement - Partie 2
draft: false
weight: 23
---

### Documentation

Pour les librairies (et Python en général) :
- `docs.python.org`
- `devdocs.io`
- stack overflow ...
- doc strings !!

Pour votre code :
- nom de variables, fonctions, argument !!!
- commentaires, doc strings
- gestionnaire de version
- generation de doc automatique ?

### Faire du "bon code"

**La lisibilité est la priorité numéro 1**

Un programme est vivant et évolue. Mieux vaut un programme cassé mais lisible (donc débuggable) qu'un programme qui marche mais incompréhensible (donc fragile et/ou qu'on ne saura pas faire évoluer)

(c.f. Guido van Rossum chez Dropbox)

Autrement dit : **la lisibilité pour vous et vos collègues a énormément d'importance pour la maintenabilité et l'évolution du projet**



- **Keep It Simple**
- **Sémantique** : utiliser des noms de variables et de fonctions concis et pertinents
- **Commentaires** : *lorsque c'est nécessaire*, pour démystifier ce qu'il se passe
- **Modularité** : découper son programme en fonctions qui chacune résolvent un sous-problème
- **Couplage faible** : garder ses fonctions autant que possibles indépendantes, limiter les effets de bords
- **Prendre le temps de refactoriser** quand nécessaire
    - si je répète plusieurs fois les mémes opérations, peut-être définir une nouvelle fonction
    - si le contenu d'une variable ou d'une fonction change, peut-être changer son nom
- **Ne pas abuser** des principes précédents
    - trop d'abstractions tue l'abstraction
    - tout ça viens avec le temps et l'expérience



[How to write good code](https://xkcd.lapin.org/strips/844Code%20correct.png)


### Conventions de nommages des variables, fonctions et classes

Variables et fonctions en snake case : `nom_de_ma_variable`

Constantes globales en macro case: `NOM_DE_MA_CONSTANTE`

Nom de classes en upper camel case : `NomDeMaClasse`


### Syntaxe, PEP8, linters

- Le style d'écriture de python est standardisé via la norme PEP8
- Il existe des "linter" pour détecter le non-respect des conventions (et également certaines erreurs logiques)
    - Par exemple `flake8`, `pylint`
- Intégration possible dans `vim` et autres IDE...
- `autopep8` ou `black` permettent de corriger un bon nombre de problème automatiquement

### Ex.12 Outils pour développer

12.1 - Utiliser `pip3` pour trouver quelle est le numéro de version du package `requests` installé

12.2 - Rechercher avec `pip3` si les paquets `flake8` et `autopep8` existent. Installez-les.

12.3 - Utilisez `flake8` sur un code que vous avez écrit récemment (disons d'au moins 30 ou 40 lignes !). Étudiez les erreurs et warnings rapportées par flake, et essayer les corriger manuellement. Si certains warnings vous semblent trop aggressif, utiliser `--ignore` pour spécifier des codes d'erreurs à ignorer.

12.4.1 - Sur un autre code relativement mal formatté, utiliser `autopep8` pour tenter d'ajuster automatiquement le formattage du code. Sauvegarder la sortie fournie par `autopep8` dans un autre fichier "version 2" et comparer le fichier initial avec le fichier de sortie à l'aide de `diff` ou de `git diff --no-index file1 file2`.

12.4.2 - Le nouveau fichier est-il exempt de problèmes d'après flake8 ?
