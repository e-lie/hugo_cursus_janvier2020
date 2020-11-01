---
title: 0. Setup de développement Python
draft: false
weight: 20
---



Le premier outil développement est bien sur l'interpréteur lui même utiliser pour lancer un fichier de code.

## Python 2 vs Python 3

- Python 2 existe depuis 2000
- Python 3 existe depuis 2008
- Fin de vie de Python 2 en 2020
- ... mais encore la version par défaut dans de nombreux système ... (c.f. `python --version`)

Généralement il faut lancer `python3` explicitement ! (et non `python`) pour utiliser python3

### Différences principales

- `print "toto"` ne fonctionnera pas en Python 3 (utiliser `print("toto")`
- Nommage des paquets debian (`python-*` vs `python3-*`)
- Gestion de l'encodage
- `range`, `xrange`
- Disponibilité des librairies ?

Il existe des outils comme 2to3 pour ~automatiser la transition

### Executer un script explicitement avec python

```bash
$ python3 hello.py
```

### ou implicitement (shebang)

```python
#!/usr/bin/env python3

print("Hello, world!")
```

puis on rend le fichier executable et on l'execute

```bash
$ chmod +x hello.py
$ ./hello.py
```


### En interactif

```bash
$ python3
>>> print("Hello, world!")
```

### `ipython3` : alternative à la console python 'classique'

```bash
$ sudo apt install ipython3
$ ipython3
In [1]: print("Hello, world!")
```

#### Principaux avantages:

- Complétion des noms de variables et de modules avec TAB
- Coloré pour la lisibilité
- Plus explicite parfois
- des commandes magiques comme `%cd`, `%run script.py`, 

#### Inconvénients:

- Moins standard
- à installer en plus de l'interpréteur python.

##### pour quitter : `exit`
