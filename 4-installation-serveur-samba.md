

# Installation d'un serveur SAMBA
## Serveur SAMBA

Sur la machine précédemment créée pour servir de serveur de fichiers, vous allez installer un serveur Samba.

Il devra être accessible via le réseau avec le nom `srv-fichiers`

### Configuration

Le serveur SAMBA devra comporter:

-   un partage du home de chaque utilisateur

-   un partage allusers (positionné dans /srv/samba/allusers)

Les utilisateurs suivants seront configurés sur ce serveur pour accéder
aux données:

-   intervenant

-   Pierre

-   Paul


### Installation
Un tutoriel vous est donné ci-dessous.

Vous devrez peut-être ajouter de l'espace disque sur votre serveur, selon les besoins.

#### Commandes de redémarrage

```bash
sudo systemctl restart smbd
```

#### Tutoriel

Vous pouvez tenir compte de ce tutoriel et l'adapter à vos besoins :

https://debian-facile.org/atelier:chantier:samba-partage-reseau

### Autres outils

Il est possible de configurer Samba via un outil graphique (`system-config-samba`). Seul souci: cet outil n'est disponible que sur les distributions Ubuntu.

Cependant, il est possible de l'installer grâce à quelques commandes après l'avoir téléchargé depuis les dépôts Ubuntu.


#### Marche à suivre

On commence par installer les dépendances:

```bash
sudo apt-get update && sudo apt-get install python-libuser python-gtk2 python-glade2 samba
```

On va télécharger le fichier depuis les dépôts Ubuntu (une recherche Web permet de trouver ce fichier ayant l'extension `.deb`):

```bash
wget http://launchpadlibrarian.net/163794081/system-config-samba_1.2.63-0ubuntu6_all.deb
```

On peut maintenant installer ce paquet téléchargé:

```bash
sudo dpkg -i -f sudo dpkg -i system-config-samba_1.2.63-0ubuntu6_all.deb
```

Il reste enfin à créer un fichier de configuration dont l'absence empêche le lancement de `system-config-samba`:

```bash
sudo touch /etc/libuser.conf
```

Cette procédure est inspirée par [cette page](http://geekeries.de-labrusse.fr/?p=113)

Pour lancer `system-config-samba`, il suffit de taper la commande suivante:

```bash
sudo system-config-samba
```

Il faut évidemment être super-utilisateur pour exécuter cette commande. Cependant, cela peut poser problème.

Le problème est qui peut apparaître (`can't open display`) peut avoir plusieurs causes:

    - la session courante n'a pas de `x11 forwarding`
    - l'autorisation `xauth` n'est pas transmise à root

Dans le premier cas, la résolution du problème est simple. Dans le second, il faut se référer à l'annexe concernant la *configuration X pour root*.
