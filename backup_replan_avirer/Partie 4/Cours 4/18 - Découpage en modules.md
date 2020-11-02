---
title: 19. Organiser son code en modules
draft: false
weight: 20
---


Considérant les fichiers suivants :

```bash
├── main.py
└── mylib/
    ├── __init__.py
    └── bonjour.py      # <-- Contient "def dire_bonjour..."
```

Depuis `main.py`, je peux faire

```python
from mylib.bonjour import dire_bonjour

dire_bonjour("Alex") # -> "Bonjour Alex !"

print(dire_bonjour)
# -> <function dire_bonjour at 0x7fb964fab668>
```

Considérant les fichiers suivants :

```bash
├── main.py
└── mylib/
    ├── __init__.py
    └── bonjour.py      # <-- Contient "def dire_bonjour..."
```

Depuis `main.py`, je peux *aussi* faire

```python
from mylib import bonjour

bonjour.dire_bonjour("Alex") # -> "Bonjour Alex !"

print(bonjour)
# -> <module 'mylib.bonjour' from 'mylib/bonjour.pyc'>
```