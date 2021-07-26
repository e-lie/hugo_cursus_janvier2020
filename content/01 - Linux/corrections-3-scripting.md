---
title: "Corrections exercices partie 3"
weight: 90
# pre: "<i class='fab fa-git'></i> - "
draft: false
---


# Partie 1 - les variables

```bash
#!/bin/bash

DISTRIB=$(cat /etc/os-release \
        | grep PRETTY_NAME \
        | awk -F= '{print $2}' \
        | tr -d '"')

NB_PROCESS=$(ps -ef --no-headers \
           | wc -l)

RAM_TOTALE=$(free -h | grep "^Mem" | awk '{print $2}')
RAM_DISPO=$(free -h | grep "^Mem" | awk '{print $7}')

UPTIME=$(uptime --pretty)
IP_LOCALE=$(ip a | grep "inet " | grep -v "127.0.0.1" | awk '{print $2}' | awk -F/ '{print $1}')

IP_GLOBALE=$(curl --silent ip.yunohost.org)

echo "La distribution est $DISTRIB"
echo "Il y a $NB_PROCESS process actuellement lancés"
echo "Il reste $RAM_DISPO RAM dispo sur $RAM_TOTALE total"
echo "La machine est up depuis $UPTIME"
echo "L'IP locale est $IP_LOCALE"
echo "L'IP globale est $IP_GLOBALE"

RED="\033[31m"
GREEN="\033[32m"
PURPLE="\033[35m"

echo -e "${RED}How ${GREEN}are ${PURPLE}you?"
```

# 2 - Paramétrabilité, interactivité

### 2.2 (add)

```
#!/bin/bash

RESULTAT=$(($1 + $2))

echo $RESULTAT
```

### 2.3 (age)

```bash
#!/bin/bash

CURRENT_YEAR=$(date +%Y)

echo -e "En quelle année est-tu né ? "
read YEAR
AGE=$(($CURRENT_YEAR-$YEAR))
echo "Tu as $AGE ans!"
```

### 2.4 (check_user)

```bash
#!/bin/bash

USER="$1"

HOME_USER=$(cat /etc/passwd | grep "^$USER:" | awk -F: '{print $6}')

ESPACE_DISQUE=$(du -hs $HOME_USER | cut -f1)

NB_PROCESS=$(ps au -u $USER --no-headers | wc -l)

NB_TERM=$(ps au -u $USER | grep bash | wc -l)

echo "L'utilisateur utiliser $ESPACE_DISQUE pour son home $HOME_USER"
echo "L'utilisateur a $NB_PROCESS processus en cours"
echo "    dont $NB_TERM terminaux bash"
```
