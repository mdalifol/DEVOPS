########## SERVEUR HEBERGEMENT MULTIUSERS ##########


## DEFINITIONS

    Installer un serveur Linux qui permette l'hébergement de plusieurs utilisateurs. Chaque utilisateur aura un accès FTP, un dossier personnel, et un dossier web.
    On n'utilise pas d'environnement graphique. Tout se fait en console.


    Il faut être root pour efectuer les opérations suivantes.       -> su - root            => debian
                                                                    -> sudo -s              => ubuntu



        Assurez-vous d'avoir un fichier /etc/apt/sources.list bien configuré et un système à jour !    

                Le fichier sources.list contient la liste des dépôts où se trouvent les applications que le système utilise ou que vous utilisez !


                    =>  aptitude update
                        aptitude full-upgrade

                -> Il est possible que le noyau soit mis à jour également. Dans ce cas-là, il faut redémarrer pour charger le nouveau noyau

                    =>  reboot




## Installer le serveur web : Apache 2            


        Installer le serveur web            => aptitude install apache2

        Tester le serveur web apache:
            -> Dans un navigateur web on lance l'url:   http://192.168.0.100
                qui est redirigée par defaut vers       http://192.168.0.100/apache2-defaut

        On va dans le fichier de config:
                => nano /etc/apache2/sites-available/default

                => On ajoute un dièse # au début de la ligne RedirectMatch ^/$ /apache2-default/ pour la commenter    

                => On redémarre apache              => /etc/init.d/apache2 restart

        On retape http://192.168.0.100 dans le navigateur
                -> on a une arborescence qui contient uniquement le dossier apache2-default

        Ce dossier étant inutile, on va le supprimer.

            Par défaut, Apache va chercher les pages dans /var/www/:

                    rm -r /var/www/apache2-default

        => Apache 2 fonctionne        



## Configurer Apache 2 avec le mod UserDir


        Le mode UserDir est un mode installé par défaut avec Apache 2. Il permet, une fois configuré, de faire correspondre une URL de type http://192.168.0.100/~utilisateur à un dossier /home/utilisateur
        L'avantage est qu'il ne faut pas changer la configuration d'Apache 2 à chaque fois qu'on ajoute un utilisateur : à chaque fois qu'une URL comme la précédente sera appelée, Apache 2 vérifiera si le dossier correspondant existe sur le serveur. S'il existe, on accèdera à son contenu, sinon, on obtiendra l'erreur 404.

        =>
            Activons le mod                 =>  a2enmod userdir
                            -> Un message nous avertit qu'il faut recharger la configuration d'Apache, mais comme nous allons la modifier par la suite, nous la rechargerons plus tard

            Configurer ce mod               => fichier /etc/apache2/mods-available/userdir.conf                
                            -> UserDir public_html signifie que l'adresse http://192.168.0.100/~utilisateur affichera le contenu du dossier [Répertoire home de utilisateur]/public_html. L'utilisateur doit donc être un utilisateur système valide
                            ==> Pour ne pas avoir à créer à chaque fois un utilisateur système, on peut remplacer cette ligne par UserDir /home/*/public_html. Du coup, il suffit juste de créer un dossier du nom de l'utilisateur dans /home
** (pour supprimer la couleur rose dans atom)

                            -> UserDir disabled [noms d'utilisateurs] permet de désactiver la fonctionnalité UserDir pour certains utilisateurs
                                ==> root par defaut l'est

                            -> Le reste du fichier de configuration sert à définir les options sur les dossiers des utilisateurs


                        => version du fichier optimisé pour nos besoins:


                                                    <IfModule mod_userdir.c>
                                                            UserDir /home/*/public_html
                                                     
                                                            <Directory /home/*/public_html>
                                                                    AllowOverride FileInfo AuthConfig Limit
                                                                    Options MultiViews Indexes IncludesNoExec
                                                                    IndexOptions FoldersFirst FancyIndexing IgnoreCase
                                                            </Directory>
                                                    </IfModule>                
                            REMARQUE: Les 3 IndexOptions ajoutées permettent un affichage plus sympathique de la liste des fichiers

              Recharger la configuration d'Apache 2 et tester           => /etc/init.d/apache2 reload

                        Pour tester:

                                    mkdir /home/test/
                                    mkdir /home/test/public_html
                                                                        Puis on accède à l'adresse http://192.168.0.100/~test

                                REMARQUE: ne pas oublier d'effacer une fois les tests validés:
                                    => rm -r /home/test


            => À ce stade, nous pouvons donc créer un utilisateur web en créant 2 dossiers :

                            - le dossier /home/utilisateur qui sera la partie privée de l'utilisateur ;
                            - le dossier /home/utilisateur/public_html qui sera la partie publique accessible avec l'adresse http://192.168.0.100/~utilisateur.


## Installer PHP5


        On installe php5                =>  aptitude install php5

        On redemarrage apache           =>  /etc/init.d/apache2 restart

                    -> Vous allez vous apercevoir qu'il est parfaitement possible pour un utilisateur d'écrire un script PHP qui va voir chez le voisin... ce qui pose un gros problème de sécurité !
                    --> Nous allons donc devoir enfermer le PHP de chaque utilisateur dans son dossier respectif. Pour cela, il suffit d'ajouter une petite ligne de rien du tout dans notre fichier /etc/apache2/mods-available/userdir.conf.

                        =>
                                                    <IfModule mod_userdir.c>
                                                            UserDir /home/*/public_html
                                                     
                                                            <Directory /home/*/public_html>
                                                                    AllowOverride FileInfo AuthConfig Limit
                                                                    Options MultiViews Indexes IncludesNoExec
                                                                    IndexOptions FoldersFirst FancyIndexing IgnoreCase
                                                                    php_admin_value open_basedir ".." # <-- voici la nouvelle ligne
                                                            </Directory>
                                                    </IfModule>                        

                            - php_admin_value permet de modifier des options de php.ini directement dans la configuration d'Apache 2
                            - open_basedir permet d'enfermer PHP dans un répertoire. À chaque fois qu'un fichier ou dossier sera ouvert en PHP, on obtiendra un message d'erreur si celui-ci n'est pas à l'intérieur du dossier spécifié
                            - ".." est la valeur de l'option open_basedir. Les 2 points correspondent au dossier parent, c'est-à-dire dans notre cas au dossier /home/utilisateur.

        La fonction PHP shell_exec() permet d'exécuter des commandes shell --> Il est donc possible de créer un script PHP dans son répertoire d'utilisateur web
        => On a donc accès à la liste des utilisateurs

        PHP est doté une fonction, le safe_mode, qui permet d'interdire les fonctions de ce type. Notre fichier /etc/apache2/mods-available/userdir.conf

            =>

                                                    <IfModule mod_userdir.c>
                                                            UserDir /home/*/public_html
                                                     
                                                            <Directory /home/*/public_html>
                                                                    AllowOverride FileInfo AuthConfig Limit
                                                                    Options MultiViews Indexes IncludesNoExec
                                                                    IndexOptions FoldersFirst FancyIndexing IgnoreCase
                                                                    php_admin_value safe_mode "1"
                                                                    php_admin_value open_basedir ".."
                                                            </Directory>
                                                    </IfModule>

                    => Nos utilisateurs peuvent mettre du PHP dans leur dossier public_html sans pour autant poser de problèmes de sécurité.


## Installer le serveur de base de données : MySQL


        On installe MySQL pour que les utilisateurs puissent accéder à une base de données:

                => aptitude install mysql-server php5-mysql

        On exécute dans la foulée le script fourni avec MySQL qui permet de sécuriser la configuration de ce dernier:

                => mysql_secure_installation

        On installe PHPMyAdmin:

                => aptitude install phpmyadmin

            -> il vous sera demandé quel est le serveur web ! Choisir apache2 avec les flèches directionnelles, cocher avec Espace, et valider avec Entrée

        Allez ensuite avec un navigateur à l'adresse http://192.168.0.100/phpmyadmin. Mettez la langue en français, le nom d'utilisateur (root) et votre mot de passe MySQL.


        Direction la page Privilèges, puis Ajouter un utilisateur. Indiquez ftp comme nom d'utilisateur, sélectionnez Local dans la liste déroulante Serveur, et indiquez un mot de passe, compliqué si possible, car il ne sera utilisé que par des scripts.
        Cochez le bouton radio Créer une base portant son nom et donner à cet utilisateur tous les privilèges sur cette base et validez.
        Retournez sur la page Privilèges et cliquez sur recharger les privilèges.
        MySQL est maintenant installé, configuré, et nous avons créé un utilisateur et une base de données pour le serveur FTP...



## Installer le serveur FTP : vsFTPd


        On installe vsFTPd et le module d'authentification par MySQL:

                => aptitude install vsftpd libpam-mysql

        Remplaçons le fichier /etc/vsftpd.conf par:

                                                            # Ceci configure VSFTPd en mode "standalone"
                                                            listen=YES

                                                            # On désactive les connexions anonymes et on active les non-anonymes (c'est le cas des utilisateurs virtuels) :
                                                            anonymous_enable=NO
                                                            local_enable=YES

                                                            # Pour des raisons de sécurité, on interdit toute action d'écriture :
                                                            write_enable=NO
                                                            anon_upload_enable=NO
                                                            anon_mkdir_write_enable=NO
                                                            anon_other_write_enable=NO

                                                            # "guest_enable" active les utilisateurs virtuels
                                                            # "guest_username" fait correspondre tous les utilisateurs virtuels à l'utilisateur d'Apache 2
                                                            guest_enable=YES
                                                            guest_username=www-data

                                                            # On définit les droits par défaut des fichiers uploadés
                                                            anon_umask=022

                                                            # On enferme les utilisateurs virtuels dans leur dossier
                                                            chroot_local_user=YES

                                                            # On définit le nombre maximum de sessions à 100
                                                            # On définit le nombre maximum de sessions par IP à 5
                                                            max_clients=100
                                                            max_per_ip=5

                                                            ####################################
                                                            # Debian customization             #
                                                            # (ou adoptons la Debian attitude) #
                                                            ####################################
                                                            # Some of vsftpd's settings don't fit the Debian filesystem layout by
                                                            # default.  These settings are more Debian-friendly.
                                                            #
                                                            # This option should be the name of a directory which is empty.  Also, the
                                                            # directory should not be writable by the ftp user. This directory is used
                                                            # as a secure chroot() jail at times vsftpd does not require filesystem
                                                            # access.
                                                            secure_chroot_dir=/var/run/vsftpd
                                                            #
                                                            # This string is the name of the PAM service vsftpd will use.
                                                            pam_service_name=vsftpd
                                                            #
                                                            # This option specifies the location of the RSA certificate to use for SSL
                                                            # encrypted connections.
                                                            rsa_cert_file=/etc/ssl/certs/vsftpd.pem


                                                            # Permet d'utiliser les configurations individuelles pour chaque utilisateur
                                                            user_config_dir=/etc/vsftpd

                                                            # Définir la plage de ports utilisée par le mode passif
                                                            pasv_min_port=20000
                                                            pasv_max_port=20050

                                                            # Message d'accueil
                                                            ftpd_banner=Bienvenue sur ce serveur FTP !

                                                            # Autoriser les utilisateurs virtuels à changer les permissions de leurs fichiers
                                                            chmod_enable=YES
                                                            virtual_use_local_privs=YES

                                                            # Utiliser le temps local et non pas le temps universel (UTC)
                                                            use_localtime=YES

                                                            # Activation du log
                                                            xferlog_enable=YES
                                                            log_ftp_protocol=YES


        Il faut créer le dossier qui contiendra les fichiers de configuration pour chaque utilisateur

            => mkdir /etc/vsftpd


## Configurer vsFTPd avec pam-mysql


        vsFTPd va devoir accéder à la liste des utilisateurs, qui sera placée dans notre base de donnée MySQL.
        -> Le module pam-mysql permet de faire ça.

        Aller sur PHPMyAdmin, se logger avec l'utilisateur ftp, sélectionner la base de donnée ftp dans le menu de gauche.
        Dans l'onglet SQL, coller le code suivant:

                                        CREATE TABLE `ftp`.`users` (
                                        `name` TEXT NOT NULL ,
                                         `pass` TEXT NOT NULL
                                        ) ENGINE = MYISAM        


        Notre table étant créée, on va remplacer le fichier /etc/pam.d/vsftpd par le suivant (qui contient les informations d'accès à cette table) :

                                            auth required pam_mysql.so host=localhost user=ftp passwd=votre_pass db=ftp table=users usercolumn=name passwdcolumn=pass crypt=1
                                            account required pam_mysql.so host=localhost user=ftp passwd=votre_pass db=ftp table=users usercolumn=name passwdcolumn=pass crypt=1

                REMARQUE: N'oubliez pas de remplacer le mot de passe de l'utilisateur MySQL ftp (2 fois) dans ce fichier


        On redemarre le serveur ftp             => /etc/init.d/vsftpd restart

        On va maintenant ajouter un utilisateur pour tester:

            -> PHPMyAdmin (toujours avec l'utilisateur ftp)
            -> ajouter un nouvel enregistrement à notre table
            -> renseigner un nom d'utilisateur et un mot de passe
                        -> Le mot de passe doit être crypté ! Il suffit de sélectionner ENCRYPT dans la liste déroulante, lors de l'insertion.
            -> créer le fichier de configuration de l'utilisateur : /etc/vsftpd/[nom d'utilisateur]


                                            anon_world_readable_only=NO
                                            local_root=/home/utilisateur
                                            write_enable=YES
                                            anon_upload_enable=YES
                                            anon_mkdir_write_enable=YES
                                            anon_other_write_enable=YES
                                            hide_file=(none)
                                            force_dot_files=YES


                REMARQUE: voir le lien pour la config   http://vsftpd.beasts.org/vsftpd_conf.html

                    ---> on a définit le dossier dans lequel l'utilisateur est enfermé, on l'autorise à y écrire, et on lui permet de voir les fichiers cachés

            -> on crée le dossier de l'utilisateur (avec les bons droits !)

                                        mkdir /home/[nom d'utilisateur]
                                        chown -R www-data.www-data /home/[nom d'utilisateur]
                                        chmod -R 700 /home/[nom d'utilisateur]


        Ontente une connexion au serveur ftp.



## Les scripts de gestion des comptes


        On va maintenant créer des scripts qui permettent d'ajouter / d'enlever des utilisateurs à notre place.


        1. liste des opérations à faire lors de l'ajout d'un utilisateur
                    - demander les identifiants ;
                    - vérifier si un utilisateur portant ce nom existe déjà ;
                    - créer les dossiers /home/[nom_utilisateur] et /home/[nom_utilisateur]/public_html ;
                    - régler le propriétaire et les droits du dossier /home/[nom_utilisateur] et de son contenu (www-data / 700) ;
                    - créer le fichier de configuration /etc/vsftpd/[nom_utilisateur] et le remplir selon le modèle précédemment vu (seule la ligne indiquant le dossier de l'utilisateur change) ;
                    - ajouter les identifiants de l'utilisateur dans la base de données ;
                    - créer un utilisateur MySQL (avec les identifiants fournis) et une base de données associée.        


        2. liste des opérations à faire lors de la suppression d'un utilisateur
                    - demander les identifiants ;
                    - vérifier si un utilisateur portant ce nom existe bien ;
                    - supprimer les identifiants de l'utilisateur dans la base de données ;
                    - supprimer l'utilisateur MySQL et la base de données associée ;
                    - supprimer le fichier de configuration /etc/vsftpd/[nom_utilisateur] ;
                    - supprimer le dossier /home/[nom_utilisateur] et l'intégralité de son contenu.




        => adduser.sh

                                        #!/bin/sh

                                        # Informations à compléter
                                        pass_mysql_ftp=mot_de_passe_de_l'utilisateur_mysql_"ftp"
                                        pass_mysql_root=mot_de_passe_de_l'utilisateur_mysql_"root"

                                        # On demande la saisie d'un nom d'utilisateur et du mot de passe (2 fois)
                                        echo -n "Nom d'utilisateur : "
                                        read nom
                                        echo "Mot de passe : "
                                        read -s pass
                                        echo "Mot de passe (le retour) : "
                                        read -s pass2

                                        # On vérifie que les 2 mots de passe soient identiques
                                        if test $pass = $pass2 ; then

                                                # On vérifie qu'il n'existe pas déjà un autre utilisateur du même nom
                                                if test -d /home/$nom ; then
                                                        echo "L'utilisateur existe deja"
                                                else
                                                        # On crée les dossiers et on fixe les droits
                                                        mkdir /home/$nom
                                                        mkdir /home/$nom/public_html
                                                        chown -R www-data.www-data /home/$nom
                                                        chmod -R 700 /home/$nom

                                                        # On crée le fichier de configuration pour vsFTPd
                                                        echo "anon_world_readable_only=NO" > /etc/vsftpd/$nom
                                                        echo "local_root=/home/$nom" >> /etc/vsftpd/$nom
                                                        echo "write_enable=YES" >> /etc/vsftpd/$nom
                                                        echo "anon_upload_enable=YES" >> /etc/vsftpd/$nom
                                                        echo "anon_mkdir_write_enable=YES" >> /etc/vsftpd/$nom
                                                        echo "anon_other_write_enable=YES" >> /etc/vsftpd/$nom
                                                        echo "hide_file=(none)" >> /etc/vsftpd/$nom
                                                        echo "force_dot_files=YES" >> /etc/vsftpd/$nom

                                                        # On ajoute l'utilisateur dans la base de données
                                                        mysql -u ftp -p"$pass_mysql_ftp" -D ftp -e "INSERT INTO users (name ,pass) VALUES ('$nom', ENCRYPT( '$pass' ));"

                                                        # On crée une base de données où l'utilisateur est tout-puissant
                                                        mysql -u root -p"$pass_mysql_root" -e "CREATE USER '$nom'@'localhost' IDENTIFIED BY '$pass';GRANT USAGE ON *.* TO '$nom'@'localhost' IDENTIFIED BY '$pass' WITH MAX_QUERIES_PER_HOUR 0 MAX_CONNECTIONS_PER_HOUR 0 MAX_UPDATES_PER_HOUR 0 MAX_USER_CONNECTIONS 0;CREATE DATABASE IF NOT EXISTS $nom;GRANT ALL PRIVILEGES ON $nom.* TO '$nom'@'localhost';"

                                                        echo "Utilisateur ajoute avec succes"
                                                fi

                                        else

                                                echo "Les 2 mots de passes ne sont pas identiques"

                                        fi        

        => deluser.sh


                                        #!/bin/sh

                                        # Informations à compléter
                                        pass_mysql_ftp=mot_de_passe_de_l'utilisateur_mysql_"ftp"
                                        pass_mysql_root=mot_de_passe_de_l'utilisateur_mysql_"root"

                                        # On demande la saisie du nom de l'utilisateur à supprimer
                                        echo -n "Nom d'utilisateur : "
                                        read nom

                                        # On vérifie qu'il existe
                                        if test -d /home/$nom ; then

                                                # On le supprime de la liste des utilisateurs de vsFTPd
                                                mysql -u ftp -p"$pass_mysql_ftp" -D ftp -e "DELETE FROM users WHERE name='$nom';"

                                                # On supprime son compte et sa base de donnée MySQL
                                                mysql -u root -p"$pass_mysql_root" -e "DROP USER $nom@'localhost';DROP DATABASE IF EXISTS $nom;"

                                                # On supprime son fichier de configuration vsFTPd
                                                rm -f /etc/vsftpd/$nom

                                                # On supprime son dossier
                                                rm -fr /home/$nom

                                                echo "Utilisateur DETRUIT"
                                        else
                                                echo "L'utilisateur n'existe pas"
                                        fi        

        REMARQUE: Pour exécuter ces scripts, en admettant qu'il soient enregistrés dans /root, il faut utiliser la commande suivante
                        => sh /root/mon_du_script.sh


            => Vous voilà donc en possession d'un système d'hébergement multiutilisateurs !                

## Annexes


        Il faut faire attention à ce que seul l'utilisateur root puisse lire les scripts que nous avons créés, car ils contiennent des mots de passe ! De plus, on pourrait créer des utilisateurs sans être root...

        On va donc résumer quels sont nos fichiers / dossiers et leurs permissions :

            - /home/utilisateur : Propriétairewww-data / Groupewww-data / Permissions700 (www-data peut tout faire) ;
            - /etc/vsftpd : Propriétaireroot / Groupewww-data / Permissions740 (root peut tout faire ; www-data peut lire) ;
            - /etc/vsftpd/utilisateur : Propriétaireroot / Groupewww-data / Permissions740 ;
            - /root/adduser.sh : Propriétaireroot / Grouperoot / Permissions700 ;
            - /root/deluser.sh : Propriétaireroot / Grouperoot / Permissions700.



                Changer le propriétaire et le groupe d'un fichier : chown proprietaire.groupe fichier
                Changer le propriétaire et le groupe d'un dossier (et de son contenu) : chown -R proprietaire.groupe dossier
                Changer les permissions d'un fichier : chmod permissions fichier
                Changer les permissions d'un dossier (et de son contenu) : chmod -R permissions dossier    

        Administrer les fichiers des utilisateurs:

            - vous voulez pouvoir gérer les fichiers des utilisateurs situés dans le dossier /home avec un client FTP ;
            - vous voulez aussi pouvoir gérer votre page d'accueil qui, elle, se trouve dans /var/www


                => On va donc créer un compte admin dont le dossier est /home. Il ne faut surtout pas utiliser le script, mais bien créer le fichier de configuration /etc/vsftpd/admin à la main et ajouter l'utilisateur admin dans la base de donnée avec PHPMyAdmin.

                => Nous avons donc un compte FTP admin pouvant naviguer et écrire dans les dossiers des utilisateurs.
                On va maintenant monter le dossier /var/www dans le dossier /home/www que nous créerons. Le dossier /home/www sera donc une sorte de redirection vers /var/www.

                => On crée le dossier /home/www et on règle ses permissions, ainsi que celle de /var/www

                                                        mkdir /home/www
                                                        chown -R www-data.www-data /home/www
                                                        chmod -R 700 /home/www
                                                        chown -R www-data.www-data /var/www
                                                        chmod -R 700 /var/www  

                => On va ensuite modifier le fichier /etc/fstab, qui contient les informations sur les montages à faire au démarrage, et insérer la ligne suivante

                                                        /var/www        /home/www        none        bind        0        0

                => On n'a plus qu'à faire un petit mount -a pour que les modifications soient appliquées.


                => On a alors accès à la racine du serveur web avec notre compte admin.



        Voir l'espace disque occupé:

            Encore une idée d'amélioration : créer un script permettant de connaître l'espace disque occupé par chaque utilisateur.
            La solution qui me semble la meilleure est tout simplement d'afficher séparément la taille de tous les dossiers contenus dans le dossier /home.

                                                    #!/bin/sh

                                                    # Pour chaque dossier contenu dans /home
                                                    for dir in /home/* ; do

                                                            # Si le dossier n'est pas /home/www
                                                            if test $dir != "/home/www" ; then

                                                                    # On affiche sa taille
                                                                    du -hs $dir
                                                            else
                                                                    du -hs /var/www
                                                            fi
                                                    done     


        Installer des extensions PHP:

            Installer GD pour les images:              => aptitude install php5-gd


        Les fichiers .htaccess:

            Un .htaccess peut permettre à un utilisateur de modifier la configuration d'Apache 2, avec certaines limites.
            Ces limites sont définies dans notre fichier /etc/apache2/mods-available/userdir.conf :

                => AllowOverride FileInfo AuthConfig Limit

                    REMARQUE:
                    - AllowOverride None interdit l'usage des fichiers .htaccess.
                    - AllowOverride All, au contraire, ne leur donne pas de restriction.
