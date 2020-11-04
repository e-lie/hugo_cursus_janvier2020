---
title: 2. Chaînes de caractères
draft: false
weight: 20
---



![](../../../../images/python/string.png)


#### Syntaxe des chaînes

- Entre simple quote (`'`) ou double quotes (`"`). Par exemple: `"hello"`
- `print("hello")` affiche le texte `Hello`
- `print(hello)` affiche **le contenu d'une variable qui s'apellerait** `Hello`

#### Longueur

```python
m = "Hello world"
len(m)        # -> 11
```

![](../../../../images/python/string.png)

#### Extraction

```python
m[:5]    # -> 'Hello'
m[6:8]   # -> 'wo'
m[-3:]   # -> 'rld'
```

#### Multiplication

```python
"a" * 6    # -> "aaaaaa"
```




#### Concatenation

```python
"Cette phrase" + " est en deux morceaux."
```

```python
name = "Marius"
age = 28
"Je m'appelle " + name + " et j'ai " + str(age) + " ans"
```
#### Construction à partir de données, avec `%s`

```python
"Je m'appelle %s et j'ai %s ans" % ("Marius", 28)
```

#### Construction à partir de données, avec `format`

```python
"Je m'appelle {name} et j'ai {age} ans".format(name=name, age=age)
```

#### Substitution

```python
"Hello world".replace("Hello", "Goodbye")   # -> "Goodbye world"
```

#### Chaînes sur plusieurs lignes

- `\n` est une syntaxe spéciale faisant référence au caractère "nouvelle ligne"

```python
"Hello\nworld"     # -> Hello <nouvelle ligne> world
```

## Interactivité basique avec input

En terminal, il est possible de demander une information à l'utilisateur
avec `input("message")`

```python
reponse = input("Combien font 6 fois 7 ?")
```

N.B. : ce que renvoie `input()` est une chaîne de caractère !


#### Et bien d'autres choses !

c.f. documentation, e.g `https://devdocs.io/python~3.7/library/stdtypes#str`
