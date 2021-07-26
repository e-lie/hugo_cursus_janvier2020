---
title: 16. Stockage de données et ORM
draft: false
weight: 20
---



## Enregistrer des objets avec `pickle`

`pickle` permet de "sérialiser" et "déserialiser" des objets (ou de manière générale des structure de données) en un flux binaire (!= texte).

### Sauvegarde

```python
import pickle

ma_facture = Facture(45)

f = open("save.bin", "wb")   # the 'b' in 'wb' is important !
pickle.dump(ma_facture, f)
```

### Puis recuperation

```python
import pickle

f = open("save.bin", "rb")
ma_facture = pickle.load(f)
```


# Un exemple courant de POO : les ORM (Object Relationnal Mapper)

## Rappels (?) sur SQL

- Base de données : stocker des informations en masse et de manière efficace
- On manipule des tables (des lignes, des colonnes) ...
- Les colonnes sont fortement typées et on peut poser des contraintes (unicité, ...)
- Relations entres les tables, écritures concurrentes, ...
- Exemple de requête :

```sql
# Create a table
CREATE TABLE members (username text, email text, memberSince date, balance real)

# Add a record
INSERT INTO members VALUES ('alice', 'alice@gmail.com', '2017-11-05', 35.14)

# Find records
SELECT * FROM members WHERE balance>0;
```

# Orienté objet : ORM

## SQL "brut" en Python

```python
import sqlite3
conn = sqlite3.connect('example.db')

c = conn.cursor()

# Create a table
c.execute('''CREATE TABLE members
             (username text, email text, memberSince date, balance real)''')

# Add a record
c.execute("INSERT INTO members VALUES ('alice', 'alice@gmail.com', '2017-11-05', 35.14)")

# Save (commit) the changes and close the connection
conn.commit()
conn.close()
```

## Définition - Object Relational Mapping

- Sauvegarder et charger des objets dans une base de donnée de type SQL de manière "transparente"
- Simplifie énormément l'interface entre Python et SQL
   - Python <-> base SQL
   - classes (ou modèle) <-> tables
   - objets <-> lignes
   - attributs <-> colonnes
- Gère aussi la construction et execution des requêtes (query)
- Syntaxe spéciale pour définir les types et les contraintes (en fonction de la lib utilisée)
- Librairie populaire et efficace : `SQLAlchemy` (on utilisera la surcouche `ActiveAlchemy`)

## Exemple de classe / modèle

```python
from active_alchemy import ActiveAlchemy

db = ActiveAlchemy('sqlite:///members.db')

class Member(db.Model):
	username    = db.Column(db.String(25), nullable=False, unique=True)
	email       = db.Column(db.String(50), nullable=True)
	memberSince = db.Column(db.Date,       nullable=False)
    balance     = db.Column(db.Float,      nullable=False, default=0.0)
    active      = db.Column(db.Boolean,    nullable=False, default=True)
```

## Créer des tables et des objets

```python
# Supprimer toutes les tables (attention ! dans la vraie vie on fait des migrations...)
db.drop_all()
# Initialiser toutes les tables dont il y a besoin
db.create_all()

# Créer des utilisateurs
alice   = Member(name="Alice",   memberSince=datetime.date(day=5, month=11, year=2017))
bob     = Member(name="Bob",     memberSince=datetime.date.today(), balance=15)
camille = Member(name="Camille", memberSince=datetime.date(day=7, month=10, year=2018), balance=10)

# Dire qu'on veut les enregistrer
db.session.add(alice)
db.session.add(bob)
db.session.add(camille)

# Commiter les changements
db.session.commit()
```

## Exemple de requete (`query`)

```python
all_members = Member.query().all()

active_members = Member.query()
                .filter(Member.active == True)
                .order_by(Member.memberSince)

for member in active_members:
    print(user.name)
```
