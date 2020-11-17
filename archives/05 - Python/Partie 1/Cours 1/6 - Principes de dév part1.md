---
title: 6. Principes de développement - Partie 1 
draft: false
weight: 20
---


## Écrire un programme ... pour qui ? pour quoi ?

Le fait qu'un programme marche n'est pas suffisant voire parfois "secondaire" !

- ... Mieux vaut un programme cassé mais lisible (donc débuggable)
- ... qu'un programme qui marche mais incompréhensible (donc fragile et/ou qu'on ne saura pas faire évoluer)
- ... et donc qui va surtout faire perdre du temps aux futurs développeurs

Autrement dit : **la lisibilité pour vous et vos collègues a énormément d'importance pour la maintenabilité et l'évolution d'un projet**


## Posture de développeur et bonnes pratiques

- Lorsqu'on écrit du code, la partie "tester" et "debugger" fait partie du job.

**On écrit pas un programme qui marche au premier essai**

- Il faut tester et débugger **au fur et à mesure**, **pas tout d'un seul coup** !

## Le debugging interactif : `pdb`, `ipdb`, VSCode

- PDB = Python DeBugger

- Permet (entre autre) de définir des "break points" pour rentrer en interactif
   - `import ipdb; ipdb.set_trace()`
   - en 3.7 : `breakpoint()` <small>Mais fait appel à `pdb` et non `ipdb` ?</small>
- Une fois en interactif, on peut inspecter les variables, tester des choses, ...
- On dispose aussi de commandes spéciales pour executer le code pas-à-pas
- Significativement plus efficace que de rajouter des `print()` un peu partout !


#### Commandes pdb et ipdb

- `l(ist)` : affiche les lignes de code autour de code (ou continue le listing precedent)
- `c(ontinue)` : continuer l'execution normalement (jusqu'au prochain breakpoint)
- `s(tep into)` : investiguer plus en détail la ligne en cours, possiblement en descendant dans les appels de fonction
- `n(ext)` : passer directement à la ligne suivante
- `w(here)` : print the stack trace, c.a.d. les différents sous-appels de fonction dans lesquels on se trouve
- `u(p)` : remonte d'un cran dans les appels de la stacktrace
- `d(own)` : redescend d'un cran dans les appels de la stacktrace

- `pp <variable>` : pretty-print d'une variable (par ex. une liste, un dict, ..)

#### Debug VSCode 
- Dans VSCode on peut fixer des breakpoints (points rouges) directement dans le code en cliquant sur la colonne de gauche de l'éditeur.
- Il faut ensuite aller dans l'onglet debug et sélectionner une configuration de debug ou en créer une plus précise (https://code.visualstudio.com/docs/python/python-tutorial)
- Ensuite on lance le programme en mode debug et au moment de l'arrêt il est possible d'explorer les valeurs de toutes les variables du programme (Démo)


## Bonnes pratiques pour la lisibilité, maintenabilité

- **Keep It Simple**
- **Sémantique** : utiliser des noms de variables et de fonctions **qui ont du sens**
- **Architecture** : découper son programme en fonction qui chacune résolvent un sous-problème précis
- **Robustesse** : garder ses fonctions autant que possibles indépendantes, limiter les effets de bords
    - lorsque j'arose mes plantes, ça ne change pas la température du four

- Lorsque mon programme évolue, **je prends le temps de le refactoriser si nécessaire**
    - si je répète plusieurs fois les mémes opérations, il peut être intéressant d'introduire une nouvelle fonction
    - si le contenu d'une variable ou d'une fonction change, peut-être qu'il faut modifier son nom
    - si je fais pleins de petites opérations bizarre, peut-être qu'il faut créer une fonction

### Quelques programmes réels utilisant Python 

#### Dropbox

![](../../../../images/python/dropbox.png)

#### Atom

![](../../../../images/python/atom.png)

#### Eve online

![](../../../../images/python/eveonline.jpg)

#### Matplotlib

![](../../../../images/python/matplotlib.png)

#### Blender

![](../../../../images/python/blender.jpg)

#### OpenERP / Odoo

![](../../../../images/python/odoo.jpg)

#### Tartiflette

![](../../../../images/python/tartiflette.png)
