

# Faire des vrais tests avec pytest

Dans mylib.py 
```
def func(x):
    return x + 1
```

Dans tests.py
```
from mylib import func

def test_answer():
    assert func(3) == 5
```

puis lancer: `pytest tests.py`

`pytest` considere comme des tests toutes les fonctions qui commencent par `test_`



