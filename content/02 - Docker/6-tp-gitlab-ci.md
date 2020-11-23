---
title: TP 6 (bonus) - Intégration continue avec Gitlab
draft: false
weight: 65
---

## Créer une pipeline de build d'image Docker avec les outils CI/CD Gitlab
1. Si vous n'en avez pas déjà un, créez un compte sur Gitlab.com : <https://gitlab.com/users/sign_in#register-pane>
2. Créez un nouveau projet et avec Git, le Web IDE Gitlab, ou bien en forkant une app existante depuis l'interface Gitlab, poussez-y l'app de votre choix (par exemple [`microblog`](https://github.com/Uptime-Formation/microblog/), [`dnmonster`](https://github.com/amouat/dnmonster/) ou l'app `healthcheck` vue au TP2).
3. Ajoutez un Dockerfile à votre repository ou vérifiez qu'il en existe bien un.
4. Créez un fichier `.gitlab-ci.yml` depuis l'interface web de Gitlab et choisissez "Docker" comme template. Observons-le ensemble attentivement.
5. Faites un commit de ce fichier.
6. Vérifiez votre CI : il faut vérifier sur le portail de Gitlab comment s'est exécutée la pipeline.
7. Vérifiez dans la section Container Registry que votre image a bien été push.

<!-- ### Déployer notre container
8.  (Avancé) Nous avons fait la partie CI (intégration continue). A l'aide de bouts de code trouvés sur Internet et d'une VM accessible depuis Internet, ajoutez une étape `deploy` à votre `.gitlab-ci.yml` pour ajouter le déploiement continu de l'app (CD) : si aucune étape précédente n'a échoué (comportement de Gitlab-CI par défaut), la nouvelle version de l'app devra être déployée sur votre serveur. Il faudra ajouter des variables secrètes à votre CI, cela se fait dans les options du projet Gitlab. -->

### Ressources
* La [section Quick Start de la documentation Gitlab-CI](https://docs.gitlab.com/ee/ci/quick_start/README.html)
* Vous pouvez trouver [des exemples dans la documentation Gitlab](https://docs.gitlab.com/ee/ci/examples/README.html).
* La [section dédiée à `docker build` de la documentation Gitlab](https://docs.gitlab.com/ee/ci/docker/using_docker_build.html)
* La [section de la documentation dédiée au Container Registry](https://docs.gitlab.com/ee/user/packages/container_registry/)
