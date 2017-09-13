# Installation d'un serveur de fichier

## Introduction

Pour cette installation, nous allons configurer un serveur Debian pour qu'il puisse accueillir des volumes logiques. Nous allons utiliser LVM sur la partition principale (de démarrage).

## Caractéristiques de la machine

 - RAM: 512Mo

 - Disques :
    - 1 disque de 3Go
    - 1 disque de 1Go



## Installation

### Langages et pays
Choix d'installation:

 - Langage: English
 - Pays: other > Europe > France
 - Locales: en_US.UTF8
 - Clavier: Français

### Réseau

On choisit le nom de machine suivant: `srvfichier`

### Disques et partitionnement

#### Partitioning method

On va utiliser un LVM non chiffré: `Guided - use entire disk and set up LVM`

On choisit `sda` comme partition principale avec une partition `/home` séparée.


## Configuration de la machine

### Outils basiques

Installez `sudo` et configurez votre utilisateur

### Configuration réseau

Une fois la machine démarrée, vous allez pouvoir changer son adresse IP dans le serveur DHCP et lui ajouter un nom réseau (dans le DNS).

Vous allez utiliser l'adresse `172.16.81.10` avec son nom réseau `srvfichier`.

Relancer les services réseaux (`systemctl restart networking`)  ou redémarrez la machine.

## Utilisation de LVM

