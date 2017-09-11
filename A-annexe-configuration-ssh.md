# Configuration de connexion ssh

## Création d'une clef

### Putty

En utilisant PuttyGen, vous pouvez générer une clef SSH

Pour cela, cliquer sur le bouton `Generate` puis déplacer la souris pendant quelques secondes jusqu'à ce que la barre de progression soit complétée.

Une fois cela fait:
1. Entrez une *passphrase* (une phrase assez longue qui va protéger votre clef)
1. Confirmez la *passphrase*
1. Sauvez la clé privée avec le bouton `Save private key` dans un emplacement de votre disque
1. Copiez le texte complet (y compris `ssh-rsa` et jusqu'en bas de la zone de sélection qui peut dépasser la zone visible)
1. Suivez les instructions du paragraphe *Installation d'une clef publique*

### Git-bash

Sur le client `git-bash` la commande à utiliser est la suivante:

```bash
$ ssh-keygen
```

La commande affiche:

```
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/lauhub/.ssh/id_rsa):
```

Ne rien saisir (laisser la valeur par défaut) pour la question `Enter file in which to save the key` et appuyez sur la touche `Entrée`.

Ensuite, choisir une passphrase:

```
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /Users/lauhub/.ssh/id_bla.
Your public key has been saved in /Users/lauhub/.ssh/id_bla.pub.
The key fingerprint is:
SHA256:qnMr1UW+zi3msGd/2UWfkqfq/ZkIi6mZzqeDLGFghh8 lauhub@MacBookAir-2-Laurent.local
The key's randomart image is:
+---[RSA 2048]----+
|                 |
|          .      |
|.        o       |
|.+E       o     .|
|o.  .. .S. .  ..o|
|  .o  ... .  oo o|
|  . o.o oo o  +o.|
|   .o+o.o+X =.+ +|
|    o+oO*Bo*O+.+ |
+----[SHA256]-----+
```

Vous obtenez deux fichiers:
-  `~/.ssh/id_rsa`
-  `~/.ssh/id_rsa.pub`

Récupérez le contenu de la clef publique et copiez le:
```bash
$ cat ~/.ssh/id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC/tAFES1cFMs7O4mVJql7C4aLW/67F6gDyXiE2JOcQIk8+WeqSyMAegOHw0OsdhnAQdf5iDPI19UQgD+kES6gHG7ZBLg+sw3v0yloAf4W1HBdHVj75HTvx0i84d7WH0Q1RuBTgq52ni7IfmvZfhHaMaRHkRcY45cfX+BY2vh4y6BwYF0bmQWOlJUJgpMaJx9lZ2ByedCBlbnYRcdcKBASSHrL7TWR7twJyF321+B6W03BIlvwF8RupROVMBSn+56BIQ27HR4DKe/VL4FOpGAF4LM9vLEoG/9J3kuMWhwk44B3ZW7GTKAvFgmFTT7YH/gXwK4+lppJuGhPMqYLQSyzV commentaire
```

Copiez ce contenu (y compris `ssh-rsa` et `commentaire`).

Suivez les instructions du paragraphe *Installation d'une clef publique*


## Installation de la clef publique
La clef publique est la *serrure* qui permettra de se connecter à une machine en utilisant la clef privée qui lui est associée.

Pour l'installer:
1. Ouvrez une session ssh sur la machine avec votre compte
1. Créez un répertoire .ssh s'il n'existe pas (dans le répertoire utilisateur) : `mkdir -p ~/.ssh`
1. Allez dans le répertoire .ssh: `cd ~/.ssh`
1. Créez un nouveau fichier nommé `authorized_keys` : `nano authorized_keys`
1. Collez le contenu de la clef publique que vous avez copié précédemment
1. Déconnectez-vous
1. Testez l'installation en suivant les instructions du paragraphe `Connexion au serveur`

## Connexion au serveur
### En utilisant Putty

Dans le menu connexion, il faut définir la clef à utiliser:
1. Connection > SSH > Auth
1. Choisir la clef privée créée précédemment dans le champ `Private key file for authentication`
1. Connectez-vous au serveur en cliquant sur le bouton Open


### Avec git-bash

Connectez-vous directement:

```bash
$ ssh user@nom_ou_adresse_serveur
```

La passphrase vous est demandée. Entrez-la.

Vous pouvez maintenant configurer l'agent (voir le paragraphe correspondant ci-dessous)

## Configuration de l'agent

### Avec Putty

Ce tutoriel indique comment configurer `Pageant` (l'agent Putty de gestion des passphrases): https://www.siteground.com/tutorials/ssh/putty.htm

### Avec git-bash

Sur le client, créer le fichier `.bash_profile` :

```bash
touch .bash_profile
```

Éditer le fichier `.bash_profile`. Ajoutez les lignes suivantes à la fin du fichier :

```bash
SSH_ENV="$HOME/.ssh/environment"

function start_agent {
     echo "Initialising new SSH agent..."
     /usr/bin/ssh-agent | sed 's/^echo/#echo/' > "${SSH_ENV}"
     echo succeeded
     chmod 600 "${SSH_ENV}"
     . "${SSH_ENV}" > /dev/null
     /usr/bin/ssh-add;
}

# Source SSH settings, if applicable

if [ -f "${SSH_ENV}" ]; then
     . "${SSH_ENV}" > /dev/null
     #ps ${SSH_AGENT_PID} doesn't work under cywgin
     ps -ef | grep ${SSH_AGENT_PID} | grep ssh-agent$ > /dev/null || {
         start_agent;
     }
else
     start_agent;
fi
```


 En cas de non fonctionnement, voir:

 https://github.com/gnhuy91/til/issues/26
