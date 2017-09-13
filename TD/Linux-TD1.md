

L’objectif de ce TD est de vous familiariser avec les commandes de base
d’Unix/Linux. Il est normal que vous ne sachiez pas répondre à quelques
questions. Lorsque vous êtes bloqués, demandez de l’aide à l'intervenant. Par ailleurs, si la réponse à une question vous semble trop
longue, c’est certainement qu’il y a une solution plus rapide. Là
encore, n’hésitez pas à demander de l’aide…

Un autre objectif de ce TD est de vous montrer l’intérêt de la ligne de
commande, ainsi que de commencer à explorer la puissance de la ligne de
commande. Pour réaliser ce TD, vous pouvez essayer de le faire via
l’interface graphique ou via la ligne de commande. Cependant
certaines réponses nécessiteront l’utilisation de la ligne de commande.

Pour réaliser ce TD dans de bonnes conditions, nous vous conseillons de
créer un répertoire spécifique, et de réaliser les actions à partir de
ce répertoire.


Premier pas sous Unix
=====================

Déplacement, visualisation
--------------------------

1.  Récupérer l’archive [premiersPas.tgz](data/premiersPas.tgz). Décompressez la, puis
    visualisez le fichier `TDUnix.pdf`.


2.  Le fichier `TDUnix.txt` est un lien vers le fichier
    `TDUnix.pdf` (ce lien est mal nommé puisqu'il s'agit d'un PDF)

    -   Comment pouvez vous voir qu’il s’agit d’un lien ?

    -   Comment être *sûr* qu’il s’agit effectivement d’un PDF ?

    -   Visualisez le, sans le renommer, afin de pouvoir lire clairement
        le texte du TD.

    -   Renommez ce fichier en `TDUnixBis.pdf`

3.  Visualisez le fichier `2050.txt`

Exécution, affichage
--------------------

1.  Exécutez le fichier `init`.

2.  Combien de fichiers ont été créés ?

3.  En êtes vous sûr ?

4.  Combien de répertoires ont été créés ?

5.  Listez tous les fichiers dont le nom contient un `F`.

6.  Listez tous les fichiers dont le nom contient deux chiffres.

7.  Listez tous les fichiers dont le nom contient deux chiffres qui se
    suivent.

8.  Effacez tous les fichiers dont le nom fini par `d`.

9.  Copiez tous les fichiers dont le nom contient un `1` ou un `3` dans un sous-répertoire nommé `copy` (créez le si nécessaire).

10. Mettez, dans un fichier nommé `listeComplete.txt`, la liste de tous
    les fichiers créés par le *script* `init`.

11. Déplacez le à la racine de votre compte.



Un peu d’autonomie
------------------



1.  Effacez le répertoire `unix` créé par le script `init`. Fermez le
    terminal, et ouvrez de nouveau un terminal. Cette partie ne se fait
    uniquement que par la ligne de commande.

2.  Vérifier où on se trouve dans l’arborescence des fichiers
    (répertoire courant)

3.  Se déplacer à la racine du disque. Lister les répertoires existants.

4.  Se déplacer dans votre répertoire de travail par défaut (3
    solutions)

5.  Créer un répertoire de nom `UNIX`

6.  Sous `UNIX` créer un répertoire de nom `TP1`

7.  Aller dans le dernier répertoire créé

8.  Créer un répertoire de nom `tmp`

9.  Aller dans le répertoire `tmp`

10. Créer un fichier vide nommé `toto`

11. Lister l’arborescence du répertoire `UNIX` en format long

12. Remonter d’un niveau dans l’arborescence.

13. Détruire le répertoire tmp avec la commande rmdir

    -   Que se passe-t-il ?

    -   Faites en sorte de détruire ce répertoire.

14. Prendre le fichier `dessus.txt` et le copier à la racine de votre
    compte Linux

15. Visualiser le fichier `dessus.txt` à l’aide de la commande `cat`.
    Puis le visualiser à l’aide de la commande `more`. Quelles sont les
    différences ?

16. Faire de même avec la commande `less`

17. Le rouvrir avec la commande `less`. Rechercher le mot *locataire*.

18. Recréer le répertoire `tmp`

19. Effectuer une copie du fichier `dessus.txt` dans le répertoire `tmp`

20. Aller dans le répertoire `tmp` et renommer le fichier `dessus.txt`
    en `Dessous.txt`.

21. Le déplacer à la racine de votre compte

22. Vérifier que le fichier s’y trouve bien
