---
title: TP Bonus - Intégration continue avec Gitlab
draft: false
weight: 120
---

1. Si vous n'en avez pas déjà un, créez un compte Gitlab : <https://gitlab.com/users/sign_in#register-pane>
2. Créez un nouveau projet et avec Git ou le Web IDE Gitlab poussez-y l'app de votre choix (par exemple `microblog` ou l'app `healthcheck` vue au TP2), ou bien forkez une app existante depuis l'interface Gitlab.
3. Avec la documentation, créez un fichier .gitlab-ci.yml basique (vous pouvez le faire depuis l'interface web de Gitlab) qui fera un test quelconque sur votre app (par exemple vérifier les erreurs de syntaxe en Python). Faites un test de votre CI avec deux commits (un d'erreur, un sans erreur) (il faut vérifier sur le portail de Gitlab comment s'est exécutée la pipeline).
4. Ajoutez un Dockerfile à votre repo et ajoutez au fichier `.gitlab-ci.yml` une étape qui fera un build du Dockerfile. Faites un test sur votre CI.
5. Toujours avec la documentation, faites une étape dans votre `.gitlab-ci.yml` qui push l'image ainsi buildée dans le registry de Gitlab (vous pouvez vous aider de bouts de code copiés depuis Internet). Faites un test sur votre CI avec deux commits.
6. (Avancé) Nous avons fait la partie CI (intégration continue). A l'aide de bouts de code trouvés sur Internet et d'une VM accessible depuis Internet, ajoutez une étape `deploy` à votre `.gitlab-ci.yml` pour ajouter le déploiement continu de l'app (CD) : si aucune étape précédente n'a échoué (comportement de Gitlab-CI par défaut), la nouvelle version de l'app devra être déployée sur votre serveur.
