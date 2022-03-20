
## Terraform caveats



- erreurs à la création/suppression
    - pour éviter les api unreachable bien mettre les depends_on entre resources et modules 
    - pour éviter timeout bien activer les wait for resource ready qui peuvent être désactivés pour certaines resources (exp scaleway k8s pool) voire ajouter une section timeouts custom si dispo pour la resource.

- ne pas comprendre les dépendances
    -> solution utiliser rover ou faire un dession à la main des resources et de leurs dépendances

- ne pas anticiper les conséquences des choix à la création
    -> solution : faire des labs copies de l'infra pour tester les choses à l'avance en conditions quasi réelles

- trop de resources à comprendre
    -> refaire l'architecture pour avoir un découpage en module plus petits et consistants

- resources existentes
    -> faire un import d'une ressource