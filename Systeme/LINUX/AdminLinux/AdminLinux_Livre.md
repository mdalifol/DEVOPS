# ADMINISTRATION LINUX

## INSTALLATION

### DISTRIBUTIONS

Il existe de nombreuses distributions linux. Les plus utilisées sont _Debian, Ubuntu, RedHat, Suze et Fedora_.
Il ne faut pas oublier **Kali Linux** qui est spécialisée dans le pentest.

### OPTIONS DE DEMARRAGE

L'architecture de votre microprocesseur va initier certaines options.
On parle de 64 bits et 32 bits.
Les microprocesseurs possédant une architecture 64 bits sont capables de gérer des adresses mémoires plus importantes.

### LE PARTITIONNEMENT

Les disques durs actuels ne peuvent contenir que 4 partitions.
Pour lever cette limitation, on peut transformer chaque partition initiale (partition primaire) en une partition étendue. Elle se comportera comme un conteneur à partitions. Les partitions dans ce container sont en nombre illimité. Elles sont appelées **lecteurs logiques**.

Il est conseillé d'utiliser le format de disque _ext4_.

On utilise tout un disque avec _LVM chiffré_.
On a besoin a minima d'une partition **/**, une partition **/home** et d'un **swap**.

Le **swap** doit être au moins égal à 2 fois la taille de la RAM.
Le **/home** doit avoir au moins 20 Go.

* **/**
La partition racine qui contient la base du systeme. Si celui-ci est sous-dimensionné, le système ne pourra pas évoluer  (donc fonctionner)

* **/home**
Répertoire qui contient le répertoire de tous les utilisateurs (sauf root). Il doit être de taille conséquente pour accueillir tous les fichiers de tous les utilisateurs.

* **/tmp**
Répertoire contenant les données temporaires qui sont effacées à chaque redémarrage.

* **/usr**
Il possède sa propre arborescence (comme _/_). Il s'y trouve les informations qui ne sont pas nécessaire au fonctionnement minimal du système et qui peuvent être partagées entre plusieurs utilisateurs.

* **/var**
Il contient les _variables_, c'est-à-dire accessible en lecture/écriture.
On y trouve par exemples les logs (_/var/logs_), les boites aux lettres (_/var/mail_), les files d'attentes des services (_/var/spool_), les répertoires des applications web (_/var/www_).

* **/opt**
Répertoire utilisé dans l'installation manuelle d'applications. Quand une application n'est pas présente dans le système, c'est ici qu'il faut l'installer.

### LE SYSTEME D'AMORCAGE

**GRUB** signifie _GRand Unified Bootloader_.
Il identifie les disques durs dans le BIOS.
Le fichier **/boot/grub/device.map** contient la liste des disques détectés.
Pour modifier ce fichier il faut faire la commande:
  `grub-install /dev/sda`

Le fichier de config GRUB est **/boot/grub/grub.cfg**.
Il est mis à jour avec la commande:
  `update-grub`

Pour conserver ces modifications il faut modifier le fichier **/etc/default/grub**.
On retape ensuite la commande `update-grub`.

Il est possible de "customizer" ce grub avec diverses options. Pour cela voir une doc sur la configuration de GRUB.



## MODE CONSOLE

### STRUCTURATION DES FICHIERS

* /
  * bin
  * dev
  * home
    * user1
    * user2
  * lib
  * media
  * mnt
  * opt
  * root
  * tmp
  * var


* bin: commandes executables du systeme
* dev: fichiers de représentations des périphériques
* home: répertoire des utilisateurs (sauf root)
* lib: bibilothèques de fonctions utilisées par les programmes
* media: répertoire par lequel les amovibles (clé usb) sont accessibles
* mnt: répertoire similaire à _/media_ mais pour un montage manuel
* opt: programmes installés manuellement
* root: répertoire de l'administrateur
* tmp: fichiers temporaires
* var: contient les variables


### DROITS D'ACCES

