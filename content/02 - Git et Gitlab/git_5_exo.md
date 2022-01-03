---
title: Git 5 - Exercices
weight: 510
---

A l'aide des ressources suivantes, créez une pipeline (un fichier `.gitlab-ci.yml`) qui :
1. build votre app (conteneurisée avec Docker ou non)
2. (sans Docker) sauvegarde l'artefact dans Gitlab et/ou utilise le *Package Registry* de Gitlab
3. (avec Docker) pousse l'image créée dans le *Container Registry* de Gitlab 
4. Fait un test sur votre app
5. (optionnel) Grâce à un secret Gitlab, déploie l'app sur un serveur distant (via l'exécution de commandes SSH)

## Ressources

### Documentation

- **Get started with GitLab CI/CD : <https://docs.gitlab.com/ee/ci/quick_start/>**
- Documentation de référence de `.gitlab-ci.yml` : <https://docs.gitlab.com/ee/ci/yaml/>

### Tutoriels
- [TP Docker : Gitlab CI](../../04-docker/6-tp-gitlab-ci/)

Code Refinery :
- <https://coderefinery.github.io/testing/continuous-integration/>
- <https://coderefinery.github.io/testing/full-cycle-ci/>

Cloud Consultancy Team :
- <https://tsi-ccdoc.readthedocs.io/en/master/ResOps/2019/Gitlab.html>