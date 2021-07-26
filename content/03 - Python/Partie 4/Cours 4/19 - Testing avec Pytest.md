---
title: 19. Tester son code 
draft: false
weight: 20
---


### Pourquoi Tester ?

#### "Pour éviter les régressions"

Une modification à un bout du programme peut casser un autre morceau si on y prend pas garde ! Par exemple si on a changer un nom de variable mais pas partout dans le code. Le logiciel a l'air de fonctionner.

Lorsqu'on a un gros logiciel avec une base de code python énorme on ne peut pas facilement connaître tout le code. Même sur un logiciel plus limité on ne peut pas penser à tout.

Comme un logiciel doit pouvoir être en permanence refactorisé pour resté efficace et propre on a vraiment besoin de tests pour tout logiciel d'une certaine taille.

Si vous codez une librairie pour d'autres développeurs/utilisateurs, ces utilisateurs veulent un maximum de tests pour garantir que vous ne laisserait pas des bugs dans la prochaine version et qu'ils peuvent faire confiance à votre code.

#### Pour anticiper les bugs avant qu'ils n'arrivent

Écrire des bons test nécessite d'imaginer les cas limites de chaque fonction. Si on a oublié de gérer le cas `argument = -1` par exemple au moment des tests on peut le remarquer, le corriger et faire en sorte que le test garantisse que ce bug est évité.

#### Pour aider à coder le programme en réfléchissant à l'avance a ce que chaque fonction doit faire

Écrire des tests avant de coder, une pratique qu'on appelle le **Test Driven Development**

## Deux types de tests: tests unitaires et tests d'intégrations

- **Unitaire**: tester chaque fonction et chaque classe. Peur détecter les problèmes locaux à chaque fonction.

- **Intégration**: tester l'application en largeur en appelant le programme ou certaines grosses partie dans un contexte plus ou moins réaliste. Pour détecter les problèmes d'intgégration entre plusieurs parties du programme mais déclenche aussi les problèmes dans les fonctions.

- Généralement les tests **unitaires** sont très **rapides** (on peut les lancer toutes les 5 minutes puisque ça prend 4 secondes)

Généralement les tests d'**intégration** sont **plus lent** puisqu'il faut initialiser toute l'application et son contexte avant de les lancer.

<!-- ## Deux manières de tester: Blackbox ou Glassbox -->



### Test unitaire avec Pytest

Dans `mylib.py`

```python
def func(x):
    return x + 1
```

Dans `tests.py`

```python
from mylib import func

def test_answer():
    assert func(3) == 5
```

### Lancer `Pytest`


- En précisant le fichier de test un fichier: `pytest tests.py` ou `python3 -m pytest tests.py` si on utilise un environnement virtuel python.

- En laissant `pytest` trouver tous les tests du projet : les commandes `pytest` ou `python3 -m pytest` parcourt tous les fichiers python du dossier et considère comme des tests toutes les fonctions qui commencent par `test_`

### Tests d'integration exemple avec Flask

#### Initialiser le contexte de test avec une fixture

(fixture = une fonction de préparation d'un contexte consistant pour les tests)

```python
import os
import tempfile
import pytest

from web_app import web_app

@pytest.fixture
def client():
    with web_app.test_client() as client: # une application flask propose une méthode test_client() pour mettre en place un serveur web destiné aux test
        yield client # pour chaque test la fonction client() renvoie le client de test flask

def test_compute_add_5_5(client): # la fixture client est passée en paramètre de la fonction de test
    return_value = client.get('/add/5/5')
    assert b'5 + 5 = 10' in return_value.data

def test_compute_add_0_0(client):
    return_value = client.get('/add/0/0')
    assert b'0 + 0 = 0' in return_value.data
```

Ces deux tests s'éxecutent en montant un serveur web et en appelant la route (~page web) correspondante. On aurait pu également initialiser une base de données pour le site web avant de lancer les tests avec une fixture par exemple `bdd`.


