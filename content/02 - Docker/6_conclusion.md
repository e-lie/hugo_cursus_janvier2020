---
title: Conteneurs Docker
---

# Conteneurs Docker

## _Modularisez et maîtrisez vos applications_

---

# Docker en production

---

# Bonnes pratiques et outils

## Sécurité / durcissement

- **un conteneur privilégié est _root_ sur la machine !**
- des _cgroups_ correct : `ulimit -a`
- les user namespaces :
  https://medium.com/@mccode/processes-in-containers-should-not-run-as-root-2feae3f0df3b

```bash
vim /etc/docker/daemon.json
adduser docker-userns -s /bin/false
service docker restart
cat /etc/subuid
cat /etc/passwd
docker run -d -it alpine sh
docker ps
htop
```

---

# Bonnes pratiques et outils

<!-- A enrichir ! -->

## Sécurité

- le benchmark Docker

  - https://github.com/docker/docker-bench-security/
  - Le livre _Mastering Docker_, de Russ McKendrick et Scott Gallagher

- Clair : l'analyse statique d'images Docker

---

# Gérer les logs des conteneurs

Avec Elasticsearch, Logstash, Filebeat et Kibana

---

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
