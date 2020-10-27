---
title: Correction Exo 4 - Rendre le jeu de carte Pythonique avec le Python Object Model
draft: false
weight: 20
---


## 4.1 Utiliser les syntaxes de liste sur la classe `Paquet`


{{% expand "`carte.py`" %}}

Idem 4.2

{{% /expand %}}

{{% expand "`paquet.py`" %}}

```python
from carte import Carte
import random

class Paquet:
    
    def __init__(self):
        self._cartes = []
        for valeur in Carte.valeurs_valides:
            for couleur in Carte.couleurs_valides:
                c = Carte(valeur, couleur)
                self._cartes.append(c)
                
    def __repr__(self):
        return str(self._cartes)

    # Permet d'utiliser la fonction len(mon_paquet)
    def __len__(self):
        return len(self._cartes)

    # Permet d'utiliser les notations de liste : mon_paquet[3] ou mon_paquet[2:5] (slice)
    def __getitem__(self, position):
        return self._cartes[position]

    # Permet d'utiliser les assignations de liste : mon_paquet[3] = carte("DAME", "TREFLE")
    def __setitem__(self, position, carte):
        self._cartes[position] = carte
    
    def couper(self):
        coupe_index = random.randint(0, len(self._cartes))
        self._cartes = self._cartes[coupe_index:] + self._cartes[:coupe_index]

    def piocher(self):
        pioche = self._cartes[0]
        self._cartes = self._cartes[1:]
        return pioche
    
    def distribuer(self, nb_joueurs, nb_cartes):
        distribution = [ [] for i in range(0, nb_joueurs)]
        for i in range(0, nb_joueurs):
            for _ in range(0, nb_cartes):
                carte = self.piocher()
                distribution[i].append(carte)
        return distribution
        
```

{{% /expand %}}

{{% expand "`main.py`" %}}

```python
from carte import Carte
from random import shuffle

c1 = Carte(3, "PIQUE")
c2 = Carte("DAME", "COEUR")

print(c1)
print(c1.points)

print(c2)
print(c2.points)

#############################

from paquet import Paquet

P = Paquet()

print("\nAvant coupe\n")
print(P)

P.couper()


print("\nApres coupe\n")
print(P)

shuffle(P)

print("\nApres melange\n")
print(P)


print(f"Actuellement il y a {len(P)} cartes dans le paquet")
print(P.piocher())
print(P.piocher())
print(f"Actuellement il y a {len(P)} cartes dans le paquet")

cartes_alice, cartes_bob = P.distribuer(nb_joueurs=2, nb_cartes=5)
print(cartes_alice)
print(cartes_bob)



```

{{% /expand %}}

## 4.2 Itérateurs de carte : génération de la suite de carte à partir d'une carte

{{% expand "`carte.py`" %}}

