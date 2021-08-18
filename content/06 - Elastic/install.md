Installation d'un cluster Elastic avec Ansible

## Mettre en place son cluster

On va créer trois machine virtuelles pour notre cluster ça prend 3Gb de
RAM. SI votre machine à 8GB en tout il ne faut pas ouvrir trop d'onglets
navigateur sous peine de sévère ramage ;)

1.  Importer deux machines elk_node (1GB) (appelez les node1 et node2)
2.  Importer la machine ubuntu graphique (1GB) (master) (idéalement
    setup 2GB et 2 processeurs)
3.  Créer un « Réseau Nat » :\
    Dans virtualbox : Fichier \> Paramètre \> Réseau \> ajouter un
    réseau NAT
4.  Éditer les réseaux des trois machines pour les mettre dans le réseau
    Nat précédement créé
5.  Allumer les trois machines.
6.  Dans chacune, trouver un terminal/tty et lancez \`**ip a**\` ou
    \`**ifconfig**\`
7.  Relevez les adresses IP des trois machines :

-   node 1
-   node 2
-   master

1.  Vérifiez que vous pouvez pinguer les autres machines du cluster

2.  Essayez de pinguer depuis la machine hôte (ça ne marche pas car les
    machines sont « cachées » dans le NAT )

## Bilan architecture réseau de notre cluster

\- NAT network address translation : 127.0.0.1 « cache » les trois
machines

\- Pour aller jusqu'au bout on peut ajouter une redirection de port pour
le master (dans les paramètres du réseau NatNetwork)

\- ssh -p 3022 elk-master\@127.0.0.1

## Configurer et tester Ansible

1.  Dans le master installez **ansible, git, curl, ssh** et **sshpass**
    (Sur ubuntu c'est comme sous mint ou debian/yunohost : apt install
    \<paquet\>)

2.  Récupérer mon modèle ansible grace à git:\
    git clone <http://github.com/e-lie/ansible-tpl-elk-forma>

3.  Observons les fichers présents :

    1.  **ansible.cfg** // config de base d'ansible
    2.  **hosts** // fichier qui liste les machines du cluster : à
        compléter
    3.  **ping.yml //** playbook (sorte de script) ansible pour tester
        l'infra
    4.  **setup_ansible.yml** // à remplir avec les étapes
        d'installations

4.  Compléter le fichier **hosts avec les ips**

5.  **Remarquez que ping.yml** est déjà complété avec les groupes
    **elk**\_**nodes**, **kibana**\_**node.**

6.  Lancez : **ansible-playbook ping.yml → **échec il manque python

7.  Ajouter une commande (task) ansible au dessus de ping pour
    activer/lier python :\
    - *raw: \'sudo ln -fs /usr/bin/python3 /usr/bin/python\'*

8.  Relancez Ping. Si vous n'avez pas d'erreur c'est que ansible est
    prêt pour la configuration des machines.

## Installer Elasticsearch

1.  Connectez vous en ssh depuis le master dans l'un des nœuds elastic
    et installez : **apt-transport-https uuid-runtime
    openjdk-11-jre-headless gpg**
2.   Lancez **ansible-playbook setup\_elastic.yml** avoir pris soin de
    vérifier que hosts visait le groupe **elk_nodes**. Les requirements
    sont installé ! Voyez les ok et changed apparaissant lorsque vous
    lancez le playbook : ansible est verbeux il informe de sa réussite.
3.   Ajoutez à la suite ces trois commandes (tasks) Ansible (qui vont
    servir à installer elasticsearch sur chacun des nodes) :

\- name: Add elasticsearch repo GPG key

apt_key:

url: \<url\>

state: \<state\>

\- name: Add elasticsearch apt repo

apt_repository:

repo: \<repo debian\>

state: \<state\>

\- name: Install Elasticsearch *\# requires java preinstalled*

apt: pkg=elasticsearch state=\<state\>

1.   Complétez ces commande à l'aide des paramètres ci-dessous

\- url de la clé GPG des dev de elastic :
https://artifacts.elastic.co/GPG-KEY-elasticsearch

répertoire debian pour elastic : \"deb
https://artifacts.elastic.co/packages/6.x/apt stable main\"

1.   Chercher sur internet quoi mettre à la place de \<state\>. Par
    exemple en tapant : ansible doc apt module → il faut mettre
    **present**. C'est l'état qui signifie que ansible va s'assurer de
    la présence des paquets/repos et les ajouter si et seulement si ils
    sont manquant.
2.   Relancez le playbook !

## Bilan Ansible :

-   Ansible peut être rejoué plusieur fois (il est idempotent)
-   Ansible garanti l'état de certains éléments du système lorsqu'on le
    (re)joue
-   Ansible est (dès qu'on est un peu habitué) plus limpide que du bash

## Configurer Elastic en cluster

1.   Il manque la configuration ! Ajoutez une nouvelle commande pour
    créer le fichier de configuration :

\- name: Configure Elasticsearch.

template:

src: \<template\>

dest: /etc/elasticsearch/elasticsearch.yml

owner: root

group: elasticsearch

mode: 0740

notify: restart elasticsearch

1.  Observez le fichier template/elasticsearch.yml.j2 : c'est modèle de
    fichier de configuration. Il contient des trous ** {{ var }} **qui
    doivent être remplis par les variables du playbook
2.  ** **Ajoutez les variables suivantes avant les tasks (attention aux
    alignement! Vars doit être aligné avec **tasks:**) :

**vars:**

\- elasticsearch_cluster_name: elk_formation

\- elasticsearch_network_host: \"0.0.0.0\"

\- elasticsearch_http_port: 9200

\- elk_node_ips:

- 10.0.2.4

- 10.0.2.5\
pensez à changer les ips pour désigner vos nœuds elastic

1.   Ajoutez la fin suivante au playbook :

\- name: Start Elasticsearch.

systemd:

name: elasticsearch

state: restarted

enabled: yes

daemon_reload: yes *\#required before first run*

\- name: Make sure Elasticsearch is running before proceeding.

wait_for: host={{ elasticsearch_network_host }} port={{
elasticsearch_http_port }} delay=3 timeout=300

handlers:

\- name: restart elasticsearch

service: name=elasticsearch state=restarted

(attention aux alignements : handlers est au même niveau que tasks)

1.   Jouer le playbook enfin complet.
2.   Lancez les commandes de diagnostic

\- curl
[http://](http://10.0.2.4:9200/_cat/nodes?pretty)[10.0.2.4](http://10.0.2.4:9200/_cat/nodes?pretty)[:9200/\_cat/nodes?pretty](http://10.0.2.4:9200/_cat/nodes?pretty)

\- curl -XGET
\'[http://](http://10.0.2.4:9200/_cluster/state?pretty)[10.0.2.4](http://10.0.2.4:9200/_cluster/state?pretty)[:9200/\_cluster/state?pretty](http://10.0.2.4:9200/_cluster/state?pretty)\'

Si tout est bien configuré vous devriez voir une liste de deux nœuds
signifiant que les deux elastic se « connaissent »

30\. Pour ajouter un nouveau nœud !

\- importer une nouvelle machine

\- l'ajouter au fichier hosts

\- ajoutez les ips dans vars

\- relancer le playbook **\#magic**

## Installer kibana

\- Remplacez : \<elastic_node_ip\> par l'ip d'un des nœuds elastic

\- Vérifier que le groupe kibana_node dans hosts pointe bien vers
elk-master

\- Lancer : **ansible-playbook setup_kibana.yml**

**- **Accéder à localhost:5601 dans firefox :D
