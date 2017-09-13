# UNIX/Linux TD2 : Processus et fichiers

## Introduction
UNIX/Linux permettent de gérer des processus de manière parallèle: pendant qu'un logiciel est exécuté, un autre peut également l'être. Ceci donne l'illusion que les deux programmes tournent en parallèle.

Nous présentons ici quelques fonctionnalités du shell. Même si vous n’utilisez pas toutes les fonctionnalités, il est utile de les connaître, dans le cas où vous en auriez besoin.

## Conventions



Dans ce document, quand vous rencontrez :
```bash
$ sleep 5
<wait 5 seconds>
$
```

* $ représente le prompt,
* la commande qui est lancée est `sleep 5`
* elle produit le résultat d’attendre 5 secondes.

Quand vous voyez <Ctrl-C>, cela signifie que vous devez taper la touche Ctrl et la touche C en même temps.


## Gestion des processus

### Exécution
Quand vous lancez :

```bash
sleep 5
```

### Terminaison
Vous exécutez cette commande en *foreground*. Ce qui signifie que le shell attend la fin de la commande avant de vous "rendre la main". Maintenant exécutez :

```bash
$ sleep 6000
<Ctrl-C>
$
```

Quand vous pressez la séquence de touche, vous envoyez un signal de terminaison au processus actuel (celui qui s’exécute en avant plan), ce qui fait que vous n’avez pas à attendre 6000 secondes avant de pouvoir exécuter une nouvelle commande.

### Exécution en arrière-plan

Plutôt que de lancer une commande en *foreground* (avant-plan), on peut la lancer en *background* (arrière-plan).

Ceci se fait simplement en rajoutant un `&` après la commande.

```bash
$ sleep 6000 &
[3] 27181
$
```

En exécutant cette commande en arrière plan, le prompt "revient" immédiatement. De plus Unix, vous indique que le job que vous venez de lancer est le numéro 3, et que son numéro de processus est le 27181 (dans cet exemple).

Le shell garde une trace de tous les jobs en cours d’exécution. La commande jobs permet de les afficher

```bash
> jobs
[2] - running evince TDUnixJobs.pdf
[3] + running sleep 6000
```

### Affichage des processus

L'affichage des informations sur les processus se fait par la commande `ps`:

```bash
$ ps
   PID TTY          TIME CMD
  3342 pts/2    00:00:00 bash
  3361 pts/2    00:00:00 sleep
  3362 pts/2    00:00:00 ps
$
```

On peut ajouter des options à la commande `ps` et la filtrer avec la commande `grep`:

```bash
$ ps -elf | grep sleep
0 S laurent    3089   2525  0  80   0 -  1803 restar 10:15 pts/0    00:00:00 sleep 6000
0 S laurent    3361   3342  0  80   0 -  1803 restar 11:20 pts/2    00:00:00 sleep 6000
0 S laurent    3365   3342  0  80   0 -  2945 pipe_w 11:25 pts/2    00:00:00 grep --color=auto sleep
$
```

Et même rajouter l'entête des colonnes:

```bash
$ ps -elf | head -1 ; ps -elf | grep sleep
F S UID         PID   PPID  C PRI  NI ADDR SZ WCHAN  STIME TTY          TIME CMD
0 S laurent    3089   2525  0  80   0 -  1803 restar 10:15 pts/0    00:00:00 sleep 6000
0 S laurent    3361   3342  0  80   0 -  1803 restar 11:20 pts/2    00:00:00 sleep 6000
0 S laurent    3365   3342  0  80   0 -  2945 pipe_w 11:25 pts/2    00:00:00 grep --color=auto sleep
$
```



On peut aussi voir ce processus en faisant :

```bash
$ ps aux | grep sleep
laurent 27181 0.0 0.0 7308  704 pts/4 SN 15:26 0:00 sleep 6000
laurent 27430 0.0 0.0 14272 964 pts/4 S+ 15:33 0:00 grep sleep
$
```

Comment faire pour terminer ce job? Il y a plusieurs façons :

```bash
$ kill 27181   # En utilisant son pid
$ kill %3      # En utilisant son numéro job
$ fg           # puis <Ctrl-C>
```

`fg` ramène le processus à l'avant-plan, il peut être interrompu avec la combinaison <Ctrl-C>

Vous pouvez aussi mettre en *background* une commande que vous venez de lancer, en exécutant

```bash
$ sleep 6000
<Ctrl-Z>
ˆZ
[1] + 28640 suspended sleep 6000


$ bg
[1] 28640 continued sleep 6000
```


## Variables d’environnement
La commande `printenv` permet d’afficher les variables d’environnement. Les variables d’environnement sont des variables que le shell (et d’autres programmes) utilisent :

