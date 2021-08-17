TODO:


Questions:

- IV) : 
    - Présenter trois types d'aggrégations:
        bucket, metric (une dimension de vos données), pipeline (composée)
        exemple vous voulez calculer le prix moyen par companies
        aggréger les avions par companies, combiner à avec une aggrégation avg
        faire un schéma au tableau en plusieurs étapes pour bien expliquer
    - Trouver Utiliser cet exemple dans Kibana pour faire un graphique.
        
    
- V) Life in a cluster:
    - Remettre dans le slide la syntaxe pour mettre à jour les shards d'un cluster
    - Ajouter supprimer un noeud ? réindexer

    - Parler des nodes et des shards
        - un slides sur les nodes
        - un sur les Shards
            Des bouts de tableaux d'index.
            (Toute une logistique de copies des informations pour que ce soit facile d'accès
            et rapide à mettre à jour).
        - Haute Dispo
            - un service qui a 100% de disponibilité et qui est résiliant.
            - redondance
            - autonomie (healthcheck + election)
            - basculement dynamique (load balancing)
            - migrations et mises à jour progressives
        - Shard principal et replicat
        - La formule du green
        - En cas de chute de noeud
            - load balancing
            - Élection d'un nouveau master node
        
    - Tester ensemble:
        - calculer la répartition pour un cluster à 2 ou 3 noeuds
        - changer les propriétés de l'index


GET /_cluster/health

PUT /blogs/_settings
{
    "number_of_replicas" : 2
}

PUT /blogs
{
"settings" : {
"number_of_shards" : 3,
"number_of_replicas" : 1
}
}
        - vérifier le status du cluster
        - supprimer un noeuds
            le service est-il toujours
        