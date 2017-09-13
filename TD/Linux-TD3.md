# Manipulation de données sous UNIX / Linux

## Recherche et classement de données

Récupérer les fichiers suivants depuis [donnees-linux.tar.gz](data/donnees-linux.tar.gz) : `2050.txt`, `nblng10.txt`, `tp2.txt` et `notes.txt`

1.  Lister les lignes du fichier `2050.txt` contenant le chiffre 1

2.  Lister les lignes du fichier `2050.txt` contenant le chiffre 1 ou le chiffre 5

3.  Modifier cette commande afin de faire apparaître au début de chaque ligne le numéro de la ligne correspondante dans le fichier d’origine

4.  Listez le fichier `tp2.txt` en faisant précéder chaque ligne par son numéro dans le fichier.

5.  Renommer le fichier `tp2.txt` en `tp2resultat.txt`

6.  Revenir à la racine de **votre répertoire utilisateur** (`$HOME`) et lister tous les fichiers de votre espace de travail avec leurs attributs complets

7.  Rechercher le fichier nommé `tp2resultat.txt`

8.  En utilisant la même commande afficher toutes les lignes du fichier `2050.txt` contenant le chiffre 1 ou le chiffre 9

9.  En utilisant la même commande afficher toutes les lignes des fichiers `tp2resultat.txt` et `2050.txt` contenant le chiffre 1 ou la chaîne *er*

## Lignes vides et lignes blanches

10. Dans le fichier `nblng10.txt`, sur combien de lignes le mot `Nibelung` apparaît-il ?

11. Eliminer les lignes vides et mettre le résultat dans `nblng10bis.txt`

12. Extraire les lignes ne contenant que des blancs (espaces ou tabulations) et mettre le résultat dans `nblng10ter.txt`

13. Combien y a-t-il de lignes blanches ?

## Redirections
Les tubes (pipe) Unix permettent de connecter le flot de sortie d’une
commande au flot d’entrée d’une autre. Ainsi on peut enchaîner plusieurs
commandes sur un flot initial afin de transformer ce flot par étapes
successives.

**Exemple** : ` head -3 notes.txt | tail -2` permet d’extraire les 2e et 3e lignes du fichier notes.txt

A partir du fichier `notes.txt`, écrivez les traitements suivants, en utilisant les commandes `cut` et `sort` :

14.  Afficher la liste des étudiants de la licence informatique triée par ordre alphabétique des nom et prénom.

15.  Afficher les résultats de `LIN2` par ordre croissant des notes. En cas d’égalité, les étudiants sont rangés par ordre alphabétique des nom et prénom.

Ici, vous pouvez utiliser la commande `tr` :

16. Prendre le contenu du fichier `nblng10.txt` et le mettre en majuscules dans le fichier `nblng10maj.txt`
