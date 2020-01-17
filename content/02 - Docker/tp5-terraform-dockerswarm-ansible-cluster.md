---
title: 'TP1 Ansible - déployer un cluster swarm digital ocean avec terraform et ansible'
draft: true
---

## Installer terraform

Terraform n'est pas disponible sous forme de dépôt ubuntu/debian. Pour l'installer il faut le télécharger et le copier manuellement:

```bash
cp /tmp
wget https://releases.hashicorp.com/terraform/0.12.6/terraform_0.12.6_linux_amd64.zip
sudo unzip ./terraform_0.12.6_linux_amd64.zip -d /usr/local/bin/
```

- Testez l'installation avec `terraform --version`

## Cloner le projet modèle

- Dans ce projet nous allons générer l'arborescence suivante:

```bash
tp1_terraform_swarm
├── ansible_roles
│   ├── docker-engine
│   │   ├── defaults
│   │   │   └── main.yml
│   │   └── tasks
│   │       └── main.yml
│   └── thomasjpfan.docker-swarm
│       ├── ...
└── terraform_infra
    ├── ansible.cfg
    ├── install_swarm.yml
    ├── main.tf
    ├── terraform-inventory.py
    ├── terraform.tfstate
    └── terraform.tfvars
```

- Pour commencer, clonez le dépôt de base à l'adresse [https://gitlab.com/e-lie/tp1_terraform_swarm](https://gitlab.com/e-lie/tp1_terraform_swarm).

- Ouvrez le projet `tp1_terraform_swarm` avec VSCode.


## Terraform digital ocean

Terraform est un outils pour décrire une infrastructure de machines virtuelles et ressources IaaS (infrastructure as a service) et les créer (commander). Il s'intègre en particulier avec AWS, DigitalOcean mais peut également créer des machines dans un cluster VMWare en interne (on premise) pour créer un cloud mixte.

- Le fichier qui décrit les VPS et ressources à créer est `main.tf`. Nous allons complétez ce fichier:
  
- le provider de cloud à utiliser il s'agit pour nous de DigitalOcean:

```terraform
provider "digitalocean" {
    token = "${var.do_api_token}"
}
```

- Cherchez et visitez la documentation de terraform pour digital ocean.
  

- Nous pouvons maintenant configurer les VPS à créer. Complétez le nom de l'image linux à utiliser pour créer le VPS (droplet): `ubuntu-18-04-x64` (slug très pénible à trouver).


- Enfin précisons le nombre de noeuds manager et worker que nous voulons créez: mettez 1 de chaque pour le moment.


- Passons maintenant à `terraform.tfvars`:
  - copiez le fichier `terraform.tfvars.dist` et renommez le en enlevant le `.dist`
  - rendez vous sur votre compte digital ocean pour créer un token et copiez immédiatement le hash du token.
  - collez le token dans le fichier de variables `terraform.tfvars`
  - ajoutez également votre clé ssh à votre compte DigitalOcean et ajoutez sa signature au fichier précédent.

