

# Installation d'un serveur FTP

## Serveur FTP

### Nom de la machine

`<votre nom>-ftp` (par exemple : torvalds-ftp)

### Partitionnement

Cette machine comportera un disque dur de 6Go séparé en quatre
partitions: une partition `/`, une partition `/home`, une partition
`/var` et `/tmp`.

### Configuration

Installez un serveur VSFTPD avec TLS. Si vous utilisez le lien
ci-dessous, **vous devez tenir compte du paragraphe qui suit pour créer
le certificat**.

<https://websetnet.com/install-configure-vsftpd-tls-debian-8-jessie>

#### Création du certificat

La ligne de commande pour créer le certificat est celle-ci (remplace
celle donnée sur le lien précédent):

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout /etc/ssl/private/vsftpd_key.pem -out /etc/ssl/private/vsftpd.pem

De ce fait, il faut modifier le fichier vsftpd.conf en
ajoutant/modifiant les lignes suivantes :

    rsa_cert_file=/etc/ssl/private/vsftpd.pem
    rsa_private_key_file=/etc/ssl/private/vsftpd_key.pem

#### Répertoire à accéder

Les utilisateurs pourront accéder uniquement à un seul répertoire depuis
leur client FTP. Ce répertoire de base pour vsftpd sera
`/srv/ftp/allusers`

Pour configurer ce répertoire, il sera nécessaire d’utiliser les deux
directives suivantes dans `/etc/vsftpd.conf`:

    chroot_local_user=YES
    local_root=/srv/ftp/allusers
