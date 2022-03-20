


## Remarques

- un module est une meta ressource qui peur avoir un count depends_on et for_each

- Il faut bien ajouter les dépendances non evidentes avec depends_on si on veut que les modules se suppriment/manipulent correctement (sinon par exemple le cluster k8s est supprimé mais pas les chart et terraform croit que la suppression a échoué car il a pas eu la confirmation)

- La création de depends_on pour les modules implique que les modules sont configurés au niveau du module parent

## Checklist d'archi terraform

- déclarer les dépendances de chaque module dans un fichier versions.tf
- déclarer/rappeler les dépendances au niveau d'un fichier versions.tf du module parent (les versions doivent être compatibles)
- configurer les providers au niveau du main du module parent (obligatoire pour les modules avec depends_on ou count)
- pas la peine de mettre des blocs provider vide dans les main des modules -> provoque un warning