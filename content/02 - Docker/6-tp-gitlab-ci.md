---
title: CI/CD avec Gitlab
draft: true
---

1. Créez un compte Gitlab
2. Créez un nouveau projet et avec Git mettez-y l'app de votre choix (par exemple `microblog`)
3. Avec la documentation, créez un fichier .gitlab-ci.yml basique qui fera un test quelconque sur votre app (par exemple vérifier les erreurs de syntaxe en Python). Faites un test de votre CI avec deux commits (un d'erreur, un sans erreur).
4. Ajoutez au fichier `.gitlab-ci.yml` une étape qui fera un build d'un Dockerfile de votre repo. Faites un test sur votre CI avec deux commits.
5. Toujours avec la documentation, faites une étape dans votre `.gitlab-ci.yml` qui push l'image ainsi buildée dans le registry de Gitlab. Faites un test sur votre CI avec deux commits.
