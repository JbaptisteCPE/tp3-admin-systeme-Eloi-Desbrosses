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


## Exercice 6. Installation d’un paquet par PPA

### Le PPA est mort, l'installer ne fonctionns plus, il est nécessaire d'utiliser l'installer local qui nécessite de récupérer le .tar.gz depuis le site d'oracle qui lui-même nécessite un compte...

Certains logiciels ne figurent pas dans les dépôts officiels. C’est le cas par exemple de la version ”officielle” de Java depuis qu’elle est développée par Oracle. Dans ces cas, on peut parfois se tourner vers un ”dépôt personnel” ou PPA.

**1. Installer la version Oracle de Java (avec l’ajout des PPA)**
```
sudo add-apt-repository ppa:linuxuprising/java
sudo apt update
sudo apt install oracle-java11-installer
```

**2. Vérifiez qu’un nouveau fichier a été créé dans /etc/apt/sources.list.d. Que contient-il ?**

## Exercice 7. Création de dépôt personnalisé

### Pour je ne sais qu'elle raison, je n'arrive pas à signé mon dépot. Je ne peux donc pas installer le package.

Dans cet exercice, vous allez créer vos propres paquets et dépôts, ce qui vous permettra de gérer les
programmes que vous écrivez comme s’ils provenaient de dépôts officiels.
Création d’un paquet Debian avec dpkg-deb

**1. Dans le dossier scripts créé lors du TP 2, créez un sous-dossier origine-commande où vous créerez un sous-dossier DEBIAN, ainsi que l’arborescence usr/local/bin où vous placerez le script écrit à l’exercice 2**

**2. Dans le dossier DEBIAN, créez un fichier control avec les champs suivants :**

```
Package: origine-commande #nom du paquet
Version: 0.1 #numéro de version
Maintainer: Foo Bar #votre nom
Architecture: all #les architectures cibles de notre paquet (i386, amd64...)
Description: Cherche l'origine d'une commande
Section: utils #notre programme est un utilitaire
Priority: optional #ce n'est pas un paquet indispendable
```

**3. Revenez dans le dossier parent de origine-commande (normalement, c’est votre $HOME) et tapez la commande suivante pour construire le paquet :**

```
dpkg-deb --build origine-commande
```

Félicitations ! Vous avez créé votre propre paquet !

Création du dépôt personnel avec reprepro

**1. Dans votre dossier personnel, commencez par créer un dossier repo-cpe. Ce sera la racine de votre dépôt**

```
root@serveur:~# mkdir repo-cpe
```

**2. Ajoutez-y deux sous-dossiers : conf (qui contiendra la configuration du dépôt) et packages (qui contiendra nos paquets)**

```
root@serveur:~# mkdir repo-cpe/conf
root@serveur:~# mkdir repo-cpe/packages
```

**3. Dans conf, créez le fichier distributions suivant :**

```
Origin: Un nom, une URL, ou tout texte expliquant la provenance du dépôt
Label: Nom du dépôt
// Suite: stable
Codename: cosmic #le nom de la distribution cible
Architectures: i386 amd64 #(architectures cibles)
Components: universe #(correspond à notre cas)
Description: Une description du dépôt
```

**4. Dans le dossier repo-cpe, générez l’arborescence du dépôt avec la commande**

```
reprepro -b . export
```

Plusieurs dossiers tel que "db" ou "dists" sont apparus.

***5. Copiez le paquet origine-commande.deb créé précédemment dans le dossier packages du dépôt, puis, à la racine du dépôt, exécutez la commande**

```
reprepro -b . includedeb cosmic origine-commande.deb
```

afin que votre paquet soit inscrit dans le dépôt.

```
root@serveur:~# cp origine-commande.deb repo-cpe/packages/origine-commande.deb
```


***6. Il faut à présent indiquer à apt qu’il existe un nouveau dépôt dans lequel il peut trouver des logiciels. Pour cela, créez (avec sudo) dans le dossier /etc/apt/sources.list.d le fichier repo-cpe.list contenant :**

```
deb file:/home/VOTRE_NOM/repo-cpe cosmic multiverse
```

(cette ligne reprend la configuration du dépôt, elle est à adapter au besoin)

