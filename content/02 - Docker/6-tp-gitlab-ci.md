---
title: TP Bonus - Intégration continue avec Gitlab
draft: false
weight: 75
---

1. Si vous n'en avez pas déjà un, créez un compte Gitlab : <https://gitlab.com/users/sign_in#register-pane>
2. Créez un nouveau projet et avec Git ou le Web IDE Gitlab poussez-y l'app de votre choix (par exemple `microblog` ou l'app `healthcheck` vue au TP2), ou bien forkez une app existante depuis l'interface Gitlab.
3. Avec la [documentation Gitlab-CI](https://docs.gitlab.com/ee/ci/quick_start/README.html), créez un fichier .gitlab-ci.yml basique (vous pouvez le faire depuis l'interface web de Gitlab) qui fera un test quelconque sur votre app (par exemple vérifier du code Python avec `pylint`). Vous pouvez trouver [des exemples dans la documentation Gitlab](https://docs.gitlab.com/ee/ci/examples/README.html).
4. Faites un test de votre CI avec deux commits (un d'erreur, un sans erreur) (il faut vérifier sur le portail de Gitlab comment s'est exécutée la pipeline).
5. Ajoutez un Dockerfile à votre repository ou vérifiez qu'il en existe un.
6. A partir de la [section dédiée de la documentation Gitlab](https://docs.gitlab.com/ee/ci/docker/using_docker_build.html), ajoutez au fichier `.gitlab-ci.yml` une étape qui fera un build du Dockerfile.
7. Avec la [section de la documentation dédiée au Container Registry](https://docs.gitlab.com/ee/user/packages/container_registry/), ajoutez une étape dans votre `.gitlab-ci.yml` qui push l'image ainsi buildée dans le registry de Gitlab.

8. (Avancé) Nous avons fait la partie CI (intégration continue). A l'aide de bouts de code trouvés sur Internet et d'une VM accessible depuis Internet, ajoutez une étape `deploy` à votre `.gitlab-ci.yml` pour ajouter le déploiement continu de l'app (CD) : si aucune étape précédente n'a échoué (comportement de Gitlab-CI par défaut), la nouvelle version de l'app devra être déployée sur votre serveur.
