---
title: Correction 4.4 - Application du design pattern observateur
draft: false
weight: 32
---

## 4.4 Design patterns 'Observateur' appliquée aux chaînes Youtube

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


{{% expand "correction" %}}
```python
import datetime

class Channel():
    
    def __init__(self, name):
    
        self.name = name
        self.subscribers = []
        self.videos = []
    
    def subscribe(self, user):
        
        self.subscribers.append(user)
        user.channels.append(self)
        
    def unsubscribe(self, user):
    
        self.subscribers.remove(user)
        user.channels.remove(self)
    
    def notifySubscribers(self):

        for subscriber in self.subscribers:
            subscriber.update()
            
    def publish(self, titre_de_video):
        
        self.videos.append({
            "titre": titre_de_video,
            "date": datetime.datetime.now()
        })
        self.notifySubscribers()


class User():
    
    def __init__(self, name):
        
        self.name = name
        self.channels = []
        
    def update(self):
        
        # Obtenir la liste de toutes les vidéos de tous les channels auquel on a subscribe  ...
        all_videos = []
        for channel in self.channels:
            
            # Pour chaque video, on veut garder le nom du channel d'origine. On le rajoute donc à la volée :
            videos_for_this_channel = channel.videos.copy()
            for video in videos_for_this_channel:
                video["channel"] = channel.name
            
            all_videos += videos_for_this_channel
        
        # ... ordonné par date de publication
        all_videos_sorted = sorted(all_videos, key=lambda v: v["date"], reverse=True)
        
        # ... et seulement les 3 les plus récentes
        most_recent_videos = all_videos_sorted[:3]
        
        with open(f"most_recent_videos_for_{self.name}.txt", "w") as f:
            for video in most_recent_videos:
                f.write(f"[{video['channel']}] {video['titre']} (publiée le {video['date']}) \n")
        

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
{{% /expand %}}