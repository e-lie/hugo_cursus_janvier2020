title: Conteneurs Docker
class: animation-fade
layout: true

<!-- This slide will serve as the base layout for all your slides -->
<!--
.bottom-bar[
  {{title}}
]
-->

---

class: impact

# {{title}}
## *Modularisez et maîtrisez vos applications*

---

class: impact

# Images et conteneurs

---

# Créer une image en utilisant un Dockerfile

- Jusqu'ici nous avons utilisé des images toutes prêtes principalement.
--

- Une des fonctionnalité principale de docker est de pouvoir facilement construire ses images à partir d'un simple fichier texte: **le Dockerfile**
--

- Le **Dockerfile** est un fichier procédural de construction qui permet décrire l'installation d'un logiciel en enchaînant des instructions Dockerfile (en MAJUSCULE)

- Exemple:
```Dockerfile
FROM debian:stretch
RUN apt-get update && apt-get install -y cowsay fortune
ENTRYPOINT["/usr/games/cowsay"]
```

---

## Instruction `FROM`

- L'image de base à partir de laquelle est construite l'image.

## Instruction `RUN`

- Permet de lancer une commande shell (installation, configuration).

## Instruction `ADD`

- Permet d'ajouter des fichier depuis le contexte de build à l'intérieur du conteneur.
- Généralement utilisé pour ajouter le code du logiciel en cours de développement et sa configuration au conteneur.

---

## Instruction CMD

- Généralement à la fin du `Dockerfile` : elle permet de préciser la commande par défaut lancée à la création d'une instance du conteneur avec `docker run`. on l'utilise avec une liste de paramètres
```Dockerfile
CMD ["echo 'Conteneur démarré'"]
```

## Instruction ENTRYPOINT

- Précise le programme de base avec lequel sera lancé la commande
  
```Dockerfile
ENTRYPOINT ["/usr/bin/python3"]
```

## Instruction ENV

- Une façon recommandée de configurer vos applications Docker est d'utiliser les variables d'environnement UNIX ce qui permet une configuration "à runtime". 

## Documentation

- Il existe de nombreuses autres instructions possible très clairement décrites dans la documentation officielle : [https://docs.docker.com/engine/reference/builder/](https://docs.docker.com/engine/reference/builder/)

---

# Lancer la construction

- La commande pour lancer la construction d'une image est:

```bash
docker build -t <tag:version> -f <chemin_du_dockerfile> <contexte_de_construction>
```
--

- Lors de la construction Docker télécharge l'image de base. On constate plusieurs téléchargement en parallèle. (démo)
--

- Il lance ensuite la séquence des instruction du Dockerfile.
--

- Observez l'historique de construction de l'image avec `docker image history <image>`

- Il lance ensuite la série d'instructions du Dockerfile et indique un hash pour chaque étape. Pourquoi ?

---

# Les layers et la mise en cache

- Docker construit les images comme une série de couches compactées.
--

- On parle d'**Union Filesystem** car chaque couche (les fichiers) écrase la précédente
--

- Chaque couche correspond à une instruction du Dockerfile.
--

- `docker image history <conteneur>` permet d'afficher les layers, leur date de construction et taille respectives.
--

- Ce principe est au coeur de l'**immutabilité** des images docker.
--

- Au lancement d'un container, le docker Engine rajoute une nouvelle couche de filesystem "normal" read/write par dessus la pile des couches de l'image.
--

- `docker diff <container>` permet d'observer les changements apportés au conteneur depuis le lancement.

---

# Optimiser la création d'images

- Les images Docker ont souvent une taille de plusieurs centaines de **megaoctets** voir parfois **gigaoctets**. `docker image ls` pour voir la taille des images.
- Or on construit souvent plusieurs dizaines de versions d'une application par jours. (souvent automatiquement sur les serveurs d'intégration continue).
- => L'espace disque devient alors un sérieux problème.

- Le principe de Docker est justement d'avoir des images légères car on va créer beaucoup de conteneurs (un par instance d'application/service)

- De plus on télécharge souvent les images depuis un registry ce qui consomme de la bande passante

- => La principale **bonne pratique** dans la construction d'images est de **limiter leur taille au maximum**.

---

# Limiter la taille d'une image

- Choisir une image linux de base **minimale**:
  - Une image `ubuntu` complète pèse déjà presque une centaine de megaoctets.
  - mais une image trop rudimentaire (`busybox`) est difficile à debugger et peu bloquer pour certaines tache (compilation par exemple)
  - Souvent on utilise des images de base construite à partir de alpine Linux qui est un bon compromis.
  - Ainsi par exemple `python` est fournit en une version `python3.7-alpine`
--

- Limiter le nombre commandes de modification du conteneur:
  - `RUN`, `ADD` et toute commande impliquant une modification du système de fichier du conteneur vas créer un nouveau layer dans l'image.
  - => Souvent on enchaine les commandes en une seule pour économiser des couches. 

---

# Publier des images vers un registry privé

- Généralement les images spécifiques produites par une entreprise n'ont pas vocation à finir dans un dépot public.

- On peut installer des **registry privés** grâce à de nombreuses solutions.

- On utilise alors `docker login <adresse_repo>` pour se logger au répository.

- Plusieurs options:
  - **Gitlab** fournit un registry intégré très intéressant car intégré dans leur workflow DevOps.
  - **Docker Trusted Registry (DTR)** fait partie de **Docker Enterprise** pratique des tests de sécurité sur les images.
  - Plein d'autre solution plus ou moins générique pour stocker des artefacts logiciel.

---

# Créer des conteneurs personnalisés

- Il n'est pas nécessaire de partir d'une image linux vierge pour construire un conteneur.
--

- On peut utiliser la directive `FROM` avec n'importe quelle image.
--

- De nombreuses application peuvent être configurées en étendant une image officielle
  - Exemple: Jenkins dans le TP du 3e jour, pour configurer l'utilisateur par défaut.

--
- L'intérêt ensuite est que l'image est disponible préconfigurée pour construire ou mettre à jour une infrastructure

--
- C'est grâce à cette fonctionnalité que Docker peu être considéré comme un outil d'Infrastructure As Code.

--
- On peut également prendre une sorte snapshot de conteneur en train de tourner sous forme d'image avec `docker commit <image>`.
