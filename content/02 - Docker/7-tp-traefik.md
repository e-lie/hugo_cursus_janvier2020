---
title: TP 7 (bonus) - Docker et les reverse proxies
draft: false
weight: 75
---

Traefik est très bien intégré à Docker, ainsi qu'à Docker Swarm.
Il fonctionne en attribuant un sous-domaine d'un nom de domaine à un service fourni par un ou plusieurs conteneurs Docker grâce aux `labels` de Docker.

1. A l'aide de la [documentation de Traefik](https://doc.traefik.io/traefik/getting-started/quick-start/), ajouter à un fichier `docker-compose.yml` de votre choix la configuration nécessaire pour permettre à Traefik de rediriger le trafic wev vers un certain container.
2. Explorez le dashboard Traefik et vérifiez que la redirection est bien effective.
3. Avec `docker run`, lancez un conteneur avec les options de `label` permettant à Traefik de router du trafic depuis une adresse web.
<!-- 2. Faites de même pour un cluster Docker Swarm -->