La [documentation Debian fournit des informations intéressantes](https://wiki.debian.org/fr/LVM) sur le fonctionnement de LVM.

Pour commencer, nous allons manipuler différents outils qui permettent de connaître l'état du système par rapport à ses partitions et disques.

### Quelques outils de gestion des volumes

#### df

Cet utilitaire très basique permet de connaître à la fois l'espace disque utilisé sur chaque partition, mais aussi des informations sur les points de montage:

```bash
$ df -h  # -h affiche les tailles en Ko, Mo, Go, c'est à dire de manière lisible pour des humains
Filesystem                       Size  Used Avail Use% Mounted on
udev                             226M     0  226M   0% /dev
tmpfs                             48M  3.1M   45M   7% /run
/dev/mapper/srvfichier--vg-root  1.4G  1.1G  231M  83% /
tmpfs                            238M     0  238M   0% /dev/shm
tmpfs                            5.0M     0  5.0M   0% /run/lock
tmpfs                            238M     0  238M   0% /sys/fs/cgroup
/dev/mapper/srvfichier--vg-home  859M  1.8M  796M   1% /home
/dev/sda1                        236M   37M  188M  17% /boot
tmpfs                             48M     0   48M   0% /run/user/1000
```

Commenter et étudier cette sortie. Quels informations peut-on en déduire ? Notez-les sur un papier ou un bloc-note.

#### mount

La commande `mount` permet de visualiser (et monter/démonter avec certaines options) les volumes montés et leurs caractéristiques.

Lancer cette commande sur votre serveur et observez la sortie. Faites le parallèle avec les informations données par `df`.

#### fdisk

**ATTENTION**: cette commande permet de modifier les partitions du disque. Elle est donc à utiliser avec précaution. En particulier, sachez que la commande interne `w` écrira sur la table des partitions les modifications que vous auriez pu faire. Mieux vaut donc éviter de l'utiliser tant que l'on n'est pas certain de ce que l'on fait.

Taper la commande: `sudo fdisk /dev/sda`

Utilisez les commandes suivantes (dans le désordre):

La commande `m` permet d'afficher une aide sur l'utilisation des commandes internes de `fdisk`.

La commande `q` permet de quitter sans modifier la table des partitions.

La commande `p` permet d'afficher les partitions sur le disque `/dev/sda` :

Dans l'exemple ci-dessous, on voit que trois partitions sont présentes :

```fdisk
Command (m for help): p
Disk /dev/sda: 3 GiB, 3221225472 bytes, 6291456 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x1df1dd29

Device     Boot  Start     End Sectors  Size Id Type
/dev/sda1  *      2048  499711  497664  243M 83 Linux
/dev/sda2       501758 6289407 5787650  2.8G  5 Extended
/dev/sda5       501760 6289407 5787648  2.8G 8e Linux LVM
```

C'est la partition `sda5` qui contient les volumes LVM.



Un second disque étant monté sur cette machine virtuelle, nous allons maintenant le partitionner et y installer LVM

### Les volumes LVM

Vous avez pu observer que nous avons deux volumes LVM actuellement montés:

 - `/dev/mapper/srvfichier--vg-root` monté sur `/`
 - `/dev/mapper/srvfichier--vg-home` monté sur `/home`

Cela correspond aux options choisies pendant l'installation du serveur.

#### Création d'un volume LVM

Nous allons maintenant créer un nouveau volume LVM sur le second disque (1Go) de notre serveur: `/dev/sdb`

##### Initialisation de la partition physique

Pour cela, nous devons y créer une partition de 200Mo (suffisant pour notre démonstration) avec `fdisk`. On appellera les commandes suivantes:
   - `n` pour créer une nouvelle partition (on rentre successivement le type, le numéro, le premier secteur et la taille de la partition comme dans l'exemple ci-dessous)
   - `t` pour positionner le type de partition sur `Linux LVM`
   - `p` pour afficher la liste des partitions sur le disque actuel (et vérifier les opérations précédentes)
   - `w` pour écrire les modifications

Reproduire ce qui est indiqué ci-dessous:

```bash
$ sudo fdisk /dev/sdb

Welcome to fdisk (util-linux 2.29.2).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1):
First sector (2048-2097151, default 2048):
Last sector, +sectors or +size{K,M,G,T,P} (2048-2097151, default 2097151): +200M

Created a new partition 1 of type 'Linux' and of size 100 MiB.

Command (m for help): t
Selected partition 1
Partition type (type L to list all types): L

 0  Empty           24  NEC DOS         81  Minix / old Lin bf  Solaris
 1  FAT12           27  Hidden NTFS Win 82  Linux swap / So c1  DRDOS/sec (FAT-
 2  XENIX root      39  Plan 9          83  Linux           c4  DRDOS/sec (FAT-
 3  XENIX usr       3c  PartitionMagic  84  OS/2 hidden or  c6  DRDOS/sec (FAT-
 4  FAT16 <32M      40  Venix 80286     85  Linux extended  c7  Syrinx
 5  Extended        41  PPC PReP Boot   86  NTFS volume set da  Non-FS data
 6  FAT16           42  SFS             87  NTFS volume set db  CP/M / CTOS / .
 7  HPFS/NTFS/exFAT 4d  QNX4.x          88  Linux plaintext de  Dell Utility
 8  AIX             4e  QNX4.x 2nd part 8e  Linux LVM       df  BootIt
 9  AIX bootable    4f  QNX4.x 3rd part 93  Amoeba          e1  DOS access
 a  OS/2 Boot Manag 50  OnTrack DM      94  Amoeba BBT      e3  DOS R/O
 b  W95 FAT32       51  OnTrack DM6 Aux 9f  BSD/OS          e4  SpeedStor
 c  W95 FAT32 (LBA) 52  CP/M            a0  IBM Thinkpad hi ea  Rufus alignment
 e  W95 FAT16 (LBA) 53  OnTrack DM6 Aux a5  FreeBSD         eb  BeOS fs
 f  W95 Ext'd (LBA) 54  OnTrackDM6      a6  OpenBSD         ee  GPT
10  OPUS            55  EZ-Drive        a7  NeXTSTEP        ef  EFI (FAT-12/16/
11  Hidden FAT12    56  Golden Bow      a8  Darwin UFS      f0  Linux/PA-RISC b
12  Compaq diagnost 5c  Priam Edisk     a9  NetBSD          f1  SpeedStor
14  Hidden FAT16 <3 61  SpeedStor       ab  Darwin boot     f4  SpeedStor
16  Hidden FAT16    63  GNU HURD or Sys af  HFS / HFS+      f2  DOS secondary
17  Hidden HPFS/NTF 64  Novell Netware  b7  BSDI fs         fb  VMware VMFS
18  AST SmartSleep  65  Novell Netware  b8  BSDI swap       fc  VMware VMKCORE
1b  Hidden W95 FAT3 70  DiskSecure Mult bb  Boot Wizard hid fd  Linux raid auto
1c  Hidden W95 FAT3 75  PC/IX           bc  Acronis FAT32 L fe  LANstep
1e  Hidden W95 FAT1 80  Old Minix       be  Solaris boot    ff  BBT
Partition type (type L to list all types): 8e
Changed type of partition 'Linux' to 'Linux LVM'.

Command (m for help): p
Disk /dev/sdb: 1 GiB, 1073741824 bytes, 2097152 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x134ded63

Device     Boot Start    End Sectors  Size Id Type
/dev/sdb1        2048 206847  204800  200M 8e Linux LVM

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.

```

Une fois cette partition créée, elle reste invisible à moins de redémarrer la machine ou d'utiliser l'outil `partprobe` (qui est dans le package `parted`).

```bash
$ ls -l /dev/sdb*
ls: cannot access '/dev/sdb*': No such file or directory

$ sudo partprobe

$ ls -l /dev/sdb*
brw-rw---- 1 root disk 8, 16 Sep 12 22:57 /dev/sdb
brw-rw---- 1 root disk 8, 17 Sep 12 22:57 /dev/sdb1
```

##### Initialisation de la nouvelle partition pour LVM

Pour permettre à la partition d'être gérée par LVM, on l'initialise avec la commande suivante.

```bash
$ sudo pvcreate /dev/sdb1
WARNING: dos signature detected on /dev/sdb1 at offset 510. Wipe it? [y/n]: y
  Wiping dos signature on /dev/sdb1.
  Physical volume "/dev/sdb1" successfully created.
```


##### Création d'un `volume group`

```bash
$ sudo vgcreate fichiers-vg /dev/sdb1
  Volume group "fichiers-vg" successfully created
$ sudo vgdisplay -v fichiers-vg
  --- Volume group ---
  VG Name               fichiers-vg
  System ID
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  1
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                0
  Open LV               0
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               196.00 MiB
  PE Size               4.00 MiB
  Total PE              49
  Alloc PE / Size       0 / 0
  Free  PE / Size       49 / 196.00 MiB
  VG UUID               TfLQcT-j6sS-o4pI-qVqt-nRwp-nQnb-0suO9s

  --- Physical volumes ---
  PV Name               /dev/sdb1
  PV UUID               0YFAUO-hHXM-pEAs-ZZZj-ofoq-fe99-GoppH1
  PV Status             allocatable
  Total PE / Free PE    49 / 49

```


##### Création du volume logique

On utilise la taille indiquée par la commande précédente (VG Size: 196Mo). On remarque que cette taille est le nombre de PE multiplié par la taille des PE.

Le PE (physical extent) est l'unité la plus petite de notre VG.

```bash
sudo lvcreate -L196 -n lv-fichiers fichiers-vg
```

Ce volume logique est désormais visible dans le répertoire `/dev`. Il est plus simple de visualiser le lien symbolique qui pointe vers lui grâce à la commande `ls -l /dev/mapper`

Que constatez-vous ?

##### Système de fichier

Nous allons maintenant créer le système de fichier sur le volume nouvellement créé. Pour cela, nous allons utiliser la commande `mkfs.<FS>`  où <FS> est remplacé par un nom de système de fichier.

Par exemple, chercher l'emplacement de la commande `mkfs.ext2` avec la commande suivante :

```bash
$ sudo which mkfs.ext2
/sbin/mkfs.ext2
```

Afficher toutes les commandes commençant par `mkfs.` et situées dans le même répertoire.

Quels sont les systèmes de fichiers que vous pouvez créer ?

Faites une recherche pour déterminer la différence entre ceux-ci.

Une fois cela fait, choisissez en un (en validant votre choix auprès du formateur) et lancez le formatage.

Par exemple pour du EXT2:

```bash
sudo mkfs.ext2 /dev/mapper/fichiers--vg-lv--fichiers
```

Reste maintenant à monter cette partition. Nous allons d'abord le faire manuellement:

```bash
sudo mkdir /mnt/fichiers
sudo mount /dev/mapper/fichiers--vg-lv--fichiers /mnt/fichiers
```

Il ne reste plus qu'à constater que notre volume est bien monté grâce à la commande `df` (avec les options appropriées pour vérifier la taille des volumes).

Maintenant vous pouvez redémarrer votre serveur de fichiers puis relancer cette commande. Que constatez-vous ?

### Montage permanent d'un volume

Pour que le volume soit monté au démarrage, il faut qu'il soit déclaré dans la table des systèmes de fichiers. Il s'agit d'un fichier nommé `/etc/fstab`

Etudiez le manuel de `fstab` et observez le fichier `/etc/fstab` existant sur votre système et (**après en avoir fait une sauvegarde**) modifiez-le pour ajouter le LVM précédemment créé sur le point de montage `/srv/fichiers`.

Utilisez la commande `mount -a` pour vérifier qu'il n'y a pas d'erreur. Faites valider les modifications par le formateur, puis redémarrez votre machine.

Ce volume va pouvoir servir pour votre serveur de fichier sous Samba.

### Extension des volumes

Le véritable intérêt de LVM est de pouvoir ajouter de l'espace disque sans avoir à repartitionner: il suffit d'ajouter des volumes (partitions, disques) au LV pour que sa taille augmente.

Imaginez un serveur de fichiers dans lequel l'espace disque vient à manquer: l'ajout de disques durs permettra d'augmenter facilement le LV présent.

#### Création d'une nouvelle partition

On va maintenant ajouter une nouvelle partition sur le disque déjà installé sur la VM:

```bash
$ sudo fdisk /dev/sdb

Welcome to fdisk (util-linux 2.29.2).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): p
Disk /dev/sdb: 1 GiB, 1073741824 bytes, 2097152 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x134ded63

Device     Boot Start    End Sectors  Size Id Type
/dev/sdb1        2048 411647  409600  200M 8e Linux LVM

Command (m for help): n
Partition type
   p   primary (1 primary, 0 extended, 3 free)
   e   extended (container for logical partitions)
Select (default p): e
Partition number (2-4, default 2):
First sector (411648-2097151, default 411648):
Last sector, +sectors or +size{K,M,G,T,P} (411648-2097151, default 2097151):

Created a new partition 2 of type 'Extended' and of size 823 MiB.

Command (m for help): n
All space for primary partitions is in use.
Adding logical partition 5
First sector (413696-2097151, default 413696):
Last sector, +sectors or +size{K,M,G,T,P} (413696-2097151, default 2097151): +100M

Created a new partition 5 of type 'Linux' and of size 100 MiB.

Command (m for help): t
Partition number (1,2,5, default 5): 5
Partition type (type L to list all types): 8e

Changed type of partition 'Linux' to 'Linux LVM'.

Command (m for help): p
Disk /dev/sdb: 1 GiB, 1073741824 bytes, 2097152 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x134ded63

Device     Boot  Start     End Sectors  Size Id Type
/dev/sdb1         2048  411647  409600  200M 8e Linux LVM
/dev/sdb2       411648 2097151 1685504  823M  5 Extended
/dev/sdb5       413696  618495  204800  100M 8e Linux LVM

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Re-reading the partition table failed.: Device or resource busy

The kernel still uses the old table. The new table will be used at the next reboot or after you run partprobe(8) or kpartx(8).

$ sudo partprobe
$ ll /dev/sdb*
brw-rw---- 1 root disk 8, 16 Sep 13 00:10 /dev/sdb
brw-rw---- 1 root disk 8, 17 Sep 13 00:10 /dev/sdb1
brw-rw---- 1 root disk 8, 18 Sep 13 00:10 /dev/sdb2
brw-rw---- 1 root disk 8, 21 Sep 13 00:10 /dev/sdb5
```

On va utiliser cette partition `sdb5`, pour l'ajouter sur le VG précédemment créé

```bash
$ sudo pvcreate /dev/sdb5
  Physical volume "/dev/sdb5" successfully created.

```

On va maintenant observer les effets de la commande `vgextend`.

Notez les valeurs données par la commande suivante:

```bash
$ sudo vgdisplay fichiers-vg
```

Augmentez la taille du Volume Group:

```bash
$ sudo vgextend fichiers-vg /dev/sdb5
  Volume group "fichiers-vg" successfully extended
```

Comparez avec la nouvelle taille:
```bash
$ sudo vgdisplay fichiers-vg
```

Que constatez-vous ? Est-ce logique ?

#### Augmentation de la taille du volume

Ce qui suit peut se faire sans démonter le volume. Cependant, ce type d'opération sera plus sûre si personne n'effectue d'opération pendant son exécution.

On préférera donc démonter le volume:

```bash
sudo umount /srv/fichiers
```

Puis exécuter les commandes suivantes:

```bash
$ sudo lvextend -L+96M /dev/mapper/fichiers--vg-lv--fichiers
  Size of logical volume fichiers-vg/lv-fichiers changed from 196.00 MiB (49 extents) to 292.00 MiB (73 extents).
  Logical volume fichiers-vg/lv-fichiers successfully resized.
```

On ajuste la taille du système de fichiers:

```bash
sudo resize2fs /dev/mapper/fichiers--vg-lv--fichiers
```

On remonte et on vérifie la taille du nouveau volume.

Que constatez-vous ?

A ce stade vous devriez avoir un volume d'environ 280Mo monté sur `/srv/fichiers`


### Exercice

Ajoutez 200Mo supplémentaires sur le volume logique `fichiers--vg-lv--fichiers`


### Le meilleur pour la fin

Un outil de configuration graphique de gestion pour LVM existe. Il est dans le paquet `system-config-lvm`


Cependant, il n'est utilisable que par root et il va donc falloir configurer X11 pour transférer le display à root...

Pour cela, vous avez ce script:

```bash
#!/bin/bash
#shebang

#première méthode
#Je récupère le cookie (qui est stocké dans mon .Xauthority dans mon HOME)
#"mon" == pour l'utilisateur courant
cookie=`xauth list |grep :$(echo $DISPLAY | cut -d : -f 2 | cut -d . -f 1) `
#Le guillement inversé ` c'est altgr-7
echo $cookie

#J'exécute en tant que root la commande suivante:
#sudo xauth add $cookie #Ne marche pas dans certains cas (Trisquel)
sudo su <<EOF
xauth add $cookie
EOF

```

Ce script doit être sauvé dans un fichier `xforward2root`. Le fichier doit être rendu exécutable grâce à la commande `chmod +x`.

Il pourra être exécuté en lançant la commande `./xforward2root`
