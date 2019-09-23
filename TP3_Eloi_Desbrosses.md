# TP3 Gestion des paquets - Eloi Desbrosses

## Exercice 1. Commandes de base

Commencez par mettre à jour votre système avec les commandes vues dans le cours.
Donnez les commandes répondant aux questions suivantes :

```
serveur@serveur:~$ sudo apt update
Atteint :1 http://fr.archive.ubuntu.com/ubuntu disco InRelease
Atteint :2 http://fr.archive.ubuntu.com/ubuntu disco-updates InRelease
Atteint :3 http://fr.archive.ubuntu.com/ubuntu disco-backports InRelease
Atteint :4 http://fr.archive.ubuntu.com/ubuntu disco-security InRelease
Lecture des listes de paquets... Fait
Construction de l'arbre des dépendances
Lecture des informations d'état... Fait
Tous les paquets sont à jour.
serveur@serveur:~$ sudo apt upgrade
Lecture des listes de paquets... Fait
Construction de l'arbre des dépendances
Lecture des informations d'état... Fait
Calcul de la mise à jour... Fait
0 mis à jour, 0 nouvellement installés, 0 à enlever et 0 non mis à jour.
```

**1. Quels sont les 5 derniers paquets installés sur votre machine ?**

```
serveur@serveur:~$ grep install /var/log/dpkg.log | sort -r | head -n 5
2019-09-23 12:32:18 status installed initramfs-tools:all 0.131ubuntu19.1
2019-09-23 12:31:36 status installed linux-image-5.0.0-29-generic:amd64 5.0.0-29.31
2019-09-23 12:30:47 status installed plymouth-theme-ubuntu-text:amd64 0.9.4-1ubuntu1
2019-09-23 12:30:47 status installed man-db:amd64 2.8.5-2
2019-09-23 12:30:47 status installed dbus:amd64 1.12.12-1ubuntu1.1
```

**2. Utiliser dpkg et apt pour compter le nombre de paquets installés (ne pas hésiter à consulter le manuel !).
Comment explique-t-on la (petite) différence de comptage ?**

```
serveur@serveur:~$ dpkg -l | wc --lines
519
serveur@serveur:~$ apt list -i | wc -l

WARNING: apt does not have a stable CLI interface. Use with caution in scripts.

515
```

**3. Combien de paquets sont disponibles en téléchargement ?**

```
serveur@serveur:~$ apt list | wc -l

WARNING: apt does not have a stable CLI interface. Use with caution in scripts.

64118
```

**4. Créer un alias “maj” qui met à jour le système**

En allant dans le fichier .bashrc, ont remarque le script en bash suivant:

```
# Alias definitions.                                                                                 
# You may want to put all your additions into a separate file like                                  
# ~/.bash_aliases, instead of adding them here directly.
# See /usr/share/doc/bash-doc/examples in the bash-doc package.                                                                                                                                                 if [ -f ~/.bash_aliases ]; then                                                                             . ~/.bash_aliases
    fi 
```

J'ai donc créer le fichier `.bash_aliases` et après une recherche sur internet pour la syntax d'un alias j'ai écrit la ligne suivante:

```
alias maj='apt upgrade'
```

Une fois une déconnexion et reconnexion effectué, j'ai pu mettre à jour mon système avec l'alias maj mais j'ai du utiliser le sudo pour cela.

```
serveur@serveur:~$ sudo -s
root@serveur:~# maj
Lecture des listes de paquets... Fait
Construction de l'arbre des dépendances
Lecture des informations d'état... Fait
Calcul de la mise à jour... Fait
0 mis à jour, 0 nouvellement installés, 0 à enlever et 0 non mis à jour.
root@serveur:~#
```

**5. A quoi sert le paquet fortunes ? Installez-le.**

```
serveur@serveur:~$ serveur@serveur:~$ apt show fortunes
Description: Fichiers de données cqqontenant des biscuits chinois
 Il s’agit d’un programme affichant des épigrammes, connues sous le nom de
 cookies fortune, choisies aléatoirement à partir de fichiers fortune.
 .
 Il y a plus de 15 000 biscuits chinois différents dans ce paquet.
 .
 Vous aurez besoin du paquet fortune-mod pour afficher les biscuits
 chinois.
 ```
 
 Le paquet fortunes et un packet stimulant les biscuits de fortunes traditionelle chinois.
 

**6. Quels paquets proposent de jouer au sudoku ?**

```
serveur@serveur:~$ apt search sudoku
En train de trier... Fait
Recherche en texte intégral... Fait
fltk1.1-games/disco 1.1.10-26ubuntu1 amd64
  Boîte à outils Fast Light - exemples de jeux : jeux de dames, sudoku

fltk1.3-games/disco 1.3.4-9ubuntu1 amd64
  Boîte à outils Fast Light - exemples de jeux : jeux de dames, sudoku

gnome-sudoku/disco 1:3.32.0-1 amd64
  Casse-tête Sudoku pour GNOME

hitori/disco 3.31.0-1 amd64
  Jeu de puzzle logique similaire au sudoku

ksudoku/disco 4:18.12.3-0ubuntu1 amd64
  Jeu et Solveur de Sudoku

libqqwing-dev/disco 1.3.4-1.1 amd64
  outil pour générer et résoudre des casse-tête Sudoku (développement)

libqqwing2v5/disco 1.3.4-1.1 amd64
  outil pour générer et résoudre des casse-tête Sudoku (bibliothèque)

nudoku/disco 1.0.0-1 amd64
  ncurses based sudoku games

qqwing/disco 1.3.4-1.1 amd64
  tool for generating and solving Sudoku puzzles (application)

sudoku/disco 1.0.5-2build3 amd64
  Sudoku en mode console

texlive-games/disco 2018.20190227-1 all
  TeX Live : Composition de jeux
```

Il s'agit du packer `sudoku`

**7. Lister les derniers paquets installés explicitement avec la commande apt install**

```
serveur@serveur:~$ grep "apt install" /var/log/apt/history.log
Commandline: apt install fortunes
```

## Exercice 2.

**A partir de quel paquet est installée la commande ls ? Comment obtenir cette information en une seule
commande, pour n’importe quel programme (indice : la réponse est dans le poly de cours 2, dans la liste des
commandes utiles) ?** 

```
serveur@serveur: $ which -a ls | tail -n 1 | xargs dpkg -S 
coreutils: /bin/ls
```

**Utilisez la réponse à pour écrire un script appelé origine-commande (sans l’extension
.sh) prenant en argument le nom d’une commande, et indiquant quel paquet l’a installée.**

```
if [ -z "$1" ]; then
        echo "no argument, provide a command name."
else
        which -a "$1" | tail -n 1 | xargs dpkg -S
fi
```

## Exercice 3.

**Ecrire une commande qui affiche “INSTALLÉ” ou “NON INSTALLÉ” selon le nom et le statut du package
spécifié dans cette commande.**

```
(dpkg -l "fortunes" | grep "^i") && echo "installé" || echo "non installé"
```

## Exercice 4.

**Lister les programmes livrés avec coreutils. A quoi sert la commande "[" et comment afficher ce qu’elle retourne ?**

```
serveur@serveur:~$ dpkg -L coreutils | grep bin 
```

## Exercice 5. aptitude

**Installez le paquet emacs à l’aide de la version graphique d’aptitude.**

Pour des raisons logique, je ne vais pas copier/coller la totalité de l'interface d'aptutide.
Basiquement, j'ai utilisé la fonction de recher `/` pour rechercher emacs, puis j'ai utiliser la touche `+` pour accepter d'installer le packet, puis deux fois `g` pour comfirmer le téléchargement et lancer l'installation.



