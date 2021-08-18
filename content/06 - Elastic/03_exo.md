---
title: "3 - Exercices"
draft: false
weight: 3031
---

geoip
https://logz.io/blog/kibana-tutorial/

https://logz.io/blog/docker-stats-monitoring-dockbeat/

https://logz.io/blog/docker-logging-elk-stack-part-two/

super dashboards

YT KIBANA :
https://www.youtube.com/watch?v=6bM5SPVIuDs

Lancer un scanner web pour faire clignoter le dashboard ? Gerne nikto

Recherche dans Kibana

- Tous les avions en provenance de New York qui ont eu du retard ?

<!-- -->

- La quantité d\'avion ayant eu du retard hier soir entre 21h30 et 22h
  ?

<!-- -->

- Le prix moyen des billets par companie avec une visualisation

<!-- -->

- Aller dans la section dashboard. Explorer les différentes
  visualisations pour comprendre de quoi elles parlent

  - Utilisez la section contrôle pour ajouter des filtres

---

Recherche dans Kibana

- Tous les avions en provenance de New York qui ont eu du retard ?

OriginCityName:\"New York\" AND FlightDelay:true

(trois vols seulement en 1 mois) -- on peut observer 3 pics sur le
graphique

- La quantité d\'avion ayant eu du retard hier soir entre 21h30 et 22h
  ?

FlightDelay:true + changer la période en mode absolu en haut à droite

ou

FlightDelay:true + Add a filter.

- Le prix moyen des billets par companie avec une visualisation

cliquer sur AvgTicketPrice dans la liste des propriétés \> visualize

Axe Y  cliquez sur la flèche bleue :

- Aller dans la section dashboard. Explorer les différentes
  visualisation

  - Utilisez la section contrôle pour ajouter des filtres