* **-** ou **0**: pas de droits
* **r** ou **4**: lecture
* **w** ou **2**: écriture
* **x** ou **1**: exécute

  `ls -l`
permet de connaitre ces droits.

| type | utilisateur | groupe | autre |
|:---|:---:|:---:|---:|



### SHELL

Un _shell_ est un environnement dans lequel on peut écrire des commandes qui seront exécutées et renverront un résultat.

* _Variable d'Environnement_
Elles permettent de conserver et modifier des valeurs. Elles sont écrites en majuscule.
La variable **PATH** indique tous les chemins d'accès aux commandes du système.

Pour définir une variable d'environnement on utilise la commande `export`

ex: `export PATH=$PATH:/autre_chemin`
Les fichiers de "autre_chemin" seront maintenant executables.

On peut personnaliser le shell. pour cela il suffit de modifier les fichiers de configuration qui sont lus au démarrage.
* _/etc/profile_
* _~/.bash_profile_
* _~/.bashrc_
* _~/.bash_logout_

ex: dans _~/.bashrc_ on peut mettre des alias aux commandes
  `alias ll="ls -lisahG"`

Il faudra ensuite "mettre à jour" la variable _PATH_.
  `export PATH=$PATH:.`
où "." indique le répertoire courant.


### COMMANDES DE BASES

* `mkdir`: permet de créer un répertoire
Si on crée un répertoire où ses "parents" n'existent pas, l'option **-p** ou **--parents** permet de le faire.

ex: on a un dossier /test
on veut créer un dossier /test/monTest/test1.
Pour créer "monTest" en même temps n écrit la commande `mkdir -p /test/monTest/test1`

* `man`: permet d'afficher le manuel d'une commande.

* `file`: permet de connaitre des informations sur un fichier.

* `locate`: permet de connaitre l'emplacement du fichier. Il est nécessaire d'avoir fait un `updatedb`
avant afin d'avoir à jour la base de données "interne" qui référence tous les fichiers présents.

* **-exec**: permet d'executer une action sur un fichier.
ex: on efface le fichier trouvé "core"
  `find / -name "core" -exec rm {} \;`
pourquoi le ";" en fin de commande?
la commande _find_ va trouver 3 fichiers. Si on ne mets pas le ";", cela correspondrait à
  rm <fichier1> rm <fichier2> rm <fichier3>
Il essaierait donc de supprimer un fichier "_rm_" qui n'existe pas.
Avec le ";" cela correspond à:
  rm <fichier1> <fichier2> <fichier3>

### EDITEUR VI
_vi_ est un éditeur.
il a des commandes:

* **i**: permet d'inserer (écrire)
* **esc**: repasse en mode commande
* **:q!**: permet de sortir sans sauvegarder un fichier
* **:wq!**: permet de sortir en sauvegarder les modif du fichier
* **:w**: sauve sans sortir du fichier
* **:!<commandeShell>**: permet de lancer une commande shell dans le fichier
* **dd**: coupe la ligne au niveau du curseur
* **[n]dd**: coupe "n" lignes à partir de celle où se trouve le curseur
* **d0**: coupe les caractères de la ligne avant le curseur
* **d$**: coupe les caractères de la ligne après le curseur
* **yy**: copie la ligne où se trouve le curseur
* **yy**: copie n lignes à partir de celle où se trouve le curseur
* **y0**: copie les caractères de la ligne avant le curseur
* **y$**: copie les caractères de la ligne après le curseur
* **p**: colle ce qui est dans le presse papier
* **:m[n]**: déplace la ligne courante à la ligne [n]
* **u**: undo la dernière action
* **gg**: déplace le curseur sur la 1ere ligne du fichier
* **G**: déplace le curseur sur la dernière ligne du fichier
* **[n]G**: déplace le curseur sur la ligne [n] du fichier
* **zz**: met la ligne courante au milieu de la fenêtre
* **/[terme]**: recherche en avant de [terme] dans le fichier
* **?[terme]**: recherche en arrière de [terme] dans le fichier