```bash
$ printenv
LC_PAPER=en_US.UTF-8
LC_ADDRESS=en_US.UTF-8
LC_MONETARY=en_US.UTF-8
TERM=xterm-256color
SHELL=/bin/bash
XDG_SESSION_COOKIE=282a2a18a......... SSH_CLIENT=192.168.136.4 52876 22
LC_NUMERIC=en_US.UTF-8
SSH_TTY=/dev/pts/0
USER=fd
LC_TELEPHONE=en_US.UTF-8
MAIL=/var/mail/fd PATH=/usr/local/bin:/usr/bin:/bin:/pau/new_pkg/bin/; ̃/bin LC_IDENTIFICATION=en_US.UTF-8
PWD=/pau/homep/profs/fd/
LANG=en_US.UTF-8
LC_MEASUREMENT=en_US.UTF-8
SHLVL=1
HOME=/home/laurent/
LOGNAME=fd
LC_CTYPE=fr_FR.UTF-8
DISPLAY=localhost:10.0
LC_TIME=en_US.UTF-8
LC_NAME=en_US.UTF-8
_=/usr/bin/printenv
```

La commande `set` affiche toutes les variables d’environnement définies. Il en existe beaucoup d’autres, certaines n’étant utilisées que par certains programmes. Certaines personnes peuvent avoir 200 variables d’environnement définies, voire plus.

Les variables d’environnement commençant par `LC` concerne la locale que vous utilisez. C’est grâce à ses variables que le système vous propose la bonne langue. Pour voir la liste des locales, il faut utiliser la commande :


```bash
$ locale -a C
C.UTF-8
en_US.utf8
POSIX
```

Voici une description des autres variables d’environnement :
* `SHELL` : le type du shell utilisé
* `USER` : l’utilisateur actuel
* `PWD` : le répertoire courant
* `PATH` : les chemins de recherche des exécutables

### Recherche de commandes
Quand vous tapez une commande (par exemple la commande `toto`), le shell :

1. analyse la variable `PATH`,
2. vérifie pour chaque répertoire listé dans `PATH` si un fichier exécutable nommmé `toto` existe (dans l’ordre des répertoires spécifiés)
3.  exécute le programme `toto` qu'il a éventuellement trouvé.

Ici, le shell essaye de trouver `toto` dans `/usr/local/bin`, si la commande s’y trouve, alors il exécute cette commande. Sinon, il recherche dans le répertoire `/usr/bin` (puis les suivants) jusqu’à ce qu’il ait trouvé la commande `toto`, ou qu’il ait épuisé tous les répertoires. Si à la fin de la recherche, il n’a pas trouvé la commande alors il affiche :

```bash
$ toto
-bash: toto: command not found
$
```


Ce qui signifie qu’il n’a pas trouvé cette commande dans les répertoires du `PATH`. Pour modifier le chemin de recherche, il faut modifier la variable `PATH`, comme suit :
```bash
$ export PATH=${PATH}:/un/nouveau/chemin:/un/autre/chemin
$
```

Vous pouvez connaître la commande qui est exécutée avec la commande `which`. Ici le shell vous indique que la commande ls se trouve dans le répertoire `/bin`

```bash
$ which ls /bin/ls
$
```


### Gestion des paramètres
Lorsque vous tapez :


```bash
$ echo hello world
```

Les étapes suivantes sont réalisées :

1.Séparation de la ligne en mot (en utilisant les blancs [**] comme séparateur)
2. Expansion des mots
3. Création d’un nouveau processus
4. Exécution de la commande (qui est forcément le premier mot)
5. Fourniture des mots comme argument à la commande

[**] *en réalité, c'est la variable d'environnement IFS qui est utilisée*

Ceci peut poser un problème, comme montrez ci-dessous :


```bash
$ ls
$ touch mon fichier
$ ls
fichier mon
$
```

Ceci peut être contourné en utilisant :

```bash
$ touch 'mon fichier'      #utilisation d'une chaine
$ touch "mon fichier"      #utilisation d'une chaine interprétée
$ touch mon\ fichier       #échappement de caractère
```


### Expansion des arguments

Le shell réalise entre l’étape 1 et l’étape 3, des opérations d’expansion qui peuvent surprendre, comme dans l’exemple suivant :

```bash
$ cd /tmp
$ echo *what* is your name ?
*what* is your name ?
$ cd /usr/bin
$ echo *what* is your name ? *what* is your name ?
whatis is your name ?
```


Que se passe-t-il ?