```python
class InvalidCardColor(ValueError):
    pass


class InvalidCardValue(ValueError):
    pass


class Carte:
    """Représente une carte à jouer classique d'un jeu de 52 cartes"""
    
    def __init__(self, valeurs_valides, couleurs_valides, valeur, couleur):
        self.valeurs_valides = valeurs_valides
        self.couleurs_valides = couleurs_valides
        # La bonne méthode pour gérer les valeurs incorrectes en python est généralement de lever une exception
        # (traditionnellement de type ValueError qui est faite pour cela mais une Exception spécifique c'est encore mieux car cela explique l'erreur à l'utilisateur de la classe)
        if valeur not in self.valeurs_valides:
            raise InvalidCardValue
        if couleur not in self.couleurs_valides:
            raise InvalidCardColor
        self._valeur = valeur
        self._couleur = couleur

    @property
    def valeur(self):
        return self._valeur

    @valeur.setter
    def valeur(self, valeur):
        if valeur not in self.valeurs_valides:
            raise InvalidCardValue
        self._valeur = valeur

    @property
    def couleur(self):
        return self._couleur

    @couleur.setter
    def couleur(self, couleur):
        if couleur not in self.couleurs_valides:
            raise InvalidCardColor
        self._couleur = couleur

    @property
    def points(self):
        return Carte.valeurs_valides.index(self.valeur) + 1
        
    def __repr__(self):
        return f"<Carte {self.valeur} de {self.couleur}>"

    def __iter__(self):
        return IterateurDeCarte(self.valeurs_valides, self.couleurs_valides, self)



class IterateurDeCarte:
    def __init__(self, valeurs_valides, couleurs_valides, carte):
        self.valeurs_valides = valeurs_valides
        self.couleurs_valides = couleurs_valides
        self._tuples_valeur_couleur = [(valeur, couleur) for valeur in valeurs_valides
                                               for couleur in couleurs_valides]
        self.index = self._tuples_valeur_couleur.index((carte.valeur, carte.couleur))

    def __next__(self):
        if self.index+1 == len(self._tuples_valeur_couleur) :
            raise StopIteration
        tuple_carte_suivante = self._tuples_valeur_couleur[self.index+1]
        self.index = self.index + 1
        return Carte(self.valeurs_valides, self.couleurs_valides, tuple_carte_suivante[0], tuple_carte_suivante[1])

    def __iter__(self):
        # Une fois qu'il a renvoyé un élément et a mis l'index à jour,
        # l'itérateur doit se renvoyer au contexte appelant pour que l'itération puisse continuer
        return self

```

{{% /expand %}}



{{% expand "`paquet.py`" %}}

```python
from carte import Carte
import random

class Paquet:
    
    def __init__(self, carte_initiale):
        self._cartes = list(iter(carte_initiale))
                
    def __repr__(self):
        return str(self._cartes)

    # Permet d'utiliser la fonction len(mon_paquet)
    def __len__(self):
        return len(self._cartes)

    # Permet d'utiliser les notations de liste : mon_paquet[3] ou mon_paquet[2:5] (slice)
    def __getitem__(self, position):
        return self._cartes[position]

    # Permet d'utiliser les assignations de liste : mon_paquet[3] = carte("DAME", "TREFLE")
    def __setitem__(self, position, carte):
        self._cartes[position] = carte
    
    def couper(self):
        coupe_index = random.randint(0, len(self._cartes))
        self._cartes = self._cartes[coupe_index:] + self._cartes[:coupe_index]

    def piocher(self):
        pioche = self._cartes[0]
        self._cartes = self._cartes[1:]
        return pioche
    
    def distribuer(self, nb_joueurs, nb_cartes):
        distribution = [ [] for i in range(0, nb_joueurs)]
        for i in range(0, nb_joueurs):
            for _ in range(0, nb_cartes):
                carte = self.piocher()
                distribution[i].append(carte)
        return distribution
```

{{% /expand %}}

{{% expand "`main.py`" %}}

```python
from carte import Carte
from paquet import Paquet
from random import shuffle


# On utilise ici des tuples car il sont immutable (pas possible d'ajouter des couleurs/valeurs pendant l'exécution)
# On utilise des noms en majuscule car c'est une convention pour dire que ce sont des constantes
# On aurait également put utiliser des enums pour représenter les couleurs/valeurs possibles (classique en programmation)
couleurs_valides = ("ROUGE", "VERT", "BLEU", "JAUNE")
valeurs_valides = tuple(list(range(0,10)) + ["CHANGE_SENS", "PASSE_TOUR", "+2_CARTES"])

carte_uno_initiale = Carte(valeurs_valides, couleurs_valides, 0, "ROUGE")

paquet_uno = Paquet(carte_uno_initiale)

print(paquet_uno)
shuffle(paquet_uno)
print(paquet_uno)
```

{{% /expand %}}

#### Bonus : d'autres générateurs de carte

Les listes sont des collections finies et les itérateurs de liste sont donc toujours finis. Cependant un itérateur n'a pas de taille en général et peut parfois générer indéfiniment des valeurs.

Bonus: Modifiez l'itérateur de carte pour en faire un générateur de carte aléatoire infini.


## 4.3 Design patterns 'Observateur' appliqué aux chaînes Youtube

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