---
title: Securité
draft: true
weight: 2100
---



## Modèles de menace

- Deux modèles fondamentaux :
    - single tenant cluster :
        - la menace est qu'un attaquant peut se connecter au cluster de l'extérieur et gagner des privilèges
        - une application exposée en mode public peut être compromise
        => sécuriser l'accès à l'API
        => sécuriser la runtime de conteneurs
        => Ajouter une pod security admission (successeur des Pod Security Policies) pour éviter qu'un pod compromis puisse compromettre le cluster
        => eviter qu'un pod ait un service account trop permissif
    - multitenant cluster :
        - isoler/protéger les parties/tenants les unes des autres au sein du même cluster
        => RBAC strict
        => Network Policies Strict
        => ResourceQuota incontournables sur tous les namespaces

## RBAC

- scanner le RBAC de tout un cluster pour identifier les pods risqués avec KubiScan https://github.com/cyberark/KubiScan
=> c'est à dire les pods qui ont un Service Account avec plein de permissions

## Auditer les images

## Pod Security Admission

https://kubernetes.io/docs/concepts/security/pod-security-admission/

- a la phase admission de la création d'un pod
=> vérifie que le pod respecte une classe de PodSecurityStandard 
Privileged 	Unrestricted policy, providing the widest possible level of permissions. This policy allows for known privilege escalations.
Baseline 	Minimally restrictive policy which prevents known privilege escalations. Allows the default (minimally specified) Pod configuration.
Restricted 	Heavily restricted policy, following current Pod hardening best practices.

https://kubernetes.io/docs/concepts/security/pod-security-standards/

## NetworkPolicies