---
title: "Cours 1 - Presentation Reveal"
outputs: ["Reveal"]
---

## Présentation d'Ansible

### Ansible

Ansible est un **gestionnaire de configuration** et un **outil de déploiement et d'orchestration** très populaire et central dans le monde de l'**infrastructure as code** (IaC).

Il fait donc également partie de façon centrale du mouvement **DevOps** car il s'apparente à un véritable **couteau suisse** de l'automatisation des infrastructures.

---
### histoire

Ansible a été créé en **2012** (plus récent que ses concurrents Puppet et Chef) autour d'une recherche de **simplicité** et du principe de configuration **agentless**.

Très orienté linux/opensource et versatile il obtient rapidement un franc succès et s'avère être un couteau suisse très adapté à l'automatisation DevOps et Cloud dans des environnements hétérogènes.

Red Hat rachète Ansible en 2015 et développe un certain nombre de produits autour (Ansible Tower, Ansible container avec Openshift). 

---

### Architecture : simplicité et portabilité avec ssh et python

![](../../images/devops/ansible_overview.jpg)

{{% notice note %}}
Ansible est **agentless** c'est à dire qu'il ne nécessite aucun service/daemon spécifique sur les machines à configurer.
{{% /notice %}}

---

La simplicité d'Ansible provient également du fait qu'il s'appuie sur des technologies linux omniprésentes et devenues universelles.

- **ssh** : connexion et authentification classique avec les comptes présents sur les machines.
- **python** : multiplateforme, un classique sous linux, adapté à l'admin sys et à tous les usages.
<!-- - (**git** : ansible c'est du code donc on versionne dans des projets gits. principalement dans le ) -->

De fait Ansible fonctionne efficacement sur toutes les distributions linux, debian, centos, ubuntu en particulier (et maintenant également sur Windows).

---
### Ansible pour la configuration

Ansible est **semi-déclaratif** c'est à dire qu'il s'exécute **séquentiellement** mais idéalement de façon **idempotente**.

Il permet d'avoir un état descriptif de la configuration:

- qui soit **auditable**
- qui peut **évoluer progressivement**
- qui permet d'**éviter** que celle-ci ne **dérive** vers un état inconnu

---