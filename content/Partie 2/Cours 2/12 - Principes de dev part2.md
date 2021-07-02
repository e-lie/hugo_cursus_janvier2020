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
