---
title: "3 - Kibana - Exercices"
draft: false
weight: 3031
---

## Exercices

Rappel : Kibana est une interface pour Elastic (soit on attaque direct Elastic soit on utilise les trucs pratiques de Kibana)

- _utiliser les données d'aviation_
- explorons l'interface
- explorons les graphiques

<!--
geoip
https://logz.io/blog/kibana-tutorial/

https://logz.io/blog/docker-stats-monitoring-dockbeat/

https://logz.io/blog/docker-logging-elk-stack-part-two/

super dashboards

YT KIBANA :
https://www.youtube.com/watch?v=6bM5SPVIuDs

Lancer un scanner web pour faire clignoter le dashboard ? Gerne nikto -->

### Recherche dans Kibana

- Tous les avions en provenance de New York qui ont eu du retard ?

{{% expand "Solution :" %}}

`OriginCityName:"New York" AND FlightDelay:true`

(trois vols seulement en 1 mois) -- on peut observer 3 pics sur le
graphique
{{% /expand %}}

- La quantité d'avions ayant eu du retard hier soir entre 21h30 et 22h
  ?

{{% expand "Solution :" %}}

`FlightDelay:true` + changer la période en mode absolu en haut à droite

ou

`FlightDelay:true` + Add a filter.

{{% /expand %}}

- Le prix moyen des billets par compagnie avec une visualisation
  {{% expand "Solution :" %}}

Cliquer sur `AvgTicketPrice` dans la liste des propriétés \> visualize.

Axe Y : cliquez sur la flèche bleue.
{{% /expand %}}

- Aller dans la section dashboard. Explorer les différentes
  visualisations pour comprendre de quoi elles parlent

  - Utilisez la section contrôle pour ajouter des filtres

---

### Exercices supplémentaires

<!-- idées d'exercices: FIXME: Are they realistic? -->

- requête pour analyser une erreur dans le code
- graphique sur le volume de connexion au cours de la journée
- (difficile) trouver les correspondances possibles pour aller d'une ville A à une ville B entre telle et telle heure ?
    <!-- - corréler des évènements comme l'exemple Nginx du début -->
    <!-- - ajouter des exemples de plus en plus compliqués -->

> > >
