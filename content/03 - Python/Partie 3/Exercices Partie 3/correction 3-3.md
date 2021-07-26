---
title: Correction exercice 3.3 - Introduction aux ORM avec ActiveAlchemy
draft: false
weight: 20
---


On se propose de reprendre le jeu de données des apps Yunohost (Exos part 2, fichier `app.yunohost.org/community.json`) et d'importer ces données dans une base SQL (plus précisémment SQLite)

- Installer `active_alchemy` à l'aide de `pip3`

- Créer un fichier `mydb.py` qui se contente de créer une base `db` (instance de ActiveAlchemy) de type sqlite. Dans la suite, on importera l'objet `db` depuis `mydb.py` dans les autres fichiers si besoin.

- Créer un fichier `models.py` et créer dedans une classe (aussi appellé modèle) `App`. On se limitera aux attributs (aussi appellés champs / colonnes) suivants : 
    - un **nom** qui est une chaîne de caractère *unique* parmis toutes les `App` ;
    - un **niveau** qui est un entier (ou vide) ;
    - une **adresse** qui est une chaîne de caractère *unique* parmis toutes les `App` ;

- Créer un fichier `nuke_and_reinit.py` dont le rôle est de détruire et réinitialiser les tables, puis de les remplir avec les données du fichier json. On utilisera pour ce faire `db.drop_all()` et `db.create_all()`. Puis, itérer sur les données du fichier json pour créer les objets `App` correspondant. Commiter les changements à l'aide de `db.session.add` et `commit`.

- Créer un fichier `analyze.py` qui cherche et affiche le nom de toutes les `App` connue avec un niveau supérieur ou égal à `n`. En utilisant l'utilitaire bash `time` (ou bien avec `time.time()` en python), comparer les performances de `analyze.py` avec un script python équivalent mais qui travaille à partir du fichier `community.json` directement (en local, pas via `requests.get`)




#### `mydb.py`

```python
from active_alchemy import ActiveAlchemy

db = ActiveAlchemy('sqlite:///apps.db')
```

#### `models.py`

```python
from mydb import db

class App(db.Model):
    name = db.Column(db.String(20), unique=True, nullable=False)
    level = db.Column(db.Integer, nullable=True)
    url = db.Column(db.String(50), unique=True, nullable=False)
    
    def __repr__(self):
        return "<App " + self.name + ">"
```

#### `nuke_and_reinit`

```python
import json
from mydb import db
from models import App

db.drop_all()
db.create_all()

with open("apps.json") as f:
    apps_from_json = json.loads(f.read())

for app, infos in apps_from_json.items():
    a = App(name=app, level=infos["level"], url=infos["git"]["url"])
    db.session.add(a)

db.session.commit()

apps_level_3 = App.query().filter(App.level == 3)
for app in apps_level_3:
    print(app.name)

```
