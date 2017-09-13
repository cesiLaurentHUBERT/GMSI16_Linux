

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
Vous pouvez tenir compte de ce tutoriel :

https://debian-facile.org/atelier:chantier:samba-partage-reseau