On peut personnalisé _vi_ en modifiant son fichier **~/.virc**.

## ADMINISTRATION

### PAQUETS APT

**APT** _Advanced Package Tool_. Il gère aussi les dépendances.
Dans **/etc/apt** se trouve **sources.list** que l'on peut éditer en tant qu'admin.
Chaque ligne correspond à une source de données.
  - Le 1er champ est le type de la source.
  - 2e: chamin vers le dépot contenant les paquets
  - 3e: type de distribution ou nom du sous-répertoire permettant d'atteindre la source

A chaque modification de ce fichier il faudra lancer la commande, pour que les sources soient atteignables:
  `apt-get update`

#### Mise à jour système

voir la fichier [apt-get](./Commande_apt-get.md)

### GESTIONS DES UTILISATEURS

La liste des utilisateurs est stockées dans 1 fichier texte **/etc/passwd**.
Chaque ligne correspond à un utilisateur.

exemple: `root:*:0:0:System Administrator:/var/root:/bin/sh`
  - utilisateur (_root_)
  - mot de passe _x_ (contenu dans **/etc/shadow**)
  - uid: _0_ id unique de l'utilisateur
  - gid: _0_ id unique du groupe principal de l'utilisateur
  - nom complet de l'utilisateur _System Administrator_
  - repertoire perso de l'utilisateur _/var/_root_
  - interpréteur de commandes _/bin/sh_

Pour voir _/etc/shadow_ il faut être administrateur. Il **NE FAUT PAS** modifier ce fichier avec un éditeur de texte.