- Maintenant que ces deux fichiers sont complétés (quoi créer et comment s'identifier auprès du provider) nous pouvons lancer la création de nos VPS:
  - `terraform init` permet à terraform de télécharger les "driver" nécessaire pour s'interfacer avec notre provider. Cette commande crée un dossier .terraform
  - `terraform plan` est facultative et permet de calculer et récapituler les créations modifications de ressources à partir de la description de `main.tf`
  - `terraform apply` permet de déclencher la création des ressources.

- La création prend quelques secondes (max 1 minute ?). Vous pouvez ensuite allez voir l'interface de digital ocean pour constater la création des VPS.


## Terraform provider ansible

Pour pouvoir se connecter à nos VPS, ansible doit connaître les adresses IP et le mode de connexion ssh de chaque VPS. Il a donc besoin d'un inventaire.

Jusqu'ici nous avons créé un inventaire statique c'est à dire un fichier qui contenait la liste des machines. Nous allons maintenant utiliser un inventaire dynamique c'est à dire un programme qui permet de récupérer dynamiquement la liste des machines et leurs adresses en contactant une API.

- Le meilleur inventaire dynamique pour terraform est [https://github.com/nbering/terraform-provider-ansible/](https://github.com/nbering/terraform-provider-ansible/). Suivez les instructions d'installation.

- Téléchargez également ce script qui servira effectivement d'inventaire: `wget https://raw.githubusercontent.com/nbering/terraform-inventory/master/terraform.py`

[https://raw.githubusercontent.com/nbering/terraform-inventory/master/terraform.py](https://raw.githubusercontent.com/nbering/terraform-inventory/master/terraform.py)

Une bonne intégration entre Ansible et Terraform permet de décrire précisément les liens entre resource terraform et hote ansible ainsi que les groupes de machines ansible. Pour cela notre binder propose de dupliquer les ressources dans `main.tf` pour créer explicitement les hotes ansible à partir des données dynamiques de terraform.

- Complétez le `main.tf` avec le code suivant:

```terraform
## Ansible mirroring hosts section
# Using https://github.com/nbering/terraform-provider-ansible/ to be installed manually (third party provider)
# Copy binary to ~/.terraform.d/plugins/

resource "ansible_host" "ansible_managers" {
  count = "${local.swarm_manager_count}"
  inventory_hostname = "manager_${count.index}"
  groups = ["swarm_nodes"]
  vars = {
    ansible_host = "${element(digitalocean_droplet.managers.*.ipv4_address, count.index)}"
  }
}

resource "ansible_host" "ansible_workers" {
  count = "${local.swarm_worker_count}"
  inventory_hostname = "worker_${count.index}"
  groups = ["swarm_nodes"]
  vars = {
    ansible_host = "${element(digitalocean_droplet.workers.*.ipv4_address, count.index)}"
  }
}

resource "ansible_group" "all" {
  inventory_group_name = "all"
  vars = {
    ansible_python_interpreter = "/usr/bin/python3"
  }
}
```

- Complétez le `ansible.cfg` avec:
  - le chemin de l'inventaire dynamique précédemment installé
  - le chemin du dossier de roles ansible de notre projet. Créez le cas échéant (`ansible_roles`)


- Créer les nouvelles ressources virtuelles ansibles avec `terraform apply`


- Testez l'inventaire dynamique :  `chmod +x terraform.py && ./terraform.py`. Vous devriez avoir du texte JSON en retour de ce programme. Interprétons le : les différentes machines avec leurs ip et groupes au format standard inventaire json ansible.

- Nous pouvons maintenant tester la connexion avec ansible directement: `ansible swarm_nodes -m ping`. Il s'agit d'une commande ad-hoc. Ces commandes sont très pratiques pour.


## Installer des programmes avec des commandes ad-hoc

- Lancez la commande bash `systemctl status ssh` à l'aide d'une commande ad-hoc du type `ansible <groupe> -m <module> -a <arguments>`. Le groupe est le même que le précédent, le module est `shell` et l'argument est la commande bash à lancer.

- Installez `htop` sur les deux noeuds en même temps avec une commande ad-hoc ansible:
  - Utilisez le module `apt`.
  - Utilisez comme argument `name=<nom_paquet>`.


## Installer docker avec un playbook ansible

- En suivant la documentation docker [suivante](https://docs.docker.com/install/linux/docker-ce/ubuntu/) et en vous inspirant du TP elasticseach ansible créez un playbook ansible qui installe `docker` et `docker-compose` sur tous nos noeuds Ubuntu (bionic) 

## Transformons notre playbook en role

Les roles sont comme les modules `puppet` il s'agit de sorte de **librairies** de haut niveau qui décrivent comment installer et maintenir des applications. Ex: un role pour installer directement `wordpress` ou `docker swarm` sans avoir besoin de coder tout le playbook correspondant.

- Créez un dossier `docker-engine` dans `ansible_roles`:
- A l'intérieur ajoutez un dossier `tasks` et un fichier `main.yml` dans `tasks`.
- Dans ce fichier copiez les tâches de votre playbook précédent (la liste de tiret sous `tasks:` mais sans le `tasks:`)
- testez votre role en créant le playbook `install_role_docker.yml` suivant dans `terraform_infra`:

```yml
- name: ""
  hosts: swarm_nodes

  roles:
    - role: docker-engine
```

- Créons une variable pour personnaliser notre role:
  - remplacez toutes les occurrences de `bionic` par `{{ docker_ubuntu_version }}` dans le fichier `tasks/main.yml`.
  - Ajoutez un dossier `defaults` dans le dossier du role (pas dans `tasks`) qui sert à décrire les valeurs par défaut des variables.
  - Ajoutez à l'intérieur un fichier `main.yml` et dans ce fichier une ligne `docker_ubuntu_version: bionic`.

- Relancez le role.


## Installer un role avec ansible-galaxy

- Visitez le dépôt officiel de role ansible nommé **Galaxy** [https://galaxy.ansible.com/](https://galaxy.ansible.com/)

- Pour installer facilement des roles depuis ce dépôt utilisez la commande: `ansible-galaxy install <nom_role>`

- Téléchargez le role `thomasjpfan.docker-swarm`

- Avant de l'exécuter il faut compléter les groupes pour désigner correctement les workers et les managers dans `main.tf` : démo et explication.

- Ajoutez une ligne à `install_docker_role.yml` et lancer ce playbook pour installer docker swarm sur notre cluster.

## Déployer notre stack Jenkins

- Connectez vous en ssh au manager.
- Installez portainer avec la commande du TP précédent pour déployer ce service (à la fin de la page)
- clonez le dépôt de correction [https://framagit.org/e-lie/jenkins_as_code_tp](https://framagit.org/e-lie/jenkins_as_code_tp) du TP précédent et déployez cette stack avec `docker stack deploy -c <file.yml> <stack_name>`
- Constatez le changement dans portainer
- Testez Jenkins.

## Redimensionner notre cluster dynamiquement !!!

- Modifiez `main.tf` pour passer le nombre de worker à 4.
- Appliquer la configuration avec `terraform apply`.
- Constatez sur Digital Ocean que les noeuds ont bien été créé
- Pinguer vos noeud avec `ansible swarm_nodes -m ping`
- Relancez le playbook `install_docker_role.yml`.
- Allez dans `portainer` et scalez le service `jenkins-slave` pour le passer à 12 replicat
- Allez dans le menu `swarm > Go to cluster visualizer` pour voir le résultat.

## Ajouter des containers applicatifs avec ansible

- Cherchez la documentation du module ansible `docker`.
- Utilisez le en mode ad-hoc pour lancer des conteneurs sur votre swarm.
- Ajoutez un playbook qui installe elasticsearch et kibana dans notre cluster grace à ce module.

## Correction

Vous trouverez la correction de ce TP dans le dépôt initial en checkoutant le commit `git checkout tp1_ansible_ready`.

## NE PAS OUBLIER DE DETRUIRE LES VPS POUR NE PAS PAYER

Pour ne pas payer des serveurs inutiles il faut absolument penser à détruire les droplets evac `terraform destroy`. Puis vérifiez dans Digital Ocean qu'il n'y a plus de droplets.

## SUPPRIMER LE TOKEN DIGITAL OCEAN POUR EVITER LES MAUVAISES SURPRISES

Un token d'API est une identification temporaire qui sert justement à être révoquée quand elle ne sert plus

- Allez dans API > tokens sur l'interface de digital ocean et supprimez le token du TP.

