---
title: Introduction à Docker
---

## _Modularisez et maîtrisez vos applications_

![](../../images/Docker-Logo-White-RGB_Horizontal.png)

---

# Bonjour !

## À propos de vous

- Quels sont vos besoins ?
- Quel rapport à Docker ? Quels préjugés / espoirs ?

---

## À propos de moi

> Hadrien Pélissier

Formateur en DevOps et sécurité informatique
Ex-ingénieur sécurité / DevOps

---

# Un peu de logistique

- ## Les slides de présentation et les TD sont disponibles à l'adresse https://hadrienpelissier.fr/docker

- Pour exporter les TD utilisez la fonction d'impression pdf de google chrome.

--

- Nous allons prendre des notes en commun sur un pad interactif CodiMD et par là faire une rapide démo de Docker.

---

# Introduction

## ![](../../images/Moby-logo.png)

# Des conteneurs

![](../../images/docker.png)

- ## La métaphore docker : "box it, ship it"

- Une abstraction qui ouvre de nouvelles possibilités pour la manipulation logicielle.
- Permet de standardiser et de contrôler la livraison et le déploiement.

]

---

# Démo

1. Je cherche comment déployer mon logiciel "CodiMD" avec Docker ~~sur Google~~ [dans la documentation officielle de mon logiciel](https://hackmd.io/c/codimd-documentation/%2Fs%2Fcodimd-docker-deployment).
2. Je trouve le fichier "docker-compose.yml". _Docker-Compose permet de déployer plusieurs conteneurs et de les faire interagir ensemble (nous reviendrons dessus en détail au chapitre 4)_
3. Je le télécharge et je le place dans mon dossier de travail. J'ouvre un terminal à cet emplacement.
4. _Ici, on devrait étudier le fichier pour l'adapter et, par exemple, changer les mots de passe par défaut dans la configuration._
5. Je fais `docker-compose up` et j'attends que Codi-MD et sa base de données postgresql associée soient lancées. Le logiciel indique après un peu de temps être bien configuré et disponible à l'adresse `0.0.0.0:3000`.
6. Je vais chercher mon IP publique, vous pouvez désormais joindre ce pad pour toute la classe.

---

7. Mais... attendez, l'adresse de pad est incompréhensible !
8. Ce qui aiderait serait de pouvoir rediriger mon IP vers le pad de la classe.
9. Docker va me permettre de déployer un serveur nginx juste pour ça rapidement.
10. Ecrivons une configuration nginx simple qui redirige vers notre pad et plaçons-la dans `/tmp/config-nginx/default.conf` :

```nginx
server {
        listen  80;
        return  http://$host:3000/HPHH9bikSQyZHtoFJUMaOA?both;
}
```

<!-- ```
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
} ``` -->

11. Lançons un conteneur Docker nginx se basant sur ma configuration :
    `docker run -p 80:80 -d -v /tmp/config-nginx:/etc/nginx/conf.d --name nginx-pad-proxy nginx`
    <!-- With network: `docker run -p 80:80 -d --network hackmd_default -v /tmp/config-nginx:/etc/nginx/conf.d --name nginx-pad-proxy nginx` -->
     <!-- Test config and network: `docker run --rm --network hackmd_default -v /tmp/config-nginx:/etc/nginx/conf.d nginx nginx -t` -->
12. Rien qu'en tapant mon IP (nous n'avons pas configuré le DNS), on devrait être redirigé·e sur le super pad !

13. Posez-y vos questions, et annotez toutes les astuces et conseils qui vous ont aidé ou aideraient les autres.
    <!-- Structurer en écrivant quelques titres -->

---

# Retour sur les technologies de virtualisation