En fait, le `shell` essaye de faire un *pattern matching* sur les fichiers présents dans le répertoire où on se situe lorsqu’on exécute la commande. Le caractère `*` matche n’importe quelle séquence de 0 à n caractères dans le nom de fichier. Donc `*what*` match sera remplacé par `whatis`. Il en va de même pour le caractère `?`. `?` remplace 1 caractère.

Si vous voulez empêcher l’expansion, il faut mettre des `"` autour de l’expression, comme dans l’exemple suivant :


```bash
$ echo "*what* is your name ?"
*what* is your name ?
```

C’est la raison pour laquelle la commande :

```bash
$ rm -rf *
```

supprime l’ensemble des fichiers du répertoire courant. En fait, il existe d’autres expansions [*]:
* `*` : 0 à n caractères
* `?` : 1 caractère
* `[abc]` : le caractère `a`, `b` ou `c`
* `[a-z]` : un caractère parmi les lettres comprises entre `a` et `z`
* `$*` : tout ce qui commence par $ est du domaine de l’expansion de paramètres

[*] *On utilise aussi souvent le terme *wildcard* à la place de *expansion.

Beaucoup d’expansion sont possibles, mais la plus courante est l’expansion de variables comme dans l’exemple :

```bash
$ echo ${PATH}
/usr/local/bin:/usr/bin:/bin:/home/new_pkg/bin/;~/bin
$
```

### Manipulations
Vous devez répondre à ces questions, en vous déplaçant le moins possible. C’est-à-dire que vous ne devez vous déplacer que lorsque cela vous est demandé.


*Exercice 1* (Manipulations basiques).

1. Affichez les fichiers de votre répertoire utilisateur.
1. Créez un fichier appelé `ancien`, sans utiliser un éditeur de texte.
1. Copiez le fichier `ancien` vers un fichier appelé `nouveau`.
1. Vérifiez la présence de ces deux fichiers.
1. Effacez le fichier `ancien`.
1. Vérifiez l’effacement.
1. Créez un répertoire appelé : `repertoireAvecUnNomSuperLongPourNePasAvoirEnvieDeLeTaperPlusDUneFois`.
1. Vérifiez sa présence.
1. Déplacez vous dans ce répertoire [*].
1. Copiez le fichier `nouveau` dans ce répertoire.
1. Utilisez une expression avec un *wildcard* pour lister les fichiers dans le répertoire courant.
1. Revenez dans votre *home directory* (3 solutions)
1. Effacez le répertoire que vous avez créé précédemment.
1. Vérifiez la suppression.



[*] *vous pouvez utiliser la touche `TAB` après avoir taper les premières lettres de son nom: celui-ci sera complété automatiquement par le `shell`*

## Alias et fichier de configuration
Dans un terminal, tapez le code :


```bash
$ alias cd=ls
$ cd
```

Vous constatez que maintenant la commande `cd` se comporte comme la commande `ls`. Ceci est normal, vous venez de définir un alias qui indique que `cd` est `ls`. Les alias sont très pratique notamment lorsqu’on veut surcharger une commande avec des options par défaut. Ici, l’alias précédemment défini vous empêche d’utiliser correctement la commande `cd`.

Comment faire pour pouvoir utiliser la commande initiale ?

Deux solutions s’offrent à vous :

1. Faire précéder la commande que l’on souhaite utiliser du caractère \. Cela permet d’utiliser temporairement la commande fournit par le shell :

```bash
$ \cd
```

2. Enlever l’alias, et donc revenir à la définition primaire du shell

```bash
$ unalias cd
```


La commande `alias` utilisée seule permet de voir les alias définis.

### Configuration des alias

Le fichier `~/.bashrc` est un fichier de configuration lu à chaque démarrage d’un terminal.

C’est donc l’endroit, où il faut configurer votre environnement afin qu’il s’adapte à votre besoin. à titre d’exemple, j’ai ajouté les alias suivant :


```bash
    alias l='ls'
    alias xs='cd'
    alias vf='cd'
    alias more='less'
    alias moer='more'
    alias moez='more'
    alias kk='ll'

```


qui me permette de faire de fautes de frappe lorsque je tape des commandes.


Ce fichier sert aussi à positionner certaines variables d’environnement. Celles-ci étant utilisées par certains programmes. Dans un shell, tapez la séquence suivante :

```bash
$ SAVE=${PS1}
$ echo ${SAVE}
<Vérifier que vous avez bien quelque chose qui s\'affiche>
<sinon recommencez !!!>
 $ export PS1=">"
```


Vous savez maintenant configurer votre environnement. à vous de tester des modifications, puis de les enregistrer dans votre fichier `~/.bashrc`. Pour que les modifications du fichier `~/.bashrc` soient prises en compte, il suffit soit de lancer un nouveau terminal, soit de taper la séquence


```bash
$ source ~/.bashrc
```
