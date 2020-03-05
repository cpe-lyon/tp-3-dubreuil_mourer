# TP3 Administration Système - Gestion de paquets
Pierre Dubreuil  
Lucie Mourer 

28/02/2020

# Exercice 1:  Commandes de base


Mettre à jour le système : `sudo apt upgrade`

## 1. Quels sont les 5 derniers paquets installés sur votre machine ?

Commande
```bash
grep  " install " /var/log/dpkg.log | tail -5
```

Réponse
```bash
2020-03-04 13:09:04 install php7.2-curl:amd64 <none> 7.2.24-0ubuntu0.18.04.3
2020-03-04 13:09:07 install php-curl:all <none> 1:7.2+60ubuntu1
2020-03-04 13:13:24 install libzip4:amd64 <none> 1.1.2-1.1
2020-03-04 13:13:26 install php7.2-zip:amd64 <none> 7.2.24-0ubuntu0.18.04.3
2020-03-04 13:13:30 install php-zip:all <none> 1:7.2+60ubuntu1
```


## 2. Utiliser `dpkg` et `apt`pour compter le nombre de paquets installés ( ne pas hésiter à consulter le manuel ! ). Comment explique-t-on la (petite) différence de comptage ?

Commande
``` bash
apt list --installed | wc -l 
```

Réponse
```bash
286
```

Commande
```bash 
dpkg -l | wc -l 
```

Réponse
```bash
290
```


`dpkg` affiche ses résultats sous forme de tableau, il ajoute des lignes au début de son affichage d'où le nombre de ligne supérieur par rapport à `apt`.

## 3. Combien de paquets sont disponibles en téléchargement ?

Commande
```bash
apt list | wc -l 
```
Réponse
```bash
65249
```

## 4. Créer un alias “maj” qui met à jour le système

``` bash
nano ~/.bash_aliases
```
``` bash
alias maj = 'sudo apt update && sudo apt full-upgrade -y'
```
``` bash
source ~/.bashrc
```


## 5. A quoi sert le paquet fortunes? Installez-le.

Pour savoir ce que fait le paquet fortunes on utilise la commande `apt show fortune`

```bash
Description: Data files containing fortune cookies
 The fortune program displays epigrams, known as fortune cookies,
 selected randomly from a selection of fortune files.
 .
 There are far over 15000 different cookies in this package.
 .
 You'll need the fortune-mod package to display the cookies.
```

Il sert donc à afficher des citation dans le terminal à la manière des biscuits chinois.

Installation:
```bash
sudo apt install fortune
```

## 6. Quels paquets proposent de jouer au sudoku ?

Commande
```bash
apt search sudoku
```

Réponse
* xenial
* bionic
* disco
* eoan
* focal

## 7. Lister les derniers paquets installés explicitement avec la commande `apt install`

```bash
grep "apt install" /var/log/apt/history.log
```

 La différence avec la question 1 est que on affiche juste ce qui a été installé avec le `apt install` 

&nbsp;

# Exercice 2

_A partir de quel paquet est installée la commande ls ? Comment obtenir cette information en une seule commande, pour n’importe quel programme (indice : la réponse est dans le poly de cours 2, dans la liste des commandes utiles) ?   
Utilisez la réponse pour écrire un script appelé origine-commande (sans l’extension .sh) prenant en argument le nom d’une commande, et indiquant quel paquet l’a installée._

Commande
```bash
dpkg -S $(which ls)
```

Réponse
```bash
coreutils: /bin/ls
```
&nbsp;
```bash
nano ~/script/origine-commande
```
```bash
#!/bin/bash
if [ -z "$1" ] ; then  #-z permet de vérifier si le contenu de $1 est vide
    echo "Utilisation: ${0##*/} <commande>"  # cette instruction permet de réinformer sur l'utilisation si on ne donne pas de paramètre
else  
    echo dpkg -S $(which $1) # on éxécute la commande d'information sur le packet d'origine
fi  
```

# Exercice 3

_Ecrire une commande qui aﬀiche “INSTALLÉ” ou “NON INSTALLÉ” selon le nom et le statut du package spécifié dans cette commande._  



