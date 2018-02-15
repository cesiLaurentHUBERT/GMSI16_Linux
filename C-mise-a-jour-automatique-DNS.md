# Mise à jour automatique du DNS

## Introduction

Dans certains locaux, l'utilisation de DNS externes est bloqué. Il est donc nécessaire d'utiliser les DNS fournis par le réseau local.

Pour cela, un script est proposé dans ce document, afin de faciliter cette mise à jour.

## Modification de la configuration de bind9

Editer le fichier de confituration de bind9 `/etc/bind/named.conf.options`:

  - Remplacer la section suivante:

```conf
forwarders {
    8.8.8.8;
};
```

  - par:

```conf
include "/etc/bind/named.conf.forwarders";
```

## Création du script de mise à jour

Créer un nouveau fichier `bind9-updateforwarders` et le rendre exécutable:

```bash
touch bind9-updateforwarders
chmod +x bind9-updateforwarders
```

Editer ce fichier et y placer le code suivant:

```bash
#!/bin/bash
####################################
# Author: Laurent HUBERT
# Licence: GPL
####################################

cat /etc/resolv.conf | grep nameserver | awk 'BEGIN{print "forwarders{"}{print "\t"$2";"}END{print "};\n"}' > /etc/bind/named.conf.forwarders

systemctl restart bind9

```

Copier ce fichier dans le répertoire `/usr/local/bin`:

```bash
sudo cp bind9-updateforwarders /usr/local/bin/
```

Exécuter le fichier:

```bash
bind9-updateforwarders
```
