# Partage de fichiers NFS

## Introduction

NFS (Network File System) permet de partager des dossiers vers d'autres serveurs Linux. Ces clients vont pouvoir monter simplement le dossier partagé dans leur système de fichiers.

## Installation et configuration de NFS sur le serveur

On va installer NFS sur le serveur de fichiers installé précédemment.

### Installation des paquets

```bash
$ sudo apt-get install nfs-kernel-server nfs-common portmap
```

### Configuration

Le fichier `/etc/exports` contient les dossiers à partager.

Il suffit simplement d'ajouter à partager dans ce fichier:

```conf
/srv/fichiers *(rw,sync,no_subtree_check)
```

Explication:

 - Le premier champs indique l'emplacement du dossier sur le système actuel
 - L'option `*` indique que toutes les IP sans exceptions pourront se connecter. À la place de ce champs, on peut aussi filtrer en indiquant une IP (172.16.81.5) ou un masque de sous-réseau (172.16.81.5/24)
 - l'option `rw` indique que le partage autorisera les accès en écriture (on peut utiliser `ro` pour le laisser en lecture seule)

Utiliser la commande `man exports` pour avoir des informations sur les autres éléments.

Voir aussi [cette page](https://wiki.debian-fr.xyz/Partage_NFS) pour plus d'explications.


### Chargement de la configuration

On redémarre le service:

```bash
sudo systemctl restart nfs-kernel-server
sudo systemctl status nfs-kernel-server
```

## Installation et configuration sur le client

Reprendre la machine utilisée pour monter un serveur FTP.

### Installation

Seul le paquet `nfs-common` est nécessaire

```bash
sudo apt-get update && sudo apt-get install nfs-common
```

On va pour notre exemple créer un répertoire `/srv/fichier`

### Montage simple

On lance la commande suivante (montage en lecture seule):

```bash
sudo mount -t nfs4 -o ro 172.16.81.10:/srv/fichiers /srv/partage/
```

Ou celle-ci (lecture / écriture):

```bash
sudo mount -t nfs4 -o rw 172.16.81.10:/srv/fichiers /srv/partage/
```

### Montage au démarrage

On modifie le fichier `/etc/fstab` et on ajoute la ligne suivante:

```fstab
172.16.81.10:/srv/fichiers     /srv/partage    nfs4    _netdev,nodev,noexec    0       0
```

Et on exécute la commande :

```bash
sudo mount -a
```


## Exercice

Modifiez votre serveur FTP pour qu'il partage un répertoire `/data` monté sur votre serveur de fichier.

Ce répertoire `data` devra être accessible en lecture/écriture.

À partir de Filezilla (en vous connectant au serveur FTP), vérifiez que tout fonctionne:

 - affichage du dossier `/data`
 - lecture
 - écriture (création de fichier ou de répertoire)

 
