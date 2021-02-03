---
title: "TP4 - Orchestration, Serveur de contrôle et Cloud" 
draft: false
weight: 24
---


## Cloner le projet modèle

- Pour simplifier le démarrage, clonez le dépôt de base à l'adresse [https://github.com/e-lie/ansible_tp_corrections](https://github.com/e-lie/ansible_tp_corrections).
- Renommez le clone en tp4.
- ouvrez le projet avec VSCode.
- Activez la branche `tp4_correction` avec `git checkout tp4_correction`.

## Facultatif: Infrastructure dans le cloud avec Terraform et Ansible

{{% expand "Facultatif  :" %}}

### Digitalocean token et clé SSH

- Pour louer les machines dans le cloud pour ce TP vous aurez besoin d'un compte digitalocean : celui du formateur ici mais vous pouvez facilement utiliser le votre. Il faut récupérer les éléments suivant pour utiliser le compte de cloud du formateur:
    - un token d'API digitalocean fourni pour la formation. Cela permet de commander des machines auprès de ce provider.

<!-- 
- Récupérez sur git la paire clé ssh adaptée: [https://github.com/e-lie/id_ssh_shared.git](https://github.com/e-lie/id_ssh_shared.git). Utilisez "clone or download" > "Download as ZIP". Puis décompressez l'archive.
- mettez la paire de clé `id_ssh_shared` et `id_ssh_shared.pub` dans le dossier `~/.ssh/`. La passphrase de cette clé est `trucmuch42`.
- Rétablissez les droits `600` sur la clé privée : `chmod 600 ~/.ssh/id_ssh_shared`.
- faites `ssh-add ~/.ssh/id_ssh_shared` pour vérifier que vous pouvez déverrouiller deux clés (l'ancienne avec votre passphrase et la nouvelle paire que vous venez d'ajouter) -->

<!-- - Si vous utilisez votre propre compte, vous aurez besoin d'un token personnel. Pour en crée allez dans API > Personal access tokens et créez un nouveau token. Copiez bien ce token et collez le dans un fichier par exemple `~/Bureau/compte_digitalocean.txt`. (important détruisez ce token à la fin du TP par sécurité).

- Copiez votre clé ssh (à créer sur nécessaire): `cat ~/.ssh/id_ed25519.pub`
- Aller sur digital ocean dans la section `account` en haut à droite puis `security` et ajoutez un nouvelle clé ssh. Notez sa fingerprint dans le fichier précédent. -->


### Installer terraform et le provider ansible

Terraform est un outils pour décrire une infrastructure de machines virtuelles et ressources IaaS (infrastructure as a service) et les créer (commander). Il s'intègre en particulier avec AWS, DigitalOcean mais peut également créer des machines dans un cluster VMWare en interne (on premise) pour créer par exemple un cloud mixte.

Terraform est notamment à l'aide d'un dépôt ubuntu/debian. Pour l'installer lancez:

```bash
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
sudo apt-add-repository "deb [arch=$(dpkg --print-architecture)] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
sudo apt install terraform
```

- Testez l'installation avec `terraform --version`

Pour pouvoir se connecter à nos VPS, ansible doit connaître les adresses IP et le mode de connexion ssh de chaque VPS. Il a donc besoin d'un inventaire.

Jusqu'ici nous avons créé un inventaire statique c'est à dire un fichier qui contenait la liste des machines. Nous allons maintenant utiliser un inventaire dynamique c'est à dire un programme qui permet de récupérer dynamiquement la liste des machines et leurs adresses en contactant une API.

- L'inventaire dynamique pour terraform est [https://github.com/nbering/terraform-inventory/](https://github.com/nbering/terraform-inventory/). Normalement il est déjà installé avec la correction du TP4.

### Terraform avec DigitalOcean

- Le fichier qui décrit les VPS et ressources à créer avec terraform est `provisionner/terraform/main.tf`. Nous allons commenter ensemble ce fichier:

!! La documentation pour utiliser terraform avec digitalocean se trouve ici [https://www.terraform.io/docs/providers/do/index.html](https://www.terraform.io/docs/providers/do/index.html)

Pour terraform puisse s'identifier auprès de digitalocean nous devons renseigner le token et la fingerprint de clé ssh. Pour cela:

- copiez le fichier `terraform.tfvars.dist` et renommez le en enlevant le `.dist`
- collez le token récupéré précédemment dans le fichier de variables `terraform.tfvars`
- normalement la clé ssh `id_stagiaire` est déjà configuré au niveau de DigitalOcean et précisé dans ce fichier. Elle sera donc automatiquement ajoutée aux VPS que nous allons créer.

- Maintenant que ce fichier est complété nous pouvons lancer la création de nos VPS:
  - `terraform init` permet à terraform de télécharger les "driver" nécessaire pour s'interfacer avec notre provider. Cette commande crée un dossier .terraform
  - `terraform plan` est facultative et permet de calculer et récapituler les créations modifications de ressources à partir de la description de `main.tf`
  - `terraform apply` permet de déclencher la création des ressources.

- La création prend environ 1 minute.

Maintenant que nous avons des machines dans le cloud nous devons fournir leurs IP à Ansible pour pouvoir les configurer. Pour cela nous allons utiliser un inventaire dynamique.

### Terraform dynamic inventory

Une bonne intégration entre Ansible et Terraform permet de décrire précisément les liens entre resource terraform et hote ansible ainsi que les groupes de machines ansible. Pour cela notre binder propose de dupliquer les ressources dans `main.tf` pour créer explicitement les hotes ansible à partir des données dynamiques de terraform.

- Ouvrons à nouveau le fichier `main.tf` pour étudier le mapping entre les ressources digitalocean et leur duplicat ansible.

- Pour vérifier le fonctionnement de notre inventaire dynamique, allez à la racine du projet et lancez:

```
source .env
./inventory_terraform.py
```

- La seconde appelle l'inventaire dynamique et vous renvoie un résultat en json décrivant les groupes, variables et adresses IP des machines crées avec terraform.

- Complétez le `ansible.cfg` avec le chemin de l'inventaire dynamique: `./inventory_terraform.py`

- Nous pouvons maintenant tester la connexion avec ansible directement: `ansible all -m ping`.

{{% /expand %}}

## Infrastructure multi-tiers avec load balancer

Pour configurer notre infrastructure:

- Installez les roles avec `ansible-galaxy install -r roles/requirements.yml -p roles`.

- Si vous n'avez pas fait la partie Terraform:
  - complétez l'inventaire statique (inventory.cfg)
  - changer dans ansible.cfg l'inventaire en `./inventory.cfg` comme pour les TP précédents
  - Supprimez les conteneurs app1 et app2 du TP précédent puis lancez le playbook de provisionning lxd : `sudo ansible-playbook provisionner/provision_lxd_infra.yml`

- Lancez le playbook global `site.yml`

- Utilisez la commande `ansible-inventory --graph` pour afficher l'arbre des groupes et machines de votre inventaire
- Utilisez la de même pour récupérer l'ip du `balancer0` (ou `balancer1`) avec : `ansible-inventory --host=balancer0`
- Ajoutez `hello.test` et `hello2.test` dans `/etc/hosts` pointant vers l'ip de `balancer0`.

- Chargez les pages `hello.test` et `hello2.test`.

- Observons ensemble l'organisation du code Ansible de notre projet.
    - Nous avons rajouté à notre infrastructure un loadbalancer installé à l'aide du fichier `balancers.yml`
    - Le playbook `upgrade_apps.yml` permet de mettre à jour l'application en respectant sa haute disponibilité. Il s'agit d'une opération d'orchestration simple en les 3 serveurs de notre infrastructure.
    - Cette opération utilise en particulier `serial` qui permet de d'exécuter séquentiellement un play sur un fraction des serveurs d'un groupe (ici 1 à la fois parmis les 2).
    - Notez également l'usage de `delegate` qui permet d'exécuter une tache sur une autre machine que le groupe initialement ciblé. Cette directive est au coeur des possibilités d'orchestration Ansible en ce qu'elle permet de contacter un autre serveur ( déplacement latéral et non pas master -> node ) pour récupérer son état ou effectuer une modification avant de continuer l'exécution et donc de coordonner des opérations.
    - notez également le playbook `exclude_backend.yml` qui permet de sortir un backend applicatif du pool. Il s'utilise avec des variables en ligne de commande


- Désactivez le noeud qui vient de vous servir la page en utilisant le playbook `exclude_backend.yml`:

```
ansible-playbook --extra-vars="backend_name=<noeud a desactiver> backend_state=disabled" playbooks/exclude_backend.yml
```

- Rechargez la page: vous constatez que c'est l'autre backend qui a pris le relais.

- Nous allons maintenant mettre à jour

## Falcultatif : ajoutons un serveur de control AWX (/ Ansible Tower)

{{% expand "Facultatif  :" %}}
- Choisissez un mot de passe et chiffrez le avec `ansible-vault encrypt_string <votre_mot_de_passe>`.

- Copiez le résultat et collez le comme valeur d'une variable `awx_admin_password` à la place de `unsecurepass` dans le fichier `group_vars/awxservers.yml` comme suit:

```yaml
awx_admin_password: !vault |
    $ANSIBLE_VAULT;1.1;AES256
    37336233643131373366333466313564303764383339353764383939353265616466633761613264
    3862663431343163353639313038623037343261363036310a366434386635333734356638353439
    34636338623866353363643232646539366532343061633037666636383136653932306563633538
    6532343239636637340a366661373039373138303737373837343639376532393962323763343139
    6538
```

- Lancez le playbook `awx.yml` avec la commande: `ansible-playbook --ask-vault-pass playbooks/install_awx.yml`. Déverrouillez votre vault avec le mdp précédent.

- L'installation prend un certain temps à se terminer.  Relancez la si nécessaire.

- Comme précédemment, lancez l'inventaire dynamique avec `ansible-inventory` pour récupérer l'ip de `awx0`
  
- Visitez cette dans un navigateur: AWX devrait démarrer.

- Complétez l'inventaire statique `inventory.cfg` avec les ip de l'inventaire dynamique (pour que la config soit plus simple une fois dans AWX)
{{% /expand %}}

<!-- ## Versionner le projet et utiliser la CI gitlab avec Ansible pour automatiser le déploiement

- Créez un compte sur la forge logicielle `gitlab.com` et créez un projet (dépôt) public `tp4_infra`.
- Affichez et copiez `cat ~/.ssh/id_ed25519.pub`.
- Dans `(User) Settings > SSH Keys`, collez votre clé publique copiée dans la quesiton précédente.
- Suivez les instructions pour pousser le code du tp4 sur ce dépôt.
- Cliquez sur `web IDE`, un bouton à droite dans l'interface de gitlab. Cet éditeur permet de développer directement dans le navigateur et commiter vos modification directement dans des branches sur le serveur.

- Ajoutez à la racine du projet un fichier `.gitlab-ci.yml` avec à l'intérieur:

```yaml
image:
  # This linux container (docker) we will be used for our pipeline : ubuntu bionic with ansible preinstalled in it
  name: williamyeh/ansible:ubuntu18.04

variables:
    ANSIBLE_CONFIG: $CI_PROJECT_DIR/ansible.cfg

deploy:
  # The 3 lines after this are used activate the pipeline only when the master branche changes
  only:
    refs:
      - master
  script:
    - ansible --version
```

En poussant du nouveau code dans master ou en mergant dans master le playbook est automatiquement lancé dans un pipeline: c'est le principe de la CI/CD Gitlab. `only: refs: master` sert justement à indiquer de limiter l'exécution des pipelines à la branche master.

- Cliquez sur `commit` dans le web IDE et cochez `merge to master branch`. Une fois validé votre code déclenche donc directement une exécution du pipeline.

- Vous pouvez retrouver tout l'historique de l'exécution des pipelines dans la Section `CI / CD > Jobs` rendez vous dans cette section pour observer le résultat de la dernière exécution.

!!! Notre pipeline nous permet uniquement de vérifier la bonne disponibilité d'ansible.

!!! Il est basé une image docker contenant Ansible pour ensuite executer notre projet d'IaC.

Nous allons maintenant configurer le pipeline pour qu'il puisse se connecter à nos serveurs de cloud. Pour cela nous avons principalement besoin de charger l'identité/clé SSH dans le contexte du pipeline et la déverrouiller.

- Affichez le contenu de votre clé privé `.ssh/id_ed25519`
- Visitez dans le projet dans la section `Settings> CI/CD > variables` et ajoutez une variable `ID_SSH_PRIVKEY` en mode `protected` (sans l'option `masked`).

- Pour charger l'identité dans le contexte du pipeline ajoutez la section `before_script` suivante entre `variables` et `deploy`:

```yaml
before_script: # some steps to execute before the main pipeline stage

  # Those command lines are use to activate the SSH identity in the pipeline container
  # so the SSH command from the deploy stage will be able to authenticate.
  - eval `ssh-agent -s` > /dev/null # activate the agent software which manage the ssh identity
  - echo "$ID_SSH_PRIVKEY" > /tmp/privkey # getting the identity key from gitlab to put it in a file
  - chmod 600 /tmp/privkey # restrict access to this file because ssh require it
  - ssh-add /tmp/privkey; rm /tmp/privkey # unlock identity for connection and remove the key file
  - mkdir -p /root/.ssh # create an ssh configuration folder
  - echo -e "Host *\n\tStrictHostKeyChecking no\n\n" > /root/.ssh/config # configure ssh not to bother of server identity (slightly unsecure mode for the workshop)

```

- Remplacez `ansible --version` par un ping de toutes les machines.
- Relancez le pipeline en commitant(/poussant) vos modifications dans master.

- Allez observer le job en cours d'exécution.

- Enfin lançons notre playbook principal en remplaçant la commande ansible précédente dans le pipeline et commitant. -->

<!-- - Ajoutez une plannification dans la section `CI / CD`. -->

<!-- ## Bonus: Créez une branche spécifique et une plannification pour le rolling upgrade de notre application

- Modifiez `only: refs:` pour ajouter la branche `rolling_upgrade`.
- Modifier la commande ansible pour lancer le playbook d'upgrade.
- Dans `CI / CD > Schedules` ajoutez un job plannifié toute les 5 min (en production toute les nuits serait plus adapté).
- Observez le résultat. -->


# Explorer AWX

- Identifiez vous sur awx avec le login `admin` et le mot de passe précédemment configuré.

- Dans la section modèle de projet, importez votre projet. Un job d'import se lance. Si vous avez mis le fichier `requirements.yml` dans  `roles` les roles devraient être automatiquement installés.

- Dans la section crédentials, créez un crédential de type machine. Dans la section clé privée copiez le contenu du fichier `~/.ssh/id_ssh_tp` que nous avons configuré comme clé ssh de nos machines. Ajoutez également la passphrase que vous avez configuré au moment de la création de cette clé.

- Créez une ressource inventaire. Créez simplement l'inventaire avec un nom au départ. Une fois créé vous pouvez aller dans la section `source` et choisir de l'importer depuis le `projet`, sélectionnez `inventory.cfg` que nous avons configuré précédemment. Bien que nous utilisions AWX les ip n'ont pas changé car AWX est en local et peut donc se connecter au reste de notre infrastructure LXD.

- Pour tester tout cela vous pouvez lancez une tâche ad-hoc `ping` depuis la section inventaire en sélectionnant une machine et en cliquant sur le bouton `executer`.

- Allez dans la section modèle de job et créez un job en sélectionnant le playbook `site.yml`.

- Exécutez ensuite le job en cliquant sur la fusée. Vous vous retrouvez sur la page de job de AWX. La sortie ressemble à celle de la commande mais vous pouvez en plus explorer les taches exécutées en cliquant dessus.

- Modifiez votre job, dans la section `Plannifier` configurer l'exécution du playbook site.yml toutes les 15 minutes.

- Allez dans la section plannification. Puis visitez l'historique des Jobs.