On compare souvent les conteneurs aux machines virtuelles. Mais ce sont de grosses simplifications parce qu'on en a un usage similaire : isoler des programmes dans des "contextes".
Une chose essentielle à retenir sur la différence technique : **les conteneurs utilisent les mécanismes internes du \_kernel de l'OS **Linux**\_ tandis que les VM tentent de communiquer avec l'OS (quel qu'il soit) pour directement avoir accès au matériel de l'ordinateur.**

![](../../images/hyperv-vs-containers.png)

- **VM** : une abstraction complète pour simuler des machines

  - un processeur, mémoire, appels systèmes, carte réseau, carte graphique, etc.

- **conteneur** : un découpage dans Linux pour séparer des ressources (accès à des dossiers spécifiques sur le disque, accès réseau).

Les deux technologies peuvent utiliser un système de quotas pour l'accès aux ressources matérielles (accès en lecture/écriture sur le disque, sollicitation de la carte réseau, du processeur)

--

Si l'on cherche la définition d'un conteneur :

### **C'est un groupe de _process_ associé à un ensemble de permissions**.

L'imaginer comme une "boîte" est donc une allégorie un peu trompeuse, car ce n'est pas de la virtualisation (= isolation au niveau matériel).

---

# Docker Origins : genèse du concept de **conteneur**

Les conteneurs mettent en œuvre un vieux concept d'isolation des processus permis par la philosophie Unix du "tout est fichier".

## `chroot`, `jail`, les 6 `namespaces` et les `cgroups`

### `chroot`

- Implémenté principalement par le programme `chroot` [*change root* : changer de racine], présent dans les systèmes UNIX depuis longtemps (1979 !) :
  > "Comme tout est fichier, changer la racine d'un processus, c'est comme le faire changer de système".
  <!-- Illustration tout-est-fichier : sockets, /dev/ … -->

### `jail`

- `jail` est introduit par FreeBSD en 2002 pour compléter `chroot` et qui permet pour la première fois une **isolation réelle (et sécurisée) des processus**.
- `chroot` ne s'occupait que de l'isolation d'un process par rapport au système de fichiers :

  - ce n'était pas suffisant, l'idée de "tout-est-fichier" possède en réalité plusieurs exceptions
  - un process _chrooté_ n'est pas isolé du reste des process et peut agir de façon non contrôlée sur le système sur plusieurs aspects
    <!-- - expliquer chroot: notamment démo de comment on en échappe ? -->

- En 2005, Sun introduit les **conteneurs Solaris** décrits comme un « chroot sous stéroïdes » : comme les _jails_ de FreeBSD

### Les _namespaces_ (espaces de noms)

- Les **_namespaces_**, un concept informatique pour parler simplement de…
  - groupes séparés auxquels on donne un nom, d'ensembles de choses sur lesquelles on colle une étiquette
  - on parle aussi de **contextes**
- `jail` était une façon de _compléter_ `chroot`, pour FreeBSD.
- Pour Linux, ce concept est repris via la mise en place de **namespaces Linux**
  - Les _namespaces_ sont inventés en 2002
  - popularisés lors de l'inclusion des 6 types de _namespaces_ dans le **noyau Linux** (3.8) en **2013**

### Les _namespaces_ (espaces de noms)

- Les conteneurs ne sont finalement que **plein de fonctionnalités Linux saucissonnées ensemble de façon cohérente**.
- Les _namespaces_ correspondent à autant de types de **compartiments** nécessaires dans l'architecture Linux pour isoler des processus.

Pour la culture, 6 types de _namespaces_ :

- **Les namespaces PID** : "fournit l'isolation pour l'allocation des identifiants de processus (PIDs), la liste des processus et de leurs détails. Tandis que le nouvel espace de nom est isolé de ses adjacents, les processus dans son espace de nommage « parent » voient toujours tous les processus dans les espaces de nommage enfants — quoique avec des numéros de PID différent."
- **Network namespace** : "isole le contrôleur de l'interface réseau (physique ou virtuel), les règles de pare-feu iptables, les tables de routage, etc."
- **Mount namespace** : "permet de créer différents modèles de systèmes de fichiers, ou de créer certains points de montage en lecture-seule"
- **User namespace** : isolates the user IDs between namespaces (dernière pièce du puzzle)
- "UTS" namespace : permet de changer le nom d'hôte.
- IPC namespace : isole la communication inter-processus entre les espaces de nommage.

---

### Les _cgroups_ : derniers détails pour une vraie isolation

- Après este à s'occuper de limiter la capacité d'un conteneur à agir sur les ressources matérielles :

  - usage de la mémoire
  - du disque
  - du réseau
  - des appels système
  - du processeur (CPU)

- En 2005, Google commence le développement des **cgroups** : une façon de _tagger_ les demandes de processeur et les appels systèmes pour les grouper et les isoler.

---

# Démo : bloquer le système hôte depuis un simple conteneur

> `:(){ : | :& }; :`

Ceci est une _fork bomb_. Que se passe-t-il si on la lance dans un conteneur **non privilégié** ?

`docker run -it --name fork-bomb bash`

On bloque tout Docker, voire tout le système sous-jacent, en l'empêchant de créer de nouveaux processus.

Pour éviter cela il faudrait limiter la création de processus via une option kernel.

Ex: `docker run -it --ulimit nproc=3 --name fork-bomb bash`

**L'isolation des conteneurs n'est donc ni magique, ni automatique, ni absolue !**
Correctement paramétrée, elle est tout de même assez **robuste, mature et testée**.

---

# Les conteneurs : définition

On revient à notre définition d'un **conteneur** :

### **Un conteneur est un groupe de _processus_ associé à un ensemble de permissions sur le système**.

> 1 container
> = 1 groupe de _process_ Linux
>
> - des _namespaces_ (séparation entre ces groups)
> - des _cgroups_ (quota en ressources matérielles)

---

# LXC (LinuX Containers)

- En 2008 démarre le projet LXC qui chercher à rassembler :

  - les **cgroups**
  - le **chroot**
  - les **namespaces**.

- Originellement, Docker était basé sur **LXC**. Il a depuis développé son propre assemblage de ces 3 mécanismes.

---

# Docker et LXC

- En 2013, Docker commence à proposer une meilleure finition et une interface simple qui facilite l'utilisation des conteneurs **LXC**.
- Puis il propose aussi son cloud, le **Docker Hub** pour faciliter la gestion d'images toutes faites de conteneurs.
- Au fur et à mesure, Docker abandonne le code de **LXC** (mais continue d'utiliser le **chroot**, les **cgroups** et **namespaces**).

- Le code de base de Docker (notamment **runC**) est open source : l'**Open Container Initiative** vise à standardiser et rendre robuste l'utilisation de containers.

---

# Bénéfices par rapport aux machines virtuelles

Docker permet de faire des "quasi-machines" avec des performances proches du natif.

- Vitesse d'exécution.
- Flexibilité sur les ressources (mémoire partagée).
- Moins **complexe** que la virtualisation
- Plus **standard** que les multiples hyperviseurs
  - notamment moins de bugs d'interaction entre l'hyperviseur et le noyau

---

# Bénéfices par rapport aux machines virtuelles

VM et conteneurs proposent une flexibilité de manipulation des ressources de calcul mais les machines virtuelles sont trop lourdes pour être multipliées librement :

- elles ne sont pas efficaces pour isoler **chaque application**
- elles ne permettent pas la transformation profonde que permettent les conteneurs :
  - le passage à une architecture **microservices**
  - et donc la **scalabilité** pour les besoins des services cloud

---

# Avantages des machines virtuelles

- Les VM se rapprochent plus du concept de "boite noire": l'isolation se fait au niveau du matériel et non au niveau du noyau de l'OS.

- même si une faille dans l'hyperviseur reste possible car l'isolation n'est pas qu'uniquement matérielle

- Les VM sont-elles "plus lentes" ? Pas forcément.
  - La RAM est-elle un facteur limite ? Non elle n'est pas cher
  - Les CPU pareil : on est rarement bloqués par la puissance du CPU
  - Le vrai problème c'est l'I/O : l'accès en entrée-sortie au disque et au réseau
    - en réalité Docker peut être bien plus lent pour l'implémentation de la sécurité réseau (usage du NAT et du bridging)
    - pareil pour l'accès au disque : la technologie d'_overlay_ (qui a une place centrale dans Docker) s'améliore mais reste lente.

La comparaison VM / conteneurs est un thème extrêmement vaste et complexe.

---

# Pourquoi utiliser Docker ?

Docker est pensé dès le départ pour faire des **conteneurs applicatifs** :

- **isoler** les modules applicatifs.

- gérer les **dépendances** en les embarquant dans le conteneur.

- se baser sur l'**immutabilité** : la configuration d'un conteneur n'est pas faite pour être modifiée après sa création.

- avoir un **cycle de vie court** -> logique DevOps du "bétail vs. animal de compagnie"

---

# Pourquoi utiliser Docker ?

Docker modifie beaucoup la **"logistique"** applicative.

- **uniformisation** face aux divers langages de programmation, configurations et briques logicielles

- **installation sans accroc** et **automatisation** beaucoup plus facile

- permet de simplifier l'**intégration continue**, la **livraison continue** et le **déploiement continu**

- **rapproche le monde du développement** des **opérations** (tout le monde utilise la même technologie)

--

- Permet l'adoption plus large de la logique DevOps (notamment le concept _d'infrastructure as code_)

---

# Le DevOps

## À propos du mot DevOps

- Un profil ? Un hybride de dev et d'ops...
- Une méthode ? Infra-as-Code, _continuous integration and delivery_ (CI/CD), conteneurisation
- Une façon de virer des adminsys...

---

## Réancrer les programmes dans la **réalité de leur utilisation**

--

"Machines ain't smart. You are!"
Comment dire correctement aux machines quoi faire ?

---

# Infrastructure As Code

Un mouvement d'informatique lié au DevOps et au cloud :

- Rapprocher la production logicielle et la gestion de l'infrastructure
  - Rapprocher la configuration de dev et de production (+ staging)
  - Assumer le côté imprévisible de l'informatique en ayant une approche expérimentale
  - Aller vers de l'intégration et du déploiement continu et automatisé.

---

# Infrastructure As Code

Une façon de définir une infrastructure dans un fichier descriptif et ainsi de créer dynamiquement des services.

- Du code qui décrit **l'état désiré** d'un système.
- Arrêtons de faire de l'admin-sys ad-hoc !

## Avantages :

- **Descriptif** : on peut lire facilement l'**état actuel** de l'infra
- Git ! Gérer les versions de l'infrastructure et collaborer facilement comme avec du code.
- Tester les instrastructure pour éviter les régressions/bugs
- Facilite l'intégration et le déploiement continus
  = vélocité
  = versions testées puis mises en prod' progressivement et automatiquement dans le _cycle DevOps_
- Pas de surprise = possibilité d'agrandir les clusters sans souci !
  - On peut multiplier les machines (une machine ou 100 machines identiques c'est pareil).

Assez différent de l'administration système sur mesure (= méthode de résolution plus ou moins rigoureuse à chaque nouveau bug)

---

# Infrastructure As Code

## Concepts proches

- Infrastructure as a Service (commercial et logiciel)

  - Amazon Web Services, Azure, Google Cloud, DigitalOcean
  - = des VM ou des serveurs dédiés

- Plateform as a Service - Heroku, cluster Kubernetes
  Avec une offre d'hébergement de conteneurs, on parle la plupart du temps de Platform as a Service.

---

## Infrastructure as Code

# Résumé

- on décrit en mode code un état du système. Avantages :
  - pas de dérive de la configuration et du système (immutabilité)
  - on peut connaître de façon fiable l'état des composants du système
  - on peut travailler en collaboration plus facilement (grâce à Git notamment)
  - on peut faire des tests
  - on facilite le déploiement de nouvelles instances

---

# Docker : positionnement sur le marché

- Docker est la technologie ultra-dominante sur le marché de la conteneurisation

  - La simplicité d'usage et le travail de standardisation (OCI) lui garantissent légitimité et fiabilité
  - La logique du conteneur fonctionne, et la bonne documentation et l'écosystème aident !

- **LXC** existe toujours et est très agréable à utiliser, notamment avec **LXD** (développé par Canonical, l'entreprise derrière Ubuntu).

  - Il a cependant un positionnement différent : faire des conteneurs pour faire tourner des OS linux complets.

- **RKT** : un autre container engine développé par **container linux** avec une architecture un peu différente. permet de faire tourner des images docker

- **Apache Mesos** : un logiciel de gestion de cluster qui permet de se passer de Docker, mais propose quand même un support pour Docker et rkt depuis 2016.

---
