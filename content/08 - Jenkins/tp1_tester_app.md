---
title: TP1 - Tester notre application monsterstack
draft: false
---

## Adapter un peu l'application monstericon du TP3 Kubernetes

Nous allons reprendre l'application du TP3 Kubernetes car c'est une application flask multicomposant simple. Cela nous permettra d'illustrer les différents types de tests logiciels.

- Créez un nouveau dossier `jenkins_TPs` sur le bureau (ou ailleurs)  en clonant le projet de base avec `git clone https://github.com/Uptime-Formation/corrections_tp.git -b jenkins_tp1_base tp_jenkins_application`.

- Ouvrez le dossier `jenkins_TPs/jenkins-k8s/tp1_monsterstack_testing` dans VSCode.

- Dans le dossier `src` (nom conventionnel pour le dossier du code source de l'application), créez le fichier `monster_icon.py` avec à l'intérieur le code:

```python
from flask import Flask, Response, request
from flask import jsonify
import requests
import hashlib
import redis
import socket

app = Flask(__name__)
redis_cache = redis.StrictRedis(host='redis', port=6379, socket_connect_timeout=2, socket_timeout=2, db=0)
salt = "UNIQUE_SALT"
default_name = 'John Doe'

page_template = '''
        <html>
          <head>
            <title>Monster Icon</title>
            <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/skeleton/2.0.4/skeleton.css">
          </head>
          <body style="text-align: center">
                <h1>Monster Icon</h1>
                <form method="POST">
                <strong>Hello dear <input type="text" name="name" value="{name}">
                <input type="submit" value="submit"> !
                </form>
                <div>
                  <h4>Here is your monster icon :</h4>
                  <img src="/monster/{name_hash}"/>
                <div>

          </br></br><h4> container info: </h4>
          <ul>
           <li>Hostname: {hostname}</li>
           <li>Visits: {visits} </li>
          </ul></strong>
        </body>
       </html>
    '''

def render(page_template, values):
    return page_template.format(**values)
    
def redis_visits_counter(redis_cache):
    try:
        visits = redis_cache.incr("counter")
    except redis.RedisError:
        visits = "<i>cannot connect to Redis, counter disabled</i>"
    return int(visits)

def hash_name(name, salt):
    salted_name = salt + name
    name_hash = hashlib.sha256(salted_name.encode()).hexdigest()
    return name_hash

@app.route('/', methods=['GET', 'POST'])
def mainpage():

    name = request.form['name'] if request.method == 'POST' else default_name

    values = {
        'name': name,
        'name_hash': hash_name(name, salt),
        'visits': redis_visits_counter(redis_cache),
        'hostname': socket.gethostname()
    }
    
    page = render(page_template, values)

    return page


@app.route('/monster/<name>')
def get_identicon(name):
    image = redis_cache.get(name)
    if image is None:
        print ("Cache miss: picture icon not found in Redis", flush=True)
        r = requests.get('http://dnmonster:8080/monster/' + name + '?size=80')
        image = r.content
    redis_cache.set(name, image)

    return Response(image, mimetype='image/png')

@app.route('/healthz')
def healthz():
    data = {'ready': 'true'}
    return jsonify(data)

if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0')
```

- Créez ensuite un fichier `__init__.py` vide dans `src` pour matérialiser la création d'un module applicatif.


Observons notre application:

- Elle est légèrement différente de celle du TP3 kubernetes pour pouvoir illustrer les 3 types de tests.
- Elle est composées de trois routes web, `/`, `/monster/<name>`, `/healthz`.
- La route principale fait appel à 3 fonctions internes `render`, `hash_name`, `redis_visits_counter`
- Les deux premières ne font pas appel a des ressources externes et peuvent être testées facilement de l'intérieur
- `redis_visits_counter` fait appel a Redis et nécessite donc un redis fonctionnel pour être exécutée


Nous aimerions maintenant tester notre application pour garantir qu'elle fonctionne au fur et a mesure du développement et qu'elle se déploie parfaitement en production.

## Tests unitaires avec Pytest: valider le comportement des fonctions internes du programme

le premier type de test applicatif classique est le **test unitaire**:

il s'agit de **tester chaque fonction** interne du programme pour s'assurer que le changement d'une fonction ne va pas "casser" un autre endroit du programme.

le framework de test le plus populaire en `python` se nomme `pytest` : [documentation de pytest](https://docs.pytest.org/)

- C'est un exécuteur de tests qui cherche automatiquement toutes les fonctions commençant par `test_` pour
    - les exécuter
    - fournir un résultat détaillé sur leurs erreurs et `assertion` lorsqu'elles échouent.

- Il fournit pleins d'outils pour réaliser des suites de tests complexes sans trop d'effort

- Pour lancer les tests (en mode détaillé) on execute `pytest --verbose <dossier ou fichier de test>` ou encore `python -m pytest --verbose <dossier ou fichier de test>`.



- Vérifiez que `pytest` est bien dans le fichier `requirements.txt`
- créez un virtualenv pour notre application avec `virtualenv -p python3 venv` puis activez le avec `source venv/bin/activate`
- installez les dépendances avec `pip3 install -r requirements.txt`

Nous allons maintenant l'utiliser pytest et écrire des tests unitaires :

- Créez un dossier `tests` dans `src` avec à l'intérieur des fichiers `unit_tests.py` et `__init__.py` vides.

```
src
├── __init__.py
├── monster_icon.py
└── tests
    ├── __init__.py
    └── unit_tests.py
```

- Ajoutez le code suivant à `unit_tests.py`:

```python
from ..monster_icon import <importer les fonctions à tester>

def test_render():
    template = '''
    <html>
    <h1>{title}<h1>
    visites: {visits}
    </html>
    '''
    values = {
        'title': 'Pytest!',
        'visits': 32
    }
    result = render(template, values)

    <assertions>


def test_hash_name():
    name = "Jacques"
    salt = "lesel"
    salt2 = "leselbis"
    result = hash_name(name, salt)
    result2 = hash_name(name, salt2)

    assert result
    assert result != result2
```

- Complétez le code avec l'import de la liste des fonctions de monster_icon à tester.

Les tests sont généralement basés sur une série d'assertions c'est à dire de tests qui déclenchent une exception s'ils sont faux.

- Complétez également avec le code avec une série d'assertion pour vérifier que le resultat de la fonction est correct, par exemple les suivantes:
    - le résultat n'est pas vide : `assert result`
    - texte du résultat contient bien la valeur `visits` à remplacée dans le template: `assert str(values['visits']) in result`
    - idem avec la valeur `title`: `assert values['title'] in result`


Observons les tests unitaires: le principe est d'appeler les fonctions à tester à l'intérieur de la fonction de test et vérifier que le résultat est conforme à l'attendu. Ainsi si l'on modifie un fonction le test échouera.

- Lancez les tests avec `python -m pytest --verbose src/tests/unit_tests.py` ils devraient bien se dérouler.

- Modifiez la fonction `render` de `monster_icon.py` en ajoutant: 

```python
    for key, val in values:
        val = val+100 if isinstance(val, int) else val
```

- Relancez les tests et constatez que notre test nous a prévenu que la fonction `render` avait un comportement bizarre.

- Corrigez à nouveau le code en enlevant les 2 lignes précédemment ajoutées.

## Tests d'intégration: tester si les différents composants de l'application sont bien intégrés

Les tests d'intégration sont des tests sur les fonctions du programme qui valident si les différents composants/fonctions d'une application fonctionnent toujours bien ensembles.

Nous allons écrire un test pour la fonction `redis_visits_counter` qui implique un appel à la librairie `redis` et à une véritable base `Redis` (même si on pourrait facilement faire ici du mocking pour avoir un test unitaire plutôt)

- Créez le fichier `integration_tests.py` dans `tests` avec à l'intérieur:

```python
from ..monster_icon import redis_visits_counter
import redis



def test_redis_counter():
    redis_cache = redis.StrictRedis(host='redis', port=6379, socket_connect_timeout=2, socket_timeout=2, db=0)

    result = redis_visits_counter(redis_cache)

    assert result
    assert not isinstance(result, redis.RedisError)
    assert isinstance(result, int)

    result2 = redis_visits_counter(redis_cache)

    assert result2 == result + 1
```

- Expliquez ce que fait le test, en particulier l'initialisation du test et les assertions.

- Lancez le test avec `python -m pytest --verbose src/tests/unit_tests.py` que se passe-t-il ?

- Lancez l'application complète avec `docker-compose up -d --build`

- Relancez le test précédent. L'intégration correct de redis a été testée.


## Tests fonctionnels: vérifier que l'application fonctionne d'un point de vue extérieur

Les fonctionnels sont des tests pour vérifier le bon fonctionnement d'une application en train de tourner.

- Ils sont lancés sur une instance de l'application déployée
- Ils sont lancés de l'extérieur un peu comme une interaction utilisateur
- Ils permettent de valider le bon déploiement
- Les tests fonctionnels sont donc typiques du **déploiement continu** et plus proche du DevOps (même si les autre tests sont souvent aussi la responsabilite du DevOps)
- Il sont notamment utilisés (avec d'autres critères) pour valider l'ensemble de l'application et du déploiement.

Nous disposons d'une application web basique. Une façon simple de vérifier son fonctionnement de l'extérieur est ainsi de lui envoyer des requêtes HTTP et de contrôler ses réponses.

- Créez le fichier `functionnal_tests.py` dans `tests` avec à l'intérieur le code suivant:

```python
"""Launch web functionnal test of monstericon application

Usage:
  functionnal_tests.py <base_url>
"""

from docopt import docopt
import requests

if __name__ == '__main__':
    arguments = docopt(__doc__)

    response = requests.get(arguments['<base_url>'])

    assert response.status_code == 200
    assert 'Monster Icon' in response.text

    response2 = requests.get(arguments['<base_url>']+'/monster/test')

    assert response2.status_code == 200

    print("Application seems Ok :)")
```

- Expliquons le fonctionnement de ce script.

- Lancez le script `functionnal_tests.py` avec l'url `http://localhost:5000`


## Correction

Vous pouvez récupérez la correction de ce TP en clonant comme suit : `git clone -b jenkins_tp1_correction https://github.com/Uptime-Formation/corrections_tp.git jenkins_tp1_correction`