**7. Lancez la commande sudo apt update.
Féliciations ! Votre dépôt est désormais pris en compte ! ... Enfin, pas tout à fait... Si vous regardez la sortie d’apt update, il est précidé que le dépôt ne peut être pris en compte car il n’est pas signé. La signature permet de vérifier qu’un paquet provient bien du bon dépôt. On doit donc signer notre dépôt.**

Signature du dépôt avec GPG
GPG est la version GNU du protocole PGP (Pretty Good Privacy), qui permet d’échanger des données de
manière sécurisée. Ce système repose sur la notion de clés de chiffrement asymétriques (une clé publique et
une clé privée)

**1. Commencez par créer une nouvelle paire de clés avec la commande**

```
gpg --gen-key
```

Attention ! N’oubliez pas votre passphrase !!!

**2. Ajoutez à la configuration du dépôt (fichier distributions la ligne suivante :**

```
SignWith: yes
```

**3. Ajoutez la clé à votre dépôt :**

```
reprepro --ask-passphrase -b . export
```

Attention ! Cette méthode n’est pas sécurisée et obsolète ; dans un contexte professionnel, on utiliserait plutot un gpg-agent.

**4. Ajoutez votre clé publique à votre dépôt avec la commande**

```
gpg --export -a "auteur" > public.key
```

**5. Enfin, ajoutez cette clé à la liste des clés fiables connues de apt :**

```
sudo apt-key add public.key
```

Félicitations ! La configuration est (enfin) terminée ! Vérifiez que vous pouvez installer votre paquet comme n’importe quel autre paquet.

## Exercice 8. Installation d’un logiciel à partir du code source

### Lors de l'exécution du script config, j'ai une erreur: 

```
checking for gcc option to accept ISO C99... none needed
./configure: line 4091: syntax error near unexpected token `CAIRO,'
./configure: line 4091: `        PKG_CHECK_MODULES(CAIRO, cairo,, AC_MSG_ERROR(['
```
### Je n'arrive pas à en trouver la cause

Lorsqu’un logiciel n’est disponible ni dans les dépôts officiels, ni dans un PPA, ou encore parce qu’on souhaite n’installer qu’une partie de ses fonctionnalités, on peut se tourner vers la compilation du code source.

Malheureusement, cette installation ”à la main” fait qu’on ne propose pas des bénéfices de la gestion de paquets apportée par dpkg ou apt. Heureusement, il est possible de transformer un logiciel installé ”à la main” en un paquet, et de le gérer ensuite avec apt ; c’est ce que permet par exemple checkinstall.

**1. Commencez par cloner le dépôt git suivant :**

```
git clone https://github.com/jubalh/nudoku
```

Ceci permet de récupérer en local le code source du logiciel nudoku.

**2. Rendez vous dans le dossier nudoku qui vient d’être créé et lancez la commande autoreconf -i (ainsi que spécifié dans le fichier README.md). A vous d’installer les éventuels paquets manquants (un peu d’aide : pour résoudre le problème de la macro AM_GNU_GETTEXT manquante, installez le paquet gettext). Relancez la commande autoreconf -i après chaque paquet installé jusqu’à ce qu’elle se termine sans erreur.**

 Cette phase a pour but de traiter le fichier configure.ac fourni, de manière à générer automatiquement un script appelé configure (vous pouvez essayer de faire un cat sur ce fichier configure pour comprendre le bénéfice de tels outils).

**3. Exécutez le script configure**

 Cette phase a pour but de préparer la meilleure compialtion possible par rapport à la plateforme
cible, de faire des choix sur les dossier d’installation du logiciel, et de générer automatiquement les Makefiles adaptés à votre machine. Un Makefile est un fichier utilisé par l’outil make contenant toutes les directives de compilation d’un logiciel. Un Makefile définit un certain nombre de règles permettant de construire des cibles. Les cibles les plus communes étant install (pour la compilation et l’installation du logiciel) et clean (pour sa suppression).

**4. Normalement, à cette étape on exécute la commande make install, qui procède à la compilation
proprement dite et à l’installation (copie des fichiers compilés dans leur dossier de destination). Mais dans notre cas, on va demander à checkinstall de s’en charger et de créer un paquet au format .deb :**

```
sudo checkinstall
```

Le logiciel est à présent installé (exécutez nudoku pour vous en assurer) ; on peut vérifier par exemple avec aptitude qu’il provient bien du paquet qu’on a créé avec checkinstall.
