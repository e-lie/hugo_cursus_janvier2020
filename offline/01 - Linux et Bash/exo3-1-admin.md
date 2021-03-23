---
title: Administration Linux - feuille d'exercice n.3
draft: false
weight: 17
---

## 5 - Se connecter et gérer un serveur avec SSH

- Récupérez l'adresse IP de votre serveur distant auprès du formateur, récupérez aussi la clé privée permettant de vous y connecter (il est peut-être nécessaire d'activer le copier-coller entre Windows et la VM dans les options de la VM Virtualbox)
- pinguez votre serveur, puis tentez de vous connecter à votre serveur en utilisant la clef (`ssh -i clef_privee_formateur user@machine`)
- La clé ssh fournie par le formateur est commune à toutes les machines. Générons une clef SSH qui vous est propre. Depuis votre machine de bureau (VM) :
  - générez une clef SSH pour votre utilisateur avec `ssh-keygen -t rsa -b 4096 -C "un_commentaire"`;
  - identifiez le fichier correspondant à la clef publique créé (généralement `~/.ssh/un_nom.pub`) ;
  - utilisez `ssh-copy-id -i clef_publique user@machine` ;
  - (notez que sur le serveur, il y a maintenant une ligne dans `~/.ssh/authorized_keys`)
  - tentez de vous reconnecter à votre serveur en utilisant votre propre clé ssh cette fois-ci (`ssh -i clef_privee user@machine`)
  - modifiez sur votre serveur le fichier `~/.ssh/authorized_keys` pour interdire d'accès les gens utilisant la clé ssh donnée par le formateur !
- Depuis votre machine de bureau, configurez `~/.ssh/config` avec ce template. Vous devriez ensuite être en mesure de pouvoir vous connecter à votre machine simplement en tapant `ssh nom_de_votre_machine`

```bash
Host nom_de_votre_machine
    User votre_utilisateur
    Hostname ip_de_votre_machine
    IdentityFile chemin_vers_clef_privee
```

<!-- - 10.1 - connectez-vous dessus en root (si possible en vérifiant la fingerprint du serveur). -->
  <!-- et **changer le mot de passe** ! (Choisir un mot de passe un minimum robuste : il sera mis à l'épreuve !!!). -->

Dans une autre console, constater qu'il y a maintenant une entrée correspondant à votre serveur dans `~/.ssh/known_hosts`.

- Dans votre session SSH, familiarisez-vous avec le système :
  - de quelle distribution s'agit-il ? (`lsb_release -a` ou regarder `/etc/os-release`)
  - quelle est la configuration en terme de CPU, de RAM, et d'espace disque ? (`cat /proc/cpuinfo`, `free -h` et `df -h`)
  - quelle est son adresse IP locale et globale ?
- Donnez un nom à votre machine avec `hostnamectl set-hostname <un_nom>`
- Créer un utilisateur destiné à être utilisé plutôt que de se connecter en root.
- Créez-lui un répertoire personnel et donnez-lui les permissions dessus.
- Définissez-lui un mot de passe.
- Ajoutez votre clé publique dans le dossier `~/.ssh/authorized_keys` de l'utilisateur créé
- Ajoutez-le au groupe `ssh`.
- Assurez-vous qu'il a le droit d'utiliser `sudo`.
- Connectez-vous en ssh avec le nouvel utilisateur.
<!-- Personnalisez le PS1, les alias, et votre .bashrc en général. -->

Créez quelques fichiers de test pour confirmer que vous avez le droit d'écrire dans votre home.

- Définissons maintenant un vrai nom de domaine "public" pour cette machine :
  - allez sur `netlib.re` et se connecter avec les identifiants fourni par le formateur ;
  - créer un _nouveau_ nom de domaine (en `.netlib.re` ou `.codelib.re`). (Ignorez les nom déjà créé, ce sont ceux de vos collègues !) ;
  - une fois créé, cliquez sur le bouton 'Details' puis (en bas) ajoutez un nouvel enregistrement de type 'A' avec comme nom '@' et comme valeur l'IP globale(!) de votre serveur ;
  - de retour dans une console, tentez de résoudre et pinger le nom de domaine à l'aide de `host` et `ping` ;
  - modifiez votre `~/.ssh/config` pour remplacer l'ip de la machine par son domaine, puis tentez de vous reconnecter en SSH.
- 10.9 - Depuis votre machine de bureau (VM), récupérez sur internet quelques images de chat ou de poney et mettez-les dans un dossier. Utilisez `scp` pour envoyer ce dossier sur le serveur.

### Exercices avancés

- Activer le plugin VSCode SSH dans Visual Studio Code et se connecter à votre serveur de cette façon

- Installez MobaXterm sous Windows et essayez de vous connecter à votre serveur avec cet outil.

- Utilisez `sshfs` pour monter le home de votre utilisateur dans un dossier de votre répertoire personnel.
- Utilisez `ssh -D` pour créer un tunnel avec votre serveur, et configurez Firefox pour utiliser ce tunnel pour se connecter à Internet. Confirmez que les changements fonctionnent en vérifiant quelle semble être votre IP globale depuis Firefox.
