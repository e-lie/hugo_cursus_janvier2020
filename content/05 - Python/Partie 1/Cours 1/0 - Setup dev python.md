---
title: 0. Setup de développement Python
draft: false
weight: 20
---


- VM Linux
- Python (3.x)

### Pour débutter

- **Thonny** : `apt install thonny`

### Plus tard

- **Vim** (éditeur en console pour ninjas)
- **Atom** (IDE relativement minimaliste, épuré et extensible)
- **Pycharm** (IDE très gros qui fait même le café)
- ???


Dans Thonny :

```python
print("Hello, world!")
```


## Parenthèse : Python 2 vs Python 3

- Python 2 existe depuis 2000
- Python 3 existe depuis 2008
- Fin de vie de Python 2 en 2020
- ... mais encore la version par défaut dans de nombreux système ... (c.f. `python --version`)

il faut lancer `python3` explicitement ! <small>(et non `python`)</small>

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

#### `ipython3` : alternative à la console 'classique'

```bash
$ sudo apt install ipython3
$ ipython3
In [1]: print("Hello, world!")
```

#### pour quitter : `exit`
