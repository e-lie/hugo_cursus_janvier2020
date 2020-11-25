---
draft: true
---

## _Administration système et opérations_

---

# Admin sys = s'occuper des machines

## Installation d'un nouvelle machine

- ## Brancher la machine (électricité, réseau etc) : seulement si c'est une petite organisation qui veut de l'autonomie

- ## Installer le système d'exploitation

- ## Configurer le réseau

- ## Installer les programmes qui forment l'environnement

- ## Configurer les programmes pour fonctionner ensembles

- Configurer les utilisateurs et les droits d'accès

---

# Adminsys

## Maintenance des machines

- ## Faire les mises à jours des programmes pour la sécurité et les fonctionnalités

- ## Effectuer des sauvegardes régulières et les vérifier

- ## Vérifier l'état des disques et du matériel

- Etc.

---

# Adminsys

## Déployer du nouveau code

Souvent dans une entreprise on installe des machines pour faire tourner le **logiciel maison** qui est **développé en interne** et **vendu à des client**

Donc il faut **déployer des nouvelles versions du logiciel** régulièrement :

- ## Tester le code

- ## Fabriquer une nouvelle version du logiciel à partir du code

- ## Copier ce logiciel sur les machines (on peut pas utiliser apt simplement)

- Faire toutes les modifications nécessaires pour que l'environnement soit compatible

---

# Adminsys

Et comme tout ça est très compliqué et ben **on fait des erreurs** donc,
il faut aussi **réparer les catastrophes**

- ## Identifier la panne

- ## Récupérer les données à partir des sauvegardes

- ## Faire des _Rollback_ = rétablir la version précédente qui marchait

- Etc. Et tout ça de temps en temps la nuit :D et stressé

---

# Les opérations

Dans un DSI (département de service informatique) on organise ces activités en opérations:

- ## On a un planning d'opération avec les priorités du moment et les trucs moins urgents

- ## On prépare chaque opération au minimum quelques jours à l'avance.

- ## On suit un protocole pour pas oublier des étapes de l'opération (pas oublier de faire une sauvegarde avant par exemple)

---

# Opérations : la difficultée

La difficulté principale pour les Obs c'est qu'un système informatique est:

- ## Un système très complexe qu'il est quasi **impossible de complètement visualiser** dans sa tête.

- ## Les **évènements** qui se passe sur la machines sont **instantanés** et **invisibles**

- ## L'**état actuel** de la machine n'est **pas ou peu explicite** (combien d'utilisateur, machine pas connectée au réseau par exemple.)

- Les **interractions entre des problèmes** peu graves peuvent entrainer des erreurs critiques en cascades.

---

# Opérations = culture de la **prudence**

---

# Opérations = culture de la **prudence**

- ## On s'organise à l'avance.

- ## On vérifie plusieurs fois chaque chose.

- ## On ne fait pas confiance au code que nous donnent les développeur·euses.

- ## On suit des procédures pour limiter les risques.

- ## On surveille l'état du système (on parle de monitoring)

- Et on reçoit même des SMS la nuit si ya un problème :S

---

# Bilan

## Les opérations **traditionnelles**:

- ## Peuvent pas aller trop vite car il faut marcher sur des oeufs.

- ## Les Ops veulent pas déployer de nouvelles versions **trop souvent** car ça fait plein de boulot et ils prennent des risques (bugs / incompatilibités)

- Quand c'est **mal organisé** ou qu'on va **trop vite** il y a des **catastrophes** possibles

---
