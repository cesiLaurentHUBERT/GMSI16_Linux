# Utilisateurs et droits

## Création d'utilisateurs et de groupes

Les commandes pour gérer des utilisateurs sous Debian sont :
* `adduser`
* `addgroup`
* `deluser`
* `delgroup`

D'autres commandes existent comme `useradd` ou `groupdel` mais elles sont moins pratiques à utiliser.


Après chacune des opérations suivantes, observez le contenu des fichiers `/etc/group`, `/etc/passwd` et `/etc/shadow`

1. Créez un utilisateur nommé `billgates`
2. Ajoutez le dans un groupe nommé `microsoft`
3. Créez un utilisateur nommé `stevejobs`
4. Ajoutez le dans un groupe nommé `apple`
5. Créez un utilisateur nommé `linustorvalds` et un autre nommé `ianmurdock`
6. Les ajouter au groupe `linux`.
7. Quels sont les rôles des fichiers `/etc/group`, `/etc/passwd` et `/etc/shadow` ?
8. Connectez-vous en tant que `ianmurdock` avec la commande `su` et créez un fichier nommé `je-suis-su.txt`. Déconnectez-vous
9. Connectez-vous en tant que `ianmurdock` avec la commande `su -` et créez un fichier nommé `je-suis-su-tiret.txt`. Déconnectez-vous
10. Qu'observez-vous ? Où sont les deux fichiers créés ?
11. Utilisez la commande suivante: `sudo -u ianmurdock echo Bonjour >> fichier-ian.txt`. À qui appartient ce fichier ? Réessayer en mettant la cible dans `/tmp` (`/tmp/fichier-ian.txt`) puis dans `/home/ianmurdock`.
12. Utilisez la commande suivante: `sudo -u ianmurdock cp fichier-ian.txt copie-ian.txt`. À qui appartient ce nouveau fichier ? Essayer de faire ces opérations dans le répertoire `/home/ianmurdock`.

## Remplacer un mot de passe

La commande `passwd` permet de remplacer un mot de passe.

13. Modifiez le mot de passe de `linustorvalds` et mettez: `8chats+caiLLou`
14. Désactivez l'utilisateur `linustorvalds` (il doit exister sur le système mais toute tentative de connexion en l'utilisant doit échouer).
15. Réactivez l'utilisateur `linustorvalds`.
16. Ajoutez-le au groupe des `sudoers`

Cependant, il peut être parfois intéressant de changer un mot de passe sur un système dont on a perdu l'accès (par exemple avec un live-CD).

La commande suivante permet de générer un mot de passe:

```bash
openssl passwd -1 -salt clesalage  nouveaumotdepasse
```
16. À quoi correspond la clef de salage ?
17. Utilisez ce résultat pour remplacer le mot de passe de `linustorvalds` dans le fichier `/etc/shadow`. Vérifiez que vous pouvez vous connecter.

Voir aussi [cette page](https://gist.github.com/JensRantil/ac691a4854a4f6cb4bd9)

## ACLs

Créer l'arborescence suivante:

* informatique
* * linux
* * * sources
* * windows
* * * sources
* * mac
* * * sources

Dans chaque dossier `sources`, créer un fichier `init.c`.

18. Mettez les droits correspondants aux groupes définis précédemment et correspondant à ces répertoires (le groupe `apple` n'a pas le droit d'aller lire ni écrire dans le répertoire `windows` par exemple).
19. Utiliser la commande `setfacl` pour permettre à `linustorvalds` (et uniquement lui) de lire et écrire dans le répertoire `mac` et ses sous-répertoires. Testez.

20. Utilisez la commande `getfacl` pour vérifier les droits affectés au fichier `informatique/mac/sources/init.c`


Voir aussi [cette page](http://lea-linux.org/documentations/Gestion_des_ACL).

## Un peu de tâches asynchrones

21. Lancez la commande `crontab -e` et ajouter la ligne suivante:

```
*/2 * * * * /bin/date >> /home/laurent/dates.txt
```
22. Faites de même avec la ligne suivante:
```
* * * * * tail -1 /home/laurent/dates.txt > /home/laurent/derniere-date.txt
```

23. Expliquez ce que signifient chacune des étoiles.
1. Comment faire pour exécuter cette dernière commande tous les lundis ?

1. Lancez la commande suivante et immédiatement après la commande `tail -f /home/<USER>/fichier.log`:
```bash
for i in 1 2 3 4 5 ; do echo $i ; sleep 5 ; done >> /home/<USER>/fichier.log 2>&1 &
```

1. Ajouter la commande suivante dans le crontab de manière à ce qu'elle s'exécute à une heure prochaine :

```
for i in 1 2 3 4 5 ; do echo $i ; sleep 5 ; done >> /home/<USER>/fichier.log 2>&1
```

Observez les modifications avec la commande `tail` vue précédemment.

1. . Ajouter la commande suivante dans le crontab de manière à ce qu'elle s'exécute à une heure prochaine :

```
echo '' > fichier.log ; for i in Un Deux Trois Quatre Cinq ; do echo $i ; sleep 5 ; do
```

Observez ce qui se passe.

1. Configurez une sauvegarde avec la commande `tar` : les fichiers `/etc/group`, `/etc/passwd` et `/etc/shadow` tous les jours à 3h du matin. Avant de définir l'heure définitive, testez avec une heure vous permettant de constater les effets de la commande. Les fichiers seront stockés dans un sous-répertoire de `/root/`

1. Utilisez la commande `nohup` pour lancer le script `monscript.sh`. Contenu du script:

```bash
#!/bin/bash

for i in $(seq 1 60)
do
        echo $i
        sleep 1
done
```

1. Utilisez la commande `nohup` associée à `wget` pour télécharger le fichier `https://cdn.kernel.org/pub/linux/kernel/v4.x/linux-4.11.3.tar.xz`. À quoi sert cette commande `nohup` ?
