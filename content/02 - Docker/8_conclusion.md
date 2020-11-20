---
title: Conclusion
weight: 80
---

<!-- # Docker en production -->

---

## Retours

- Comment ça s'est passé ?
  - Difficulté : trop facile ? trop dur ? quoi en particulier ?
  - Vitesse : trop rapide ? trop lent ? lors de quoi en particulier ?
  - Attentes sur le contenu ? Les manipulations ?
  - Questions restées ouvertes ? Nouvelles questions ?

## Bonnes pratiques et outils

### Sécurité / durcissement

- **un conteneur privilégié est _root_ sur la machine !**
- des _cgroups_ correct : `ulimit -a`
- exemple de durcissement conseillé : [modifier la config par défaut des user namespaces](https://medium.com/@mccode/processes-in-containers-should-not-run-as-root-2feae3f0df3b)

<!-- Exemple de renforcement :
```bash
vim /etc/docker/daemon.json
adduser docker-userns -s /bin/false
service docker restart
cat /etc/subuid
cat /etc/passwd
docker run -d -it alpine sh
docker ps
htop
``` -->

- le benchmark Docker : <https://github.com/docker/docker-bench-security/>

- [Clair](https://github.com/quay/clair) : l'analyse statique d'images Docker

## Aller plus loin

- Le livre _Mastering Docker_, de Russ McKendrick et Scott Gallagher

### Gérer les logs des conteneurs

Avec Elasticsearch, Filebeat et Kibana… grâce aux labels sur les conteneurs Docker

### Gérer le reverse proxy

Avec Traefik, aussi grâce aux labels sur les conteneurs Docker

### Configurer le réseau de façon plus complexe avec des plugins réseau

- Réseaux "overlay": IP in IP, VXLAN…
- …mais on a rapidement besoin de plugins exclusifs à Kubernetes : [Calico](https://github.com/projectcalico/calico), [Flannel](https://github.com/coreos/flannel/), Canal (Calico + Flannel), [Cilium](https://github.com/cilium/cilium) (qui utilise eBPF)

### Configurer de la CI/CD

- La nature facile à déployer des conteneurs et l'intégration du principe d'Infrastructure-as-Code les rend indispensable dans de la CI/CD (intégration continue et déploiement continu).
- Les principaux outils de CI sont Gitlab, Jenkins, Github Actions, Travis CI…
  - Gitlab propose par défaut des runners préconfigurés qui utilisent des conteneurs Docker et tournent en général dans un cluster Kubernetes.
  - Gitlab propose aussi un registry d'images Docker, privé ou public, par projet.
- Les tests à l'intérieur des conteneurs peuvent aussi être faits de façon plus poussée, avec par exemple Ansible comme source de healthcheck ou comme suite pour les tests.
- Dans une autre catégorie, Gitpod base son workflow sur des images Docker permettant de configurer un environnement de développement

<!--

# Monitorer des conteneurs

Avec Portainer

--- -->

<!--
# Tests sur des conteneurs

Ansible comme source de healthcheck

--- -->

<!-- # Exemples de cas pratiques :

Présentation d'un workflow Docker, du développement à la production -->