* `adduser <nomUtilisateur>` crée 1 utilisateur
* `adduser <nomUtilisateur> <nomGroupe>` crée 1 utilisateur et l'associe à 1 groupe
* `deluser <nomUtilisateur>` supprime 1 utilisateur
* `passwd <nomUtilisateur>` modifie le mot de passe de l'utilisateur
* `passwd -l <nomUtilisateur>`bloque 1 utilisateur (ex: absent pendant 1 période, on bloque le compte de l'utilisateur)
* `passwd -u <nomUtilisateur>` remet en service le compte utilisateur
* `chage -l <nomUtilisateur>` liste les conditions d'expirateur du mot de passe
* `chage -m <nbJoursMin> <nomUtilisateur>` definit le nb de jours minimum entre les changements de mot de passe
* `chage -M <nbJoursMin> <nomUtilisateur>` definit le nb de jours maximum
* `chage -E <YYY-MM-dd> <nomUtilisateur>` definit 1 date d'expiration
* `chage -i <nomUtilisateur>` réactive un compte ou supprime l'expiration

#### usermod

Commande qui modifie un compte utilisateur. Voir le manuel pour connaitre les options.

  `usermod <option> <login>`

#### chfn

Commande qui modifie le nom complet de l'utilisateur.

  `chfn [-f nom_complet] [-r no_bureau] [-w tel_bureau] [-h tel_perso] [-o autre] [utilisateur]`

#### Quota

On peut ajouter des quotas aux utilisateurs. Pour cela il faut ajouter le paquet _quota_.
  `apt-get install quota`
Ensuite il faut modifier le fichier **/etc/fstab**.

On a 2 choix:
* `usrquota`  quotas pour les utilisateurs
* `grpquota`  quotas pour les groupes

Exemple avant modif:
  `# <file system> <mount point>   <type>  <options>       <dump>  <pass>
    proc            /proc           proc    defaults        0       0
    # /dev/sda1 -- converted during upgrade to edgy
    UUID=8840ac3b-7209-4e00-a79e-d393de74e0ca / ext3 defaults,errors=remount-ro 0 1
    # /dev/sdb1 -- converted during upgrade to edgy
    UUID=af16a96e-6ecf-4083-9a77-b21fedf09e5d /home ext3 defaults 0 2
    # /dev/sda2 -- converted during upgrade to edgy
    UUID=6263979f-794c-43c8-a95b-b33627978928 none swap sw 0 0`

Exemple après modif:
  `# <file system> <mount point>   <type>  <options>       <dump>  <pass>
    proc            /proc           proc    defaults        0       0
    # /dev/sda1 -- converted during upgrade to edgy
    UUID=8840ac3b-7209-4e00-a79e-d393de74e0ca / ext3 defaults,errors=remount-ro 0 1
    # /dev/sdb1 -- converted during upgrade to edgy
    UUID=af16a96e-6ecf-4083-9a77-b21fedf09e5d /home ext3 defaults,usrquota,grpquota 0 2
    # /dev/sda2 -- converted during upgrade to edgy
    UUID=6263979f-794c-43c8-a95b-b33627978928 none swap sw 0 0`

Cette modification touche la partition _/home_.

Pour que ces modifications soient prises en compte il faut:

* remonter la partition
  `mount -o remount /home`

* initialiser le systeme de quotas (et crée les fichiers de quotas groupe et utilisateur s'ils n'existent pas)
  `quotacheck -cguvf /home`

* initialiser le systeme de gestion des quotas
  `quotacheck -vgum /home`

* active les quotas
  `quotaon -avug`

  * désactive les quotes
    `quotaoff -avug`

* fixe les quotas par groupe (_-g_) ou par utilisateur (_-u_)
  `edquota -u <nomUtilisateur>`

Cette commande ouvre l'editeur par defaut pour les quotas de l'utilisateur.
  `Disk quotas for user <nomUtilisateur> (uid 1006):
  Filesystem                   blocks       soft       hard     inodes     soft     hard
  /dev/sdb1                      9252       8192      10240          6        0        0`

  - _Filesystem_: système de fichiers concerné par les quotas
  - _blocks_: limite de la taille de dossier utilisateur
  - _soft_: limite à partir de laquelle l'utilisateur reçoit 1 avertissement
  - _hard_: limite qui ne pourra jamais être dépassée
  - _inodes_: limite de nombre d'inodes (fichiers)
  - _soft_: idem qu'avec les blocs mais pour les inodes
  - _hard_: idem qu'avec les blocs mais pour les inodes

Une fois ces quotas créés, on peut facilement les dupliquer:
  `edquota -p <nomUtilisateur> <nomUtilisateur2>`


#### Information sur les connexions utilisateurs

  `who` permet de connaitre qui est connecté.
  `last` permet de connaitre les dernières connexions (y compris les echecs)


### GESTION DES GROUPES

Très comparable aux utilisateurs.

* **/etc/group** est le fichier liste de groupes.

* `groupadd <nomGroupe>` crée un groupe
* `groupdel <nomGroupe>` supprime un groupe
* `passwd -g <nomGroupe>` modifie/active le mot de passe groupe
* `passwd -r -g <nomGroupe>` désactive le mot de passe du groupe
* `useradd -G <nomGroupe> <nomUtilisateur>` ajoute un utilisateur à 1 groupe (et le crée s'il n'existe pas)

### LES PROCESSUS

Un processus est un programme chargé en mémoire et en train d'être exécuté.
Chaque process à un ID unique (PID), un nom de propriétaire, une heure de démarrage, un PID du process parent, don état et la commande à laquelle il correspond.

` UID   PID  PPID        F CPU PRI NI       SZ    RSS WCHAN     S             ADDR TTY           TIME CMD
  501   275     1     4004   0   4  0  4376724    700 -      S                   0 ??         0:00.22 /System/Library/F
  501   276     1     4004   0   4  0  4351564   3216 -      S                   0 ??         0:08.22 /usr/sbin/cfprefs`

#### Etats des processus

* `R`   Running
* `S`   Sleeping
* `T`   Stopped
* `Z`   Zombie


#### Gestion des priorités

La commande `nice` permet de lancer une commande en y associant une priorité.
La notion de priorité est un nb allant entre -20 (la + haute) et 19 (la + faible).
  `nice –n 16 vmstat 1 &`
-> initialiser le processus vmstat avec une priorité basse (16)

Si un process a étét lancé sans ordre de priorité, il est possible d'en créer un a posteriori avec `renice`.
  `renice <Priorité> {-p <PID> | -g <GID> | -u <User>}`

#### Interrompre et/ou relancer un processus

La commande `kill`. Cette commande permet de stopper un process.

Les commandes `bg`ou `fg` permettent de mettre en arrière plan ou premier plan le processus qui est associé à la commande.

#### Suivre l'évolution d'un processus

La commande `top` permet de voir en direct l'évolution des processus.


### MONTAGE DE FS

#### Monter un périphérique

Pour connaitre le nom d'un péricphérique il faut aller voir dans **/var/log/messages**.

  `tail -f /var/log/messages`

Pour monter le volume (ex: le volume est _/dev/sb1_):
  `mount /dev/sb1 /media/cle_usb`
-> on monte le media "cle_usb" sur le volume /dev/sb1

* Monter à 1 format special `mount -t <format> <nomVolume> <nomMediaAMonter>` format: nfs, ext4....
  ex: au format _nfs_ `mount -t nfs /dev/sb1 /media/cle_usb`

* Monter images ISO `mount -o loop -t <imageISO> <nomVolume> <nomMediaAMonter>`

#### Démonter un périphérique

  `umount <nomMediaADemonter>`

S'il y a un problème pour démonter un media, c'est peut-être parce qu'un processus utilise le FS.
Pour connaitre les liste de ces process: `fuser -v <nomMediaADemonter>`

#### Monter un système distant

* `mount -t <format> <cheminNomSystemeDistant> <nomMediaAMonter>`

* Avec un identifiant et un mot de passe
    `mount -t <format> <cheminNomSystemeDistant> <nomMediaAMonter> -o username=<nomUser> password=<Mot2Passe>`

* Avec connexion ssh (il faut que sshfs soit installé)
  `sshfs <identifiant>@<adresseIPSystemeAConnecter:/ /mnt -p <numeroDePort>`

#### Fichier /etc/fstab

Pour les montages automatisés au démarrage, c'est renseigné dans le fichier **/etc/fstab**.

ex de fichier:
  `UUID=33b870b8-a81e-4203-a4fd-7affa9f412fb    /  ext4    defaults        0       2`
-> Il y a 6 champs:
    **_filesystem   mnt   type  options  dump  pass_**

* _filesystem_: nom du périphérique, reconnaissable avec son UUID _UUID=33b870b8-a81e-4203-a4fd-7affa9f412fb_
* _mnt_: point de montage dans l'arborescence _/_
* _type_: indique le type de système de fichiers ou l’algorithme utilisé pour l’interpréter _ext4_
* _options_: options de montage. _defaults_
* _dump_: utilisée par l'utilitaire dump pour décider quand faire des sauvegardes _0_
* _pass_: la priorité de vérification du système de fichiers par l'utilitaire fsck _2_
  * **0** pas de vérification
  * **1** systeme de fichier racine
  * **2** autres systemes de fichiers avec vérification


##### Options de montage

* **defaults** - paramètres de montage par défaut (équivalent à rw,suid,dev,exec,auto,nouser,async).
* **auto** - le système de fichiers sera monté automatiquement au démarrage, ou quand la commande 'mount -a' sera joué.
* _noauto_ - le système de fichiers est monté que quand on lui demande de le faire.
* _discard_ - Active la fonctionnalité TRIM sur un disque dur SSD
* _nofail_ - si la partition n'est pas disponible au démarrage, elle n'est pas montée et ne bloque pas le démarrage
* _rw_ - monte le système de fichiers en lecture et en écriture.
* _ro_ - monte le système de fichiers en lecture seulement.
* _relatime_ - mettre à jour la date d'accès sur l'inode par rapport au modification ou au changement de date.
* _noatime_ - ne pas mettre à jour la date d'accès sur l'inode pour le système de fichier
* _user_ - permet à n'importe quel utilisateur de monter le système de fichiers (cela implique noexec,nosuid,nodev).
* _nouser_ - autorise seulement le compte root à monter le fichier système (par défaut).
* _sync_ - Les entrées/sorties (I/O) devraient être faites de manière synchrone.
* _async_ - Les entrées/sorties (I/O) devraient être faites de manière asynchrone.
* _suid_ - autorise les opérations sur les bits suid et sgid. Le plus souvent cela permet d'autoriser un utilisateur sur un ordinateur à exécuter un binaire avec une élévation temporaire des privilèges dans le but d'effectuer une tache spécifique.
* _nosuid_ - bloque les opérations sur les bits suid et sgid.
* _exec_ - autorise l'exécution de binaire qui sont sur cette partition (par défaut).
* _noexec_ - n'autorise pas l'exécution de binaires sur le système de fichier.
* _acl_- autorise la gestion des acl sur cette partition.

Pour que les modifications su fichier _/etc/fstab_ soient prises en compte, il faudra soit redémarrer soit faire un re démarrage du FS `mount -a`

### COMMANDES DE DEPANNAGE

#### Commandes Reseau de base

* **ifconfig** - indique les informations sur l'ensemble de la carte reseau
  * _inet adr_ indqiue l'adresse IP de la machine.

* **wificonfig** - indique les informations sur la connexion wifi mais permet également de modifier l'association à un reseau wifi.

#### Fichiers de configuration

* **/etc/hosts** - fichier contenant la liste des correspondances entre les adresses IP et les machines

* **/etc/network** - fichier contenant les associations entre adresses IP et réseau

* **/etc/network/interfaces** - liste de l'ensemble des interfaces réseau du systeme avec adresses IP et masques associés

* **/etc/resolv.conf** - configuration de la résolution DNS

* **/etc/services** - liste de tous les services réseau du systeme

* **/etc/protocols** - liste des protocoles connus du systeme

### CONNAITRE SON MATERIEL

#### Connaitre le systeme

* **uname -r** - permet de connaitre la version du noyau installé

* **uname -a** - permet de connaitre le nom de la machine, le type de processeur


#### Connaitre le materiel

Le répertoire **/proc** contient les fichiers spéciaux contenant les informations sur le systeme classé par type.

* _/proc/cpuinfo_ informations sur le(s) processeur(s)

* _/proc/modules_ liste les modules chargés dans le noyau

* _/proc/meminfo_ informations sur l'utilisation de la mémoire vive

* _free_ renvoie des informations sur la quantité de RAM et de SWAP
  `free -m` affiche tous les résultats en Mo

### PARTAGE DE FICHIERS

#### Outils **SAMBA**

Il y a 2 outils samba: **client** et **server**.

* **server** : **smbd** est le démon qui gère le partage des fichiers et des imprimantes. Il dialogue avec les clients qui se connectent. Il utilise la commande `service`.
  `service samba start`
  `service samba stop`
  `service samba restart`
  `service samba status`

* **client** : **smbclient** est le client qui permet de se connecter à un serveur samba.
  `smbclient -L <adresseServer> -U <nomUser>` : liste les partages disponibles sur 1 serveur pour 1 user
  `smbclient //<nomServer>/<nomRessource> -U <nomUser>` : pour se connecter à 1 ressource

##### Gestion des Utilisateurs

 `smbpasswd -a <nomUser>` : ajout d'1 nouvel user
 `smbpasswd -x <nomUser>` : suppression d'1 user
 `smbpasswd -d <nomUser>` : desactivation d'1 user
 `smbpasswd -e <nomUser>` : activation d'1 user (après l'avoir désactivé)

##### Outils de vérification

Il existe 2 commandes pour faire des tests sur les fichiers de config samba:

* `testparm`: vérifier si le fichier **/etc/samba/smb.conf** est correct

* `smbstatus` : indique la liste des clients connectés au serveur
