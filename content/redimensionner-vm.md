---
title: "Redimensionner le disque d'une machine virtualbox"
---

- Bien éteindre la machine.
- Sur le système hôte (Windows ou Linux):
    - Téléchargez `gparted` : [lien sourceforge](https://sourceforge.net/projects/gparted/files/gparted-live-stable/0.33.0-2/gparted-live-0.33.0-2-amd64.iso/download)

- Pour redimensionner le disque sur Windows:
    - Ouvrir une invite de commande.
    - Visitez `C:\Users\<votre_user>\Virtualbox VMs\<votre_machine>\`
```bash
# la première ligne est utile seulement si le disque est au format vdmk
"C:\Program Files\Oracle\VirtualBox\VBoxManage" clonemedium "<votre_disque>.vmdk" "<votre_disque>.vdi" --format vdi
"C:\Program Files\Oracle\VirtualBox\VBoxManage" modifymedium "cloned.vdi" --resize 20480
```

- Pour redimensionner le disque sur linux:
    - Dans un terminal, visitez `"~/Virtualbox VMs"`, entrez dans le dossier de la machine en question.
```bash
# la première ligne est utile seulement si le disque est au format vdmk
VBoxManage clonemedium "<votre_disque>.vmdk" "<votre_disque>.vdi" --format vdi
VBoxManage modifymedium "cloned.vdi" --resize 20480 # 20Gio par exemple.
```

- Allez dans la configuration de la machine, déconnectez le disque VMDK et connectez le nouveau disque VDI.

- Ajoutez le CD gparted à la machine.

- Lancez la machine.

- Gparted demarre. choisissez le type de clavier (fr) puis le lancement avec serveur X (option 0).

- Une fenêtre avec votre disque s'affiche. Cliquez sur le disque dans la liste puis "redimensionner/déplacer"

- Dans la fenêtre suivante, agrandissez à la souris la partition pour occuper tout l'espace disponible.

- Faites `ok` puis `appliquer`.

