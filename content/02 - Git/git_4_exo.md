---
title: Git 4 - Forges Git - Exercices
weight: 410
---

<!-- Le faire sur Github ET gitlab ? -->

# Partie 4 : Développer de façon collaborative avec la forge logicielle Gitlab

Dans ce TP vous allez travailler par binôme sur le tutoriel Flask de Miguel Grindberg : https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-ii-templates

### Créer un compte sur Framagit et pousser un projet

Framagit est une forge libre basée sur `Gitlab` (et qui respecte votre vie privée).

- Rendez-vous sur <https://framagit.org> pour créer un compte.
<!-- - FIXME: quel projet then? -->
- Utilisez ensuite un projet Git de votre choix à héberger.
- Rendez-vous dans le dossier du projet en terminal et suivez les instructions gitlab pour pousser votre dépôt existant

<!-- ### Reprise du tutoriel Flask -->

<!-- FIXME: rework, on fait microblog ou non ? si oui à partir de quand ? -->

<!-- Le tutoriel a des chapitres. Le but du TP consistera a travailler à deux sur un chapitre avec un.e qui code et l'autre qui relit le code, suit le tutoriel et conseille le/la codeur/codeuse. Ce principe est très proche d'une méthodologie de développement agile nommée XP (extreme programming): -->

<!-- FIXME: rework -->

## Workflow

- Pour chaque ajout le code sera :
  - Ajouté dans une nouvelle branche.
  - Poussé sur un projet Framagit partagé.
- Le code sera revu par la personne qui n'a pas codé grâce à une **merge request** puis sera fusionnée (merged) dans la branche `master`.
- La personne qui n'a pas codé récupère la dernière version du code grâce à `git pull`

### Merge

Les fusions de branche peuvent s'effectuer en local sur la machine ou sur la forge logicielle.
Prendre le TP microblog et localiser la branche qui ajoute une page "A propos". Faire un `merge` de cette branche avec `master`...

  <!-- FIXME: euh je l'ai pas marqué quelque part ça ? tp3 ? fusionner -->

- ... via Gitlab avec une Merge Request
- ... via Github avec une Pull Request

- Faites une merge request sur le dépôt de quelqu'un de votre groupe, ou bien sur le dépôt de ce cours : <https://github.com/Uptime-Formation/cours-git>
  <!-- - Les deux premiers chapitres seront à merger en local et les deux suivants sur framagit. -->

<!-- FIXME: ajout autre remote, changement URL d'origine et ajout de celle de grinberg -->

## Exercices sur Learning Git Branching

- Sur [Learn Git branching](https://learngitbranching.js.org/), cherchez la section "Remote" et lancez "Push & Pull -- dépôts gits distants !" (ou bien `level remote1`)
