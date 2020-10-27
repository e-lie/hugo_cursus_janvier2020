---
title: Exo 4 - Rendre le jeu de carte Pythonique avec le Python Object Model
draft: false
weight: 20
---


## 4.1 Utiliser les syntaxes de liste sur la classe `Paquet`

- Plutôt que d'utiliser `len(mon_paquet.cartes)` pour avoir le nombre de carte on voudrait utiliser `len(mon_paquet)`. Implémentez la méthode spéciale `__len__` pour renvoyer la longueur du paquet. Profitez-en pour empêcher que les utilisateurs de la classe modifient directement le paquet en rendant l'attribut `cartes` privé. Testez votre programme en mettant à jour le code `main.py`

- Maintenant que l'attribut `cartes` n'est plus censé être accessible hors de la classe, nous avons besoin d'un nouvelle méthode pour accéder à une carte du paquet depuis le programme principal. Implémentez la méthode spéciale `__getitem__` pour pouvoir accéder à une carte avec `mon_paquet[position]`. Tester la dans le programme principal.

- Notre `Paquet` ressemble maintenant beaucoup à une véritable liste python. Essayez dans le `main.py` d'utiliser la méthode `shuffle` classique de Python pour mélanger un paquet de carte : Il manque quelque chose.

- Dans l'interpréteur (`python3` ou `ipython3`) affichez la liste des méthode de la classe paquet en utilisant `dir()`. Les méthodes en python sont assignées dynamiquement aux classes et peuvent être modifiées au fur et à mesure du programme. Ajoutons une méthode `__setitem__` directement depuis l'interpréteur (démo). Affichez à nouveau le dictionnaire `dir()` de `mon_paquet` pour voir la nouvelle méthode ajoutée.

- Ajoutez maintenant `__setitem__` dans le code de `Paquet`. Supprimez et remplacez la méthode `melanger` par `shuffle` dans le code du projet.


## 4.2 Itérateurs de carte : génération de la suite de carte à partir d'une carte


Plutôt que de générer les 52 cartes avec une boucle for dans le constructeur du paquet on voudrait utiliser un générateur/itérateur associé à la classe carte.

- Ajoutez à `carte.py` une classe `IterateurDeCarte` pour générer la suite des carte à partir d'un objet carte.
    1. D'abord créez la classe `IterateurDeCarte` qui prend en argument une Carte à la création et qui possède des méthodes `__next__(self)` qui retourne la carte suivante dans l'ordre des cartes et `__iter__` qui lui permet de se renvoyer lui même pour continuer l'itération.
    1. Ajoutez une méthode `__iter__` à la classe carte qui renvoie un itérateur basée sur la carte courante.

- Générez les 52 cartes du paquet à partir de notre iterateur de carte.

- Ajoutez un paramètre facultatif `carte_de_départ` au contructeur de paquet pour commencer la génération du paquet à partie d'une carte du milieu de la série de carte possible.

- Modifiez le constructeur de la classe `Carte` pour qu'elle prenne en argument des valeurs et couleurs possibles qui ne soit pas les valeurs classique. Testez cette fonctionnalité dans `main.py` en générant un jeu de "UNO" (sans les cartes "Joker" noire) à la place d'un jeu classique. ![Cartes de Uno](https://upload.wikimedia.org/wikipedia/commons/2/28/Baraja_de_UNO.JPG)


## Bonus : d'autres générateurs de carte

Les listes sont des collections finies et les itérateurs de liste sont donc toujours finis. Cependant un itérateur n'a pas de taille en général et peut parfois générer indéfiniment des valeurs (grace à un générateur infini par exemple).

- Modifiez l'itérateur de carte pour qu'elle se base sur un générateur de carte aléatoire infini.


## 4.3 Design patterns 'Observateur' appliquée aux chaînes Youtube

Les design patterns sont des patrons de conception qui permettent de gérer de manière des problèmes génériques qui peuvent survenir dans une grande variété de contextes. L'une d'entre elle est la design pattern "observateur". Il définit deux types d'entités "observables" et "observateur". Une observable peut être surveillée par plusieurs observateurs. Lorsque l'état de l'observable change, elle notifie alors tous les observateurs liés qui propage alors le changements.

Concrètement, ceci peut correspondre à des éléments d'interface graphique, des capteurs de surveillances (informatique ou physique), des systemes de logs, ou encore des comptes sur des médias sociaux lorsqu'ils postent de nouveaux messages.

(Reference plus complète : https://design-patterns.fr/observateur )

Nous proposons d'appliquer ce patron de conception pour créer un système avec des journaux / chaines youtube (observables, qui publient des articles / videos) auxquels peuvent souscrire des personnes.

- Créer deux classes Channel (chaîne youtube) et User (suceptibles de s'abonner)
    - Chaque Channel et User a un nom.
    - La classe Channel implémente des méthodes `subscribe` et `unsubscribe` qui ajoutent/enlèvent un compte observateur donné en argument. On introduira également un attribut dans User qui liste les vidéos auxquel un compte est abonné et qui est modifié par les appel de `subscribe` et `unsubscribe`.
    - La classe Channel implémente aussi une méthode `notifySubscribers` qui appelle `compte.actualiser()` pour chaque compte abonné de la chaîne. Pour le moment, la méthode `actualiser` de la classe User ne fait rien (`pass`)

- Ajoutons une méthode `publish` à la classe `Channel` qui permet d'ajouter une vidéo à la liste de vidéo de la chaíne. Chaque vidéo correspondra uniquement à un titre et une date de publication (gérée avec la librairie datetime). Lorsque la méthode publish est appellée, elle déclenche aussi `notifySubscribers`.

- La méthode `actualiser` de la classe `User` s'occupe de parcourir toutes les chaines auxquelles le compte  est abonné, et de récupérer le titre des 3 vidéos les plus récentes parmis toutes ses chaines. Ces 3 titres (et le nom du channel associé!) sont ensuite écris dans `latest_videos_for_{username}.txt`.

- Tester l'ensemble du fonctionnement avec un programme tel que:

```python

arte = Channel("ARTE")
cestpassorcier = Channel("c'est pas sorcier")
videodechat = Channel("video de chat")

alice = User("alice")
bob = User("bob")
charlie = User("charlie")

arte.subscribe(alice)
cestpassorcier.subscribe(alice)
cestpassorcier.subscribe(bob)
videodechat.subscribe(bob)
videodechat.subscribe(charlie)

cestpassorcier.publish("Le système solaire")
arte.publish("La grenouille, un animal extraordinaire")
cestpassorcier.publish("Le génie des fourmis")
videodechat.publish("Video de chat qui fait miaou")
cestpassorcier.publish("Les chateaux forts")
```