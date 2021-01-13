---
title: Démo
weight: 10
draft: true
---

# Un peu de logistique

- **Les slides de présentation et les TD sont disponibles à l'adresse https://cours.hadrienpelissier.fr**

- Pour exporter les TD utilisez la fonction d'impression pdf de google chrome.

<!-- - Nous allons prendre des notes en commun sur un pad interactif CodiMD -->
<!-- - et par là faire une rapide démo de Docker et Docker-Compose. -->

---

# Introduction

![](../../images/Moby-logo.png)

# Des conteneurs

- ## La métaphore docker : "box it, ship it"

- Une abstraction qui ouvre de nouvelles possibilités pour la manipulation logicielle.
- Permet de standardiser et de contrôler la livraison et le déploiement.

---
<!-- 
# Démo

1. Je cherche comment déployer mon logiciel "CodiMD" avec Docker ~~sur Google~~ [dans la documentation officielle de mon logiciel](https://hackmd.io/s/codimd-docker-deployment).
2. Je trouve le fichier "docker-compose.yml". _Docker-Compose permet de déployer plusieurs conteneurs et de les faire interagir ensemble (nous reviendrons dessus en détail au chapitre 4)_
3. Je le télécharge et je le place dans mon dossier de travail. J'ouvre un terminal à cet emplacement.
4. _Ici, on devrait étudier le fichier pour l'adapter et, surtout, changer les mots de passe par défaut dans la configuration._
5. Je vais chercher mon IP publique avec `curl ip.yunohost.org`
6. Je fais `docker-compose up` et j'attends que Codi-MD et sa base de données postgresql associée soient lancées. Le logiciel indique après un peu de temps être bien configuré et disponible à l'adresse `0.0.0.0:3000`.
7. Vous pouvez désormais joindre ce pad sur son adresse IP publique (et sur le port `3000`).
8. Zut, on ne peut pas éditer le pad sans faire de compte. En regardant la documentation, il faut changer la variable d'environnement `CMD_ALLOW_ANONYMOUS` à `true`. J'édite mon fichier `docker-compose.yml`.

---

7. Mais... attendez, l'adresse de pad est incompréhensible !
8. Ce qui aiderait serait de pouvoir rediriger mon IP vers le pad du cours.
9. Docker va me permettre de déployer un serveur nginx juste pour ça rapidement.
10. Ecrivons une configuration nginx simple qui redirige vers notre pad et plaçons-la dans `/nginx.conf` :

```nginx
server {
        listen  80;
        location / {
                proxy_pass  http://$host:3000/;
        }
}
```

```nginx
server {
        listen  80;
        return  http://$host:3000/HPHH9bikSQyZHtoFJUMaOA?both;
}
```

```
server {
        listen   80;
        server_name  0.0.0.0;
        location / {
                proxy_pass         http://hackmd_codimd_1:3000/;
        }
        location = /pad {
		rewrite /pad /HPHH9bikSQyZHtoFJUMaOA break;
                proxy_pass         http://hackmd_codimd_1:3000/;
		proxy_redirect off;
        }
} ```

11. Lançons un conteneur Docker nginx se basant sur ma configuration :
    `docker run -p 80:80 -d -v /nginx.conf:/etc/nginx/conf.d/default.conf --name nginx-pad-proxy nginx`
    With network: `docker run -p 80:80 -d --network hackmd_default -v /tmp/config-nginx:/etc/nginx/conf.d --name nginx-pad-proxy nginx`
     Test config and network: `docker run --rm --network hackmd_default -v /tmp/config-nginx:/etc/nginx/conf.d nginx nginx -t`
12. Rien qu'en tapant mon IP (nous n'avons pas configuré le DNS), on devrait être redirigé·e sur le super pad !

13. Je rajoute rapidement un domaine grâce au service [netlib.re](https://netlib.re)

14. Posez-y vos questions, et annotez toutes les astuces et conseils qui vous ont aidés ou aideraient les autres.
    Structurer en écrivant quelques titres -->

---