```bash
nano ~/script/installe-oupas

#!/bin/bash
if [ -z "$1" ] ; then  #-z permet de vérifier si le contenu de $1 est vide
    echo "Utilisation: ${0##*/} <package>"  # cette instruction permet de réinformer sur l'utilisation si on ne donne pas de paramètre
else  
    installed=$(dpkg-query -W -f='${Status}\n' $1 2> /dev/null)
    if [ $installed = "install ok installed" ] ; then #on regarde le status de la commande entrée par l'utilisateur
        echo "INSTALLÉ" 
    else 
        echo "NON INSTALLÉ"
    fi
fi  
```
 Sur une chaine de caractère pour faire une comparaison on utilise `=`

&nbsp;

# Exercice 4 

_Lister les programmes livrés avec coreutils. A quoi sert la commande ’[’ et comment aﬀicher ce qu’elle retourne ?_

Commande
```bash
show coreutils 
``` 

Réponse
 ```bash
  Specifically, this package includes:
 arch base64 basename cat chcon chgrp chmod chown chroot cksum comm cp
 csplit cut date dd df dir dircolors dirname du echo env expand expr
 factor false flock fmt fold groups head hostid id install join link ln
 logname ls md5sum mkdir mkfifo mknod mktemp mv nice nl nohup nproc numfmt
 od paste pathchk pinky pr printenv printf ptx pwd readlink realpath rm
 rmdir runcon sha*sum seq shred sleep sort split stat stty sum sync tac
 tail tee test timeout touch tr true truncate tsort tty uname unexpand
 uniq unlink users vdir wc who whoami yes
 ```

 `[` est une commande permettant de réaliser des comparaisons sur les valeurs et de comparer le type des fichiers

 On affiche son resultat avec la commande `echo $[.........]`

&nbsp;

# Exercice 5 Aptitude

_Installez le paquet emacs à l’aide de la version graphique d’aptitude._

On a suivi les instructions du cours `aptitude`  
Recherche via la touche `/` puis on tape `emac`
on le selectionne avec `+` puis on appuie 2 fois sur `g` pour installer.

&nbsp;

# Exercice 6 Installation d’un paquet par PPA

## 1. Installer la version Oracle de Java (avec l’ajout des PPA)

```bash
sudo add-apt-repository ppa:linuxuprising/java 
sudo apt update
sudo apt install oracle-java11-installer
```

## 2. Vérifiez qu’un nouveau fichier a été créé dans /etc/apt/sources.list.d. Que contient-il ?

Commande
```bash
cat /etc/apt/sources.list.d/linuxuprising-ubuntu-java-bionic.list
```

Réponse
```bash
deb http://ppa.launchpad.net/linuxuprising/java/ubuntu bionic main                                                      # deb-src http://ppa.launchpad.net/linuxuprising/java/ubuntu bionic main 
```

&nbsp;

# Exercice 7 Création de dépôt personnalisé

## Création d’un paquet Debian avec dpkg-deb

### 1. _Dans le dossier scripts créé lors du TP 2, créez un sous-dossier origine-commande où vous créerez un sous-dossier DEBIAN, ainsi que l’arborescence usr/local/bin où vous placerez le script écrit à l’exercice 2._

```bash
mkdir origine-commande
cd origine-commande/
mkdir DEBIAN
cd DEBIAN/
mkdir usr/local/bin -p  # -p sert à créer l'arborescence
```


### 2. Dans le dossier DEBIAN, créez un fichier control avec les champs suivants :

```
Package: origine-commande   #nom du paquet
Version: 0.1                #numéro de version
Maintainer: Foo Bar         #votre nom
Architecture: all           #les architectures cibles de notre paquet (i386, amd64...)
Description: Cherche l'origine d'une commande
Section: utils              #notre programme est un utilitaire
Priority: optional          #ce n'est pas un paquet indispendable
```

### 3. Revenez dans le dossier parent deorigine-commande(normalement, c’est votre `$HOME`) et tapez la commande suivante pour construire le paquet :

```bash
dpkg-deb --build 
origine-commande
```

## Création du dépôt personnel avec reprepro

### 1. Dans votre dossier personnel, commencez par créer un dossier repo-cpe. Ce sera la racine de votre dépôt

```bash
mkdit repo-cpe
cd repo-cpe/
```

### 2. Ajoutez-y deux sous-dossiers: conf (qui contiendra la configuration du dépôt) et packages (qui contiendra nos paquets)
```
mkdir conf 
mkdir package
```

### 4. Dans le dossier repo-cpe, générez l’arborescence du dépôt avec la commande 

```bash
reprepro -b . export
```

### 5. Copiez le paquet origine-commande.deb créé précédemment dans le dossier packages du dépôt, puis, à la racine du dépôt, exécutez la commande

```bash 
reprepro -b . includedeb cosmic origine-commande.deb
```

afin que votre paquet soit inscrit dans le dépôt.


### 6. Il faut à présent indiquer à apt qu’il existe un nouveau dépôt dans lequel il peut trouver des logiciels. Pour cela, créez (avec sudo) dans le dossier `/etc/apt/sources.list.d` le fichier `repo-cpe.list` contenant : 

```bash
deb file:/home/VOTRE_NOM/repo-cpe cosmic multiverse
``` 

(cette ligne reprend la configuration du dépôt, elle est à adapter au besoin)

### 7. Lancez la commande `sudo apt update`


## Signature du dépôt avec GPG 

_GPG est la version GNU du protocole PGP (Pretty Good Privacy), qui permet d’échanger des données de manière sécurisée. Ce système repose sur la notion de clés de chiffrement asymétriques (une clé publique et une clé privée)_

### 1. Commencez par créer une nouvelle paire de clés avec la commande

```bash
gpg --gen-key
```

_Attention à passphrase !!!_

### 2. Ajoutez à la configuration du dépôt (fichier distributions la ligne suivante :

```
SignWith: yes
```

### 3. Ajoutez la clé à votre dépôt :

```bash
reprepro --ask-passphrase -b . export
```

_Attention ! Cette méthode n’est pas sécurisée et obsolète ; dans un contexte professionnel, on utiliserait plutot un `gpg-agent`._

### 4. Ajoutez votre clé publique à votre dépôt avec la commande

```bash
gpg --export -a "auteur" > public.key
```

### 5. Enfin, ajoutez cette clé à la liste des clés fiables connues de apt :

```bash
sudo apt-key add public.key
```

# Exercice 8 :  Installation d’un logiciel à partir du code source

_Lorsqu’un logiciel n’est disponible ni dans les dépôts officiels, ni dans un PPA, ou encore parce qu’on
souhaite n’installer qu’une partie de ses fonctionnalités, on peut se tourner vers la compilation du code
source.
Malheureusement, cette installation ”à la main” fait qu’on ne propose pas des bénéfices de la gestion de
paquets apportée par dpkg ou apt. Heureusement, il est possible de transformer un logiciel installé ”à la
main” en un paquet, et de le gérer ensuite avec apt ; c’est ce que permet par exemple checkinstall._

## 1. Commencez par cloner le dépôt git suivant :

```bash
git clone https://github.com/jubalh/nudoku
```

**Ceci permet de récupérer en local le code source du logiciel nudoku.**

## 2. Rendez vous dans le dossier nudoku qui vient d’être créé et lancez la commande autoreconf -i (ainsi que spécifié dans le fichier README.md). 
A vous d’installer les éventuels paquets manquants (un peu d’aide : pour résoudre le problème de la macro AM_GNU_GETTEXT manquante, installez le paquet gettext). Relancez la commande `autoreconf -i` après chaque paquet installé jusqu’à ce qu’elle se termine sans erreur.

Cette phase a pour but de traiter le fichierconfigure.acfourni, de manière à générer automatique-ment un script appeléconfigure(vous pouvez essayer de faire uncatsur ce fichier configure pour comprendre le bénéfice de tels outils).

## 3. Exécutez le script configure

## 4. Normalement, à cette étape on exécute la commande make install, qui procède à la compilation proprement dite et à l’installation (copie des fichiers compilés dans leur dossier de destination). Mais dans notre cas, on va demander à checkinstall de s’en charger et de créer un paquet au format .deb : sudo checkinstall Le logiciel est à présent installé (exécutez nudoku pour vous en assurer) ; on peut vérifier par exemple avec aptitude qu’il provient bien du paquet qu’on a créé avec checkinstall.

```bash
sudo checkinstall
```