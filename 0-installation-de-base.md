# Installation de base sur un serveur Debian

## Introduction
Ceci montre les éléments de base à configurer sur un serveur Debian. Ces éléments permettent de gérer un serveur avec la commande `sudo`et d'installer un éditeur de texte plus pratique que `nano`.


## Installation sudo
Sudo permet de prendre les droits super-utilisateur tout en contrôlant finement ces droits. De plus il permet de garder une trace de ces opérations dans les log, ce qui responsabilise les personnes ayant ces droits.

Ces personnes sont appelées *sudoers*. Elles le deviennent dès qu'elles sont ajoutées au groupe `sudo`.

```bash
#Installation
apt-get install sudo

#Ajout de l'utilisateur au groupe sudo
adduser <VOTRE UTILISATEUR> sudo
sudo -u <VOTRE UTILISATEUR> echo bonjour
```

Remplacer `<VOTRE UTILISATEUR>` par le vrai identifiant de votre utilisateur. Par exemple: `paul`

Se déconnecter de l'utilisateur `root`

Se déconnecter de l'utilisateur `<VOTRE UTILISATEUR>`. Ceci est nécessaire pour que `<VOTRE UTILISATEUR>` soit effectivement ajouté au groupe (il ne le sera pas pour une session déjà ouverte).

Se reconnecter

## Installation ne

`ne` (Nice Editor) est un éditeur de texte ayant une ergonomie plus simple pour les personnes peu habituées à Linux. Ce paragraphe illustre l'installation de `ne` ou de n'importe quel autre paquet.

### Pour les systèmes installés à partir des DVD

Si vous avez installé votre système à partir des DVD, vous devez réaliser les opérations suivantes. Sinon, ce n'est pas nécessaire **sauf si vous souhaitez ajouter les paquets `non-free` (non libres)**.

Plus d'informations sont disponibles sur les fichiers `sources.list` ici : https://wiki.debian.org/fr/SourcesList (faire une recherche avec les mots : `debian apt source`)

Editer le fichier `/etc/apt/sources.list`:

```bash
sudo nano /etc/apt/sources.list
```
Commenter les lignes commençant par `deb http://security.debian.org` et `deb-src http://security.debian.org/`

Rajouter les lignes suivantes (d'après https://wiki.debian.org/fr/SourcesList).

```
deb http://deb.debian.org/debian jessie main contrib non-free
deb-src http://deb.debian.org/debian jessie main contrib non-free

deb http://deb.debian.org/debian jessie-updates main contrib non-free
deb-src http://deb.debian.org/debian jessie-updates main contrib non-free

deb http://security.debian.org/ jessie/updates main contrib non-free
deb-src http://security.debian.org/ jessie/updates main contrib non-free
```

Les paquets `non-free` sont les paquets non libres de droits intellectuels.

### Mise à jour apt

Ceci permet de mettre à jour la base de données locale des dépendances. À faire avant toute installation si vous ne l'avez pas fait dans les dernières 24h.

```bash
sudo apt-get update
```

### Installation

L'installation du paquet proprement dite se fait avec la commande suivante:

```bash
sudo apt-get install ne
```
