# Connexion X et utilisateur Root

## Problème
On a parfois ce message quand on exécute un programme avec `sudo`

```
X11 connection rejected because of wrong authentication.
```

## Pour tester
On installe le paquet `x11-apps` et on utilise `xeyes` avec `sudo`.

## Solution

Pour corriger le problème il faut:

1. lancer la commande suivante et en copier la sortie dans le presse-papier:

```bash
$ xauth list
netservice/unix:10  MIT-MAGIC-COOKIE-1  9577b0ac5ac9e8cdb6a96e7b53f6969a
$
```

2. se connecter en tant que `root` (`sudo su -`) puis faire un `xauth add` en collant le contenu du presse-papier à la suite:

```bash
$ xauth add netservice/unix:10  MIT-MAGIC-COOKIE-1  9577b0ac5ac9e8cdb6a96e7b53f6969a
$
```

3. Tester:

```bash
sudo xeyes
```



## Automatisation
Voici la solution pour automatiser: un script qui transfère les autorisations à root.

Ce script peut être ajouté à la fin de votre fichier `.bash_profile` sur votre serveur.

### Scripts



Pour cela, vous avez deux versions du  scripts ci-dessous.

Ce script doit être sauvé dans un fichier `xforward2root`. Le fichier doit être rendu exécutable grâce à la commande `chmod +x`.

Il pourra être exécuté en lançant la commande `./xforward2root`

#### Première version
```
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

#qui a pour effet d'ajouter le cookie récupéré précédemment dans le fichier
#.Xauthority qui se trouve dans le HOME de root
```


#### Deuxième version
```
#!/bin/bash

#deuxième méthode
numdisplay=$(echo $DISPLAY | cut -d: -f2 | cut -d. -f1)
cookie=$(xauth list |grep :$numdisplay)
sudo xauth add $cookie
#sudo xauth add $cookie #Ne marche pas dans certains cas (Trisquel)
sudo su <<EOF
xauth add $cookie
EOF
```
