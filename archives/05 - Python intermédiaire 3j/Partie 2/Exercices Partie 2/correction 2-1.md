---
title: Correction - Exercice 2.1
weight: 5
draft: false
---

## 2.1 - Fichiers, JSON et dictionnaires

- Écrire une fonction qui prends un nom de fichier en argument et retourne le contenu si elle a été capable de le récupérer. Sinon, elle doit déclencher une exception qui explique en français pourquoi elle n'a pas pu.

{{% expand "correction" %}}

```python
def contenu_fichier(chemin_fichier):
    try:
        with open(chemin_fichier, 'r') as fichier
            contenu = fichier.read() # lire tout le contenu sous forme texte
    except FileNotFoundError:
        raise FileNotFoundError("Fichier non trouvé !")
    except FileNotFoundError:
        raise Exception("Impossible de récupérer le contenu du fichier indiqué")

    return contenu


if __name__ == "__main__":
    contenu = contenu_fichier('ouvrir_fichier.py') # ouvre le code de l'exercice lui même
    print(contenu)

    contenu = contenu_fichier('fichier_inexistant.py') # déclenche notre exception en français
```

{{% /expand %}}

- Écrire une fonction qui remplace un mot par un autre dans un fichier. On pourra pour cela se servir de `une_chaine.replace("mot", "nouveau_mot")` qui renvoie une version modifiée de `une_chaine` en ayant remplacé "mot" par "nouveau mot".

{{% expand "correction" %}}

```python
def remplacer_dans_fichier(chemin_fichier, mot_a_remplacer, nouveau_mot):
    result = contenu_fichier(chemin_fichier).replace(mot_a_remplacer, nouveau_mot)
    try:
        with open(chemin, 'w') as fichier:
            fichier.write(result)
    except:
        raise Exception("Problème pour écrire dans le fichier")

if __name__ == '__main__':
    remplacer_dans_fichier("monfichier.py", "mon_mot", "nouveau_mot")
```

{{% /expand %}}

- Télécharger le fichier `https://app.yunohost.org/community.json` (avec votre navigateur ou `wget` par exemple). Écrire une fonction qui lit ce fichier, le charge en tant que données json et renvoie un dictionnaire Python. Écrire une autre fonction capable de filtrer le dictionnaire pour ne garder que les apps d'un level supérieur ou égal à un level `n` donné en argument. Essayez votre fonction avec le niveau 8.    

{{% expand "correction" %}}
```python
import json
import pprint
from typing import Dict, Any


def json_fichier_vers_dict(file_path: str) -> Dict[Any, Any]:
    with open(file_path, "r") as json_file:
        json_content = json.loads(json_file.read())
    return json_content


def filtrer_yunohost_apps(yunoapps_dict: Dict[str, Dict], level_min: int) -> Dict[str, Dict[Any, Any]]:
    result = {}
    for app_name, app in yunoapps_dict.items():
        try:
            level = int(app['level']) # Transforme le niveau en entier si possible sinon renvoie une exception
        except:
            continue # Si le niveau n'est pas un nombre sauter l'application et passer à la suivante
        if level >= level_min:
            result[app_name] = app
    return result

if __name__ == "__main__":
    # pprint.pprint(json_fichier_vers_dict("community.json")) # pretty print the dictionnary
    
    app_level_8_ou_plus = filtrer_yunohost_apps(
        json_fichier_vers_dict("community.json"),
        8
    )
    pprint.pprint(app_level_8_ou_plus)
```
{{% /expand %}}


- Améliorez le programme précédent pour récupérer la liste directement depuis le programme avec `requests`. Gérer les différentes exceptions qui pourraient se produire (afficher un message en français) : syntaxe json incorrecte, erreur 404, time-out du serveur, erreur SSL

{{% expand "correction" %}}
```python
import json
import pprint
import requests
from typing import Dict, Any


def json_fichier_vers_dict(file_path: str) -> Dict:
    with open(file_path, "r") as json_file:
        json_content = json.loads(json_file.read())
    return json_content

def json_url_vers_dict(url: str) -> Dict:
    try:
        requete = requests.get(url, timeout=30)
        return requete.json()
    except requests.exceptions.SSLError:
        raise Exception("Erreur de chiffrement SSL")
    except requests.exceptions.ContentDecodingError:
        raise Exception("Erreur de décodage du JSON")
    except requests.exceptions.HTTPError:
        raise Exception("Erreur HTTP "+ str(requete.status_code))
    except requests.exceptions.Timeout:
        raise Exception("Erreur Délai de connexion dépassé")


def filtrer_yunohost_apps(yunoapps_dict: Dict[str, Dict], level_min: int) -> Dict[str, Dict[Any, Any]]:
    result = {}
    for app_name, app in yunoapps_dict.items():
        try:
            level = int(app['level']) # Transforme le niveau en entier si possible sinon renvoie une exception
        except:
            continue # Si le niveau n'est pas un nombre sauter l'application et passer à la suivante
        if level >= level_min:
            result[app_name] = app
    return result

if __name__ == "__main__":    
    app_level_8_ou_plus = filtrer_yunohost_apps(
        json_url_vers_dict("https://app.yunohost.org/community.json"),
        8
    )
    pprint.pprint(app_level_8_ou_plus)
```
{{% /expand %}}