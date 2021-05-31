########## LINUX ##########


## ARCHITECTURE

    /:          root
    /bin:       contient des programmes exécutables
    /boot:      fichiers pour le demarrage, le kernel
    /dev:       fichiers contenant les périphériques
    /etc:       fichiers de config du linux
    /home:      fichiers des utilisateurs
    /lib:       dossier contenant les bibliothèques partagées (généralement des fichiers.so) utilisées par les programmes
    /media:     lorsqu'un périphérique amovible (comme une carte mémoire SD ou une clé USB) est inséré dans votre ordinateur,
                Linux vous permet d'y accéder à partir d'un sous-dossier de media. On parle de montage.
    /mnt:       comme /media mais pour un usage plus temporaire
    /opt:       répertoire utilisé pour les add-ons de programmes
    /proc:      contient des informations système(comme sur les processus)      
    /root:      dossier personnel de l'utilisateur « root »
    /sbin:      contient des programmes système importants
    /sys:       tous les fichiers en rapport avec le systeme
    /tmp:       tous les fichiers temporaires
    /usr:       contient souvent les programmes demandés par les utilisateurs
    /var:       contient les fichiers variables (log; data-base)



## COMMANDES

    **pwd**    
    **cd**
    **ls**
    **cp**
    **rm**
    **mv**
    **date**            => affiche la date du jour
    **which**           => connaitre l'emplacement d'une commande
    **nano**            => editeur de fichier
    **cat**             => affiche le contenu d'1 fichier
    **less**            => affiche le contenu d'un fichier page par page
						rmq: pour sortir du "less" tapez "q"
    **grep**            => recherche d'1 fichier (sert de filtre)
    **|** (pipe)        => redirection: renvoie dans 1 programme
    **>l**              => envoie dans 1 fichier l      
    **>>l**             => ajoute dans 1 fichier l
    **touch**           => crée 1 fichier
    **dpkg**            => gestionnaire de paquets                      dpkg -S **usb**             dit où est le paquet lié à "usb"
    **mtr <IP>**          => My Trace Route: on voit par quelles IP on passe pour atteindre celle qu'on veut
    **wget**            => permet de télécharger 1 paquet à partir d'1 http
    **apt-get**         =>  apt-get update
                        apt-get upgrade
                        apt-cache search nomPaquet
                        apt-get install nomPaquet
	**yum** 			=> équivalent de _apt-get_ en fonction des distribution
		`yum install nomPaquet`
		`yum search nomPaquet`
  **du**              => donne la taille occupée par un dossier
	**which** 			=> permet de trouver la localisation d'un programme
	**whatis**			=> permet d'afficher de manière résumé, le man
	**whereis**			=> permet d'afficher le chemin de fichier executable
  **whoami**          => permet de dire qui est connecté (login)
  **passwd**          => permet de changer le mot de passe de la personne qui est logguée
                            passwd nomUser  -> permet de changer le mot de passe de l'utilisateur
                            les fichiers de password sont dans /etc/passwd
    **id**              => indique toutes les infos sur l'utilisateur en cours
						id 					donne les infos de l'utilisateur en cours
						id nomUser			donne les infos de lutilisateur demandé
    **adduser**         => permet d'ajouter 1 user
    **deluser**         => permet de supprimer 1 user
    **sudo**            => permet de faire des actions comme "root" SI l'utilisateur en cours en à les droits (password de l'utilisateur en cours)
    **groupadd**        => crée un nouveau groupe de user
      `usermod -aG nomGroup nomUser`
      -> fait que le user nomUser fait parti du groupe nomGroup
    **chown**           => change le propriétaire d'un fichier
      `chmod -u`            => pour le user
      `chmod -g`            => pour le groupe
      `chmod -o`            => pour tous les autres users
    **chgrp**           => change le groupe propriétaire d'un fichier
          `chgrp nomGroup nomFichier`
    **chmod**           => change les droits d'un fichier
    **history**         => affiche toutes les dernières commandes avec 1 n°
      -> `!n°commande`      => fait la commande
      -> `history -c`       => efface tout l'historique des commandes
     **stat** 			=> permet d'afficher les infos d'un fichier
					`stat nomFichier`

## UTILISER L'AIDE

		On peut soit utiliser le man, --help, soit aller dans le dossier /usr/share/doc.

		Tout d'abord faire
							mandb
			afin que toutes les aides soit répertoriées. C'est le dossier /usr/share/man

		- man

			man passwd	=> on voit PASSWD (1)
									(1) parce qu'il y a plusieurs sections qui traitent de ce sujet

				si on veut voir la section 3:
							man 3 passwd

				Pour avoir les pages en français (yum install man-pages-fr en fonction des distribution)

		- apropos		

			apropos passwd	=> permet de connaitre toutes les sections d'aide de passwd et voir à quoi chacune sert

		- info		/usr/share/info

			info passwd 	=> permet de voir comme man mais en plus complet

		- Si une application n'a pas de pages "man" ou "info" => comment faire?

			- parfois 	nomAppli -h (ou --help) 	peut donner une aide
			- sinon aller dans /usr/share/doc
				=> se trouve toutes les doc de tous les programmes installés


			- which nomAppli 			=> donne le chemin où se situe l'appli
			- wahtis nomAppli			=> affiche un man résumé
			- whereis

## CONNAITRE SON ADRESSE IP

    ip locale                       => hostname -I
        pour plus de details        => ifconfig
    ip publique                     => dig +short myip.opendns.com @resolver1.opendns.com
        dig est un programme de débogage de serveur DNS




## ATTRIBUT DE COMMANDES

    ls -a
    ls -l
    ls -la
    ls -lisaGh
    ls -F       => connaitre le type de fichiers ou dossier
    ls -t       => trie par date de dernière modification



## LES FICHIERS

    Tout en linux peut être considéré comme un fichier. Un fichier est un ensemble de bits. Une connexion, un transfert .... est aussi un ensemble de bits.
    Il y a 7 types de fichiers:
        - regular files                                 -> data files
        - directories                                   -> repertoires
        - symbolic links                                -> liens avec un data file
        - character devices and block devices           -> tous les devices
        - named pipes and sockets                       -> concerne les processus



## RECHERCHE DE FICHIERS

    - locate nomFichier       indique le chemin des fichiers qui contiennent nomFichier

        REMARQUE: pour que tous les fichiers apparaissent avec cette commande il faut qu'ils soient répertoriés dans la base de données du pc:
            => sudo updatedb
                -> maintenant on peut faire locate même sur 1 fichier qu'on vient de créer


    - find /cheminDoùPartLaRecherche(url)  -name nomFichier
                => si on part de la racine /, va chercher partout (c'est plus long)

    - find /url -name nomFichier -size +10M
                => va rechercher tous les fichiers qui font plus de 10Mo

    - find /url -name "*.extension" -atime 6
                => va rechercher tous les fichiers qui ont l'extension .extension et qui ont 6 jours max (moins de 7)

** (pour supprimer la couleur rose dans atom)

    - find /url -name nom -type d
                => va rechercher tous les dossiers (d pour directories) qui portent le nom nom
    - find /url -name nom -type f
                => va rechercher tous les fichiers (f pour files) qui portent le nom nom            

    - find /url -name "*.txt" -delete
                => va rechercher tous les fichiers d'extension .txt ET les effacer

    - find /url -name "*.txt"  -exec chmod 600 {} \;
                => va rechercher tous les fichiers .txt et va lancer la commande chmod 600 sur chacun d'eux
                    {} est la pour remplacer le nom des fichiers
                    \; est obligatoire en fin de commande

	- find /url -user nomUserAChercher
					find / -user user			on recherche un utilisateur qui se nomme user

	- find / -mtime -3
					on recherche TOUS les fichiers qui ont été modifiés dans les 3 jours

	- find / -mtime +3
					on recherche TOUS les fichiers qui ont été modifiés avant les 3 derniers jours

	- find /url -argumentDerecherche nomRecherche -exec nomFonction{} \;
							argumentDeRecherche: name; user; mtime, type, group...
							{} signifie pour chaque fichier


				find /url -user nomUser -type f -exec rm{} \;
							on va supprimer (rm{}) tous les fichiers (type f ) qui appartiennent à l'utilisateur nomUser qui se trouve à /url

				find /home -user Toto -type f -exec cp {} /urlDestination \;
							on va copier tous les fichiers de l'utilisateur Toto qui se trouve sur /home

			=> on peut exécuter des commandes avec find grace à -exec et \; en fin de commande





## EXTRAIRE, FILTRER, TRIER DES DONNEES                    

    grep
            Son rôle est de rechercher un mot dans un fichier et d'afficher les lignes dans lesquelles ce mot a été trouvé. L'avantage de cette commande est qu'elle peut être utilisée de manière très simple ou plus complexe.

            Les expressions régulières constituent un moyen très puissant de rechercher un texte.

		SYNTAXE:
					grep [options] expreg [fichiers...]


        grep texte nomfichier
                => on recherche le texte "texte" dans le fichier "monfichier". Il n'est pas nécessaire de mettre des guillemets autour du mot à trouver sauf si vous recherchez une suite de plusieurs mots séparés par des espaces.

        grep -i texte nomFichier
                => -i permet de ne pas tenir compte de la casse majuscule/minuscule

        grep -n texte nomFichier
                => permet d'afficher les n° de lignes où "texte" est trouvé dans le fichier

        grep -v texte nomFichier
                => permet d'inverser la recherche. ici c'est pour rechercher tout ce qui n'est pas "texte"

        grep -r texte nomDossier/
                => permet d'aller chercher dans tous les fichiers et sous-dossiers de nomDossier

		grep -w texte nomFichier
				=> permet de touver le mot "texte" en entier dans le fichier




    GREP ET LES EXPRESSIONS REGULIERES:        


            .           => caractere quelconque
            ^           => debut de ligne
            $           => fin de ligne
            []          => 1 des caracteres entre crochets
            ?           => l'element precedent est optionnel
            *           => n'inporte quoi
            |           => ou
            ()          => groupe d'expressions


        grep -E ....            => -E permet de dire à grep qu'il va y avoir une expression reguliere


        grep -E ^texte nomFichier
                => texte doit être situé en début de lignes

        grep -E [Tt]exte nomFichier
                => ex:  grep -E [Aa]lias .bashrc        -> alias ou Alias dans le fichier .bashrc
                        grep -E [0-4]lias .bashrc       -> tous les chiffres de 0 à 4 dans le fichier
                        grep -E [a-zA-Z]lias .bashrc    ->  tous les caracteres alphabétiques compreis entre "a et z" ET "A et Z"





    sort (trier)
        La commande sort se révèle bien utile lorsqu'on a besoin de trier le contenu d'un fichier.


        sort -o fichierTrie nomFichier
                => le fichier n'ayant pas été sauvé trié, -o permet d'écrire le resultat dans 1 nouveau fichier fichierTrie

        sort -r nomFichier
                => permet de trier dans le sens inverse

        sort -R nomFichier
                => fait un tri aléatoire

        sort -n nomFichier
                => permet de trier des nombres (le fichier contient des nombres)




    wc: word count

        La commande wc est un compteur de mots, mais on lui trouve plusieurs autres utilités : compter le nombre de lignes (très fréquent) et compter le nombre de caractères.


        wc nomFichier
                => renvoie  8 12 64 nomFichier        -> 8 est le nb de lignes, 12 nb de caracteres, 64 nb d'octets

        wc -l nomFichier
                => compter les lignes

        wc -c nomFichier
                => compter les octets

        wc -m nomFichier
                => compter les caracteres





    uniq        
            Permet de supprimer les doublons


        uniq nomFichier
            => supprime les doublons

        uniq nomFichier fichierSans
            => supprime les doublons ET enregistre le résultat dans 1 autre fichier

        uniq -c nomFichier
            => compte les occurences

        uniq -d nomFichier
            => affiche les lignes presentes en double



    cut
            Coupe une partie du fichier


        cut -c 2-5 nomFichier
                => permet de ne garder que les caracteres 2 à 5 pour chaque ligne du fichier

        cut -c 3 nomFichier  
                => coupe du 1er au 3 caraceres

        cut -c 3- nomFichier
                => coupe à partir du 3e caracteres






## FLUX DE REDIRECTION



    Permet de rediriger les resultats d'une commande.
    On peut les rediriger dans la console, dans 1 fichier, dans 1 autre commande.


    > et >>     
            Redirige dans 1 fichier

        commande[avec arguments] > nouveauFichier
                => renvoie les resultats dans 1 nouveau fichier

        commande[avec arguments] >> nomFichier
                => renvoie les resultats à la fin du fichier nomFichier


    2> 2>> 2>&1
            Redirige les erreurs

                Toutes les commandes produisent 2 flux de données: sortie standard(la commande) ET sortie d'erreurs
                Lorsque tout va bien -> sortie standard, sinon -> sortie d'erreurs    

        commande[avec arguments] >> nomFichier 2> erreurs.log
                => si erreur, elle apparaitra dans le fichier erreurs.log

            2>>     => à la fin du fichier

        commande[avec arguments] >> nomFichier 2>&1
                => on souhaite que les erreurs et la sortie standard aille dans le même fichier



    < et <<
            Lire depuis 1 fichier

        cat < nomFichier
                => meme resultat que cat nomFichier MAIS dans ce cas c'est le shell qui permet d'envoyer le contenu à la commande cat, sinon c'est cat qui ouvre le fichier

        <<
            passe la console en mode saisie au clavier, ligne par ligne. Toutes ces lignes seront envoyées à la commande lorsque le mot-clé de fin aura été écrit.

        sort -n << FIN
            dans 1 fichier on note des nb ligne par ligne et on termine par écrire FIN (mot clé de fin dans la commande)
                => ça nous nous trier tous les nb écrit



    |   pipe

        Sert à chainer les commandes

        commande1 | commande2 | ....
            => ex: sudo grep log -Ir /var/log  | cut -d : -f 1  | sort | uniq
                    ->Elle liste tous les fichiers contenant le mot « log » dans /var/log (-I permettant d'exclure les fichiers binaires).
                    ->Elle extrait de ce résultat uniquement les noms des fichiers.
                    ->Elle trie ces noms de fichiers.
                    ->Elle supprime les doublons.


	head et tail

		Sert à montrer les 10 premières lignes d'un fichier (head) ou les 10 dernières (tail)

			head nomFichier
					=> montre les 10 premières lignes du ficheir monFichier

			head -n nbLignes nomFichier
					=> on peut choisir le nombre de lignes que l'on souhaite afficher
						ex: head -n 5 nomFichier		affiche les 5 premières lignes du fichier

			tail nomFichier
					=> montre les 10 dernières lignes du ficheir monFichier

			tail -n nbLignes monFichier
					=> on peut choisir le nombre de lignes que l'on souhaite afficher
						ex: tail -n 3 monfichier		affiche les 3 dernières lignes


## USERS AND GROUPS

    Il y a 3 classes de users:
        - regular users
        - system users
        - superuser





## BOOT, REBBOT ET SHUTDOWN

	- systemd

		systemd (pour « system daemon » : le démon du système) est un ensemble de programmes destiné à la gestion système, conçu pour le noyau Linux. Il permet le chargement en parallèle des services au démarrage, gère les services et essaie de réduire les appels aux scripts shell. Son but est de remplacer le démon init System V, appelé aussi SysVinit.

						systemctl

		La configuration des services se trouve par défaut dans le répertoire /lib/systemd/system ou /usr/lib/systemd/system.
		On utilisera le répertoire /etc/systemd/system pour stocker ses modifications et configurations personnelles, ce qui a le gros avantage que ces fichiers ne seront pas effacés en cas de mise à jour du système.


	- init

		À tout moment, votre système Linux se trouve à un niveau d'exécution donné.
		=> init 0: Hors service : l'alimentation peut alors être coupée sans danger
		=> init 1: Mode mono-utilisateur (pour administration système)
		=> init 2: Mode multi-utilisateurs : fonctionnement normal sans NFS (identique au niveau 3 mais sans les fonctionnalités réseau).
		=> init 3: Mode multi-utilisateurs : fonctionnement normal pour systèmes en réseau, partageant leurs ressources avec d'autres systèmes.
		=> init 4: Inutilisé
		=> init 5: X11
		=> init 6: Mise hors service et redémarrage : sert durant le redémarrage du système à partir d'un niveau de fonctionnement (2, 3, 4, 5). Le système passera ensuite au niveau 0.




	- halt

						halt
						systemctl halt		=> power off du syteme


	- reboot

		reboote le systeme
													reboot
							=> équivalent à 		systemctl reboot



	- shutdown

						shutdown -r			=> reboot
						shutdown -p 		=> power off
						shutdown -r +5		=> shutdown dans 5 minutes
						shutdown -c 		=> cancel a reboot
						shutdown -r 00:00	=> reboot planifié à minuit
						shutdown -r now
				ou		shutdown -r +0		=> reboot maintenant
						shutdown -h +5		=> éteint le syteme dans 5 minutes




## SURVEILLER L'ACTIVITE DU SYSTEME

    Linux est un systeme multi-taches => il gere plusieurs programmes en même temps.
    Linux est un systeme multi-users => plusieurs utilisateurs peuvent se connecter en même temps sur la même machine (via internet par ex).



    w       
        qui fait quoi

        C'est la première commande que je tape en général quand je me connecte à un serveur surchargé et que je veux essayer de comprendre ce qui se passe. Cela me permet de voir d'un seul coup d'oeil si la machine est vraiment surchargée (et si oui, à quel point) et si quelqu'un d'autre est en train d'intervenir sur la machine.

        w permet de faire le point sur l'état du système

        on voit:
            - date (on peut aussi y accéder avec la commande date)
            - uptime => up 8:50     c'est la durée de fonctionnement de l'ordinateur
            - liste des connectés (commande who)
                IDLE: depuis combien l'utilisateur est inactif
                WHAT: la commande qu'il est en train d'executer en ce moment



    ps top      
            lister les processus qui tournent sur la machine




        ps
            ps vous permet d'obtenir la liste des processus qui tournent au moment où vous lancez la commande. Cette liste n'est pas actualisée en temps réel.

                PID:        n° identification du processus
                TTY:        nom console d'où à été lancé ce processus
                TIME:       la durée pendant laquelle le processus a occupé le processeur depuis son lancement
                CMD:        le programme qui a généré ce processus

            ps -ef
                    => lister tous les processus en cours
                        UID:    user id -> qui a lance ce processus

            ps -ejH
                    => regrouper les processus sous forme d'arborescence
                        Plusieurs processus sont des « enfants » d'autres processus, cela vous permet de savoir qui est à l'origine de quel processus.

            ps -u nomUser
                    => liste les processus lancé par l'utilisateur nomUser


            ps aux  
                    => liste tous les processus même les daemons (x). Un daemon est un programme qui s'execute en background plutot qu'à la demande d'un utilisateur

					ex: 		ps aux | grep gnome			=> affiche en detail
					ou 			pgrep gnome					=> affiche les num des process
								pgrep gnome -l				=> affiche les noms des process


    top
        top donne 1 liste dynamique des processus en cours



	free
			free
					=> indique la mémoire libre



    Ctrl+C
             arrete 1 processus lancé en console

    kill
            tue 1 processus
                => kill n°PID

            kill -9 n°PID   
                => tue sans lui laisser le temps de s'arreter proprement

		kill -l
				=> affiche une liste

        killall
            tue plusieurs processus

                -> on fait une recherche puis on tue les resultats de cette recherche

                    ps -u nomUser | grep find
                        -> affiche 1 liste
                    killall find
                        -> tue les processus de la liste


## OPTIMISER LES PROCESSUS

	Le but est de donner une prorité aux processus afin d'optimiser la machine.

		- nice et renice

		Sous linux les priorités sont organisées:
				-20 le plus important
				+19 le moins important

			=> nice permet d'initialiser la priorité d'un processus

						nice -n priorité nomProcess

							ex: nice -n -2 sshd 		=> on initialise la priorité su ssh à -2.

			=> renice permet de modifier cette priorité

						renice -n priorité <-p PID | -g GID | -u user>

							ex: renice -n 5 numProcess
								renice -n -7 $(pgrep sshd)
									=> va modifier toutes les priorités de tous les process sshd
								renice -n 4 -u alfred
									=> va modifier tous les process de l'utilisateur alfred




		On trouve le niveau de priorité dans la collone "PR" lorsqu'on liste les processus ou

						ps –eo "%p %c %n"
							indique tous les programmes avec son n° PID, son nom et sa priorité




## EXECUTER DES PROGRAMMES EN ARRIERES PLAN


    &
        Rajouter le petit symbole & à la fin de la commande que vous voulez envoyer en arrière-plan.

            ex: cp video.avi copie_video.avi &
                -> renvoie:
                    [1] 16504

                        => [1]      -> numéro du processus en arrière-plan dans cette console
                        => 16504    -> numéro d'identification général du processus(PID)

                find / -name "*log" > sortiefind &
                    lance en arrière plan la recherche dans tous les fichiers (/) les fichiers contenant log
                    on  enverra ces resultats dans le fichier sortiefind

** (pour supprimer la couleur rose dans atom)

    nohup

        Comme &, permet de lancer une tache en arrière plan MAIS detache le processus de la console.

        Ce qui veut dire que si la console est fermée ou que l'utilisateur se déconnecte, le processus sera automatiquement arrêté.
            => Si on veut que le processus continue, il faut lancer la commande via nohup

            nohup commande

                => ex:  nohup cp video.avi copie_video.avi




    Ctrl+Z
            Met en pause l'execution d'un programme

            ex: cp video.avi video_copie.avi
                -> Ctrl+Z   => Le processus est maintenant dans un état de pause. Il ne s'exécute pas mais reste en mémoire


    bg
            Passe le processus en arriere plan. Il faut l'utiliser apres Ctrl+z.

            ex: à la suite de l'avoir mis en pause on peut le mettre en arriere plan avec cette commande.

    jobs
            Permet de connaitre les processus en arriere plan.
            S'utilise lorsqu'on en a envoyé plusieurs soit avec & soit avc bg (apres Ctrl+z).


    fg
            Remettre un processus en 1er plan.
            Après avoir lister (jobs)




    screen
            est un programme puissant que vous pouvez installer avec apt-get.
             Il permet d'ouvrir plusieurs consoles virtuelles au sein d'une seule et même console, et donc d'exécuter facilement plusieurs processus en parallèle.






## EXECUTER UN PROGRAMME A 1 HEURE DIFFEREE

    Il faut d'abord est sûr de la date et l'heure du jour.

        date
            date -s HH:MM:SS
                => pour changer uniquement l'heure
            date -s MM/JJ/AAAA
                => pour changer uniquement la date

                REMARQUE: ça a permit de changer la date du systeme MAIS il faudrait aussi changer celle du BIOS
                =>
                hwclock
                    => permet de connaitre la date et l'heure du BIOS
                hwclock --systohc
                    => permet de les modifier

            changer le format de la date:   + %ceQuonVeutChanger (ex heure -> %H)

            date "+%H:%M:%S"
                    => affiche 12:36:15
            date "+%Hh%Mm%Ss"
                    => affiche 12h41m01s



        at
            Execute une commande plus tard. MAIS cette commande ne peut être executée qu'un seule fois.

            La commande s'utilise en deux temps.

                - Vous indiquez à quel moment (quelle heure, quel jour) vous désirez que la commande soit exécutée.
                - Vous tapez ensuite la commande que vous voulez voir exécutée à l'heure que vous venez d'indiquer.

                  ex: at 14:17
                            -> renvoie certaines choses et se termine par at> (où on écrit à sa suite la commande à executer)
                        touch fichier.txt


                         at 14:17 tomorrow      => ce sera demain à 14h17

                         at now +5 minutes      => dans 5 minutes
                                    minutes; hours; days; weeks; months; years

        atq/ atrm

            atq     => liste les procesus en attente
            atrm    => supprime les processus en attente
                atrm n°PID



        sleep

            Permet de faire 1 pause entre des commandes.
            REMARQUE: on peut lister plusieurs commandes en les séparant avec des ; ou &&

                ex: touch fichier.txt; sleep 10; rm fichier.txt
                    OU touch fichier.txt && sleep 10 && rm fichier.txt
                        => crée 1 fichier; fait 1 pause de 10s; supprime le fichier

                    touch fichier.txt; sleep 1m; rm fichier.txt
                        => pause de 1 minute
                            m: minute; h: hour; d: days



        crontab

            cet outil nous permet de programmer l'exécution régulière d'un programme.

            1. Nous devons modifier notre configuration (notre fichier .bashrc)

                rajoutez la ligne suivante à la fin de votre fichier .bashrc
                => echo "export EDITOR=nano" >>  ~/.bashrc

            2.

                crontab est en fait une commande qui permet de lire et de modifier un fichier appelé la « crontab ».
                Ce fichier contient la liste des programmes que vous souhaitez exécuter régulièrement, et à quelle heure vous souhaitez qu'ils soient exécutés.

                Il y a trois paramètres différents à connaître:
                    -e : modifier la crontab ;
                    -l : afficher la crontab actuelle ;
                    -r : supprimer votre crontab. Attention, la suppression est immédiate et sans confirmation !


                afficher la crontab habituelle: (si on n'en a jamais crée il dira qu'il n'y en a pas)

                    crontab -l

                 modifier la crontab:

                    crontab -e

                        => Le fichier ne contient qu'une seule ligne :
                            # m h  dom mon dow   command
                                m:          minutes (0 - 59) ;
                                h:          heures (0 - 23) ;
                                dom:        (day of month) : jour du mois (1 - 31) ;
                                mon:        (month) : mois (1 - 12) ;
                                dow:        (day of week) : jour de la semaine (0 - 6, 0 étant le dimanche) ;
                                command:    la commande à exécuter


                        ex: exécuter une commande tous les jours à 15 h 47 qui crée un fichier fichier.txt
                            => 47 15 * * * touch /home/nomUser/fichier.txt


                    ATTENTION: il faut penser à rediriger les erreurs
                    => 47 15 * * * touch /home/nomUser/fichier.txt >> /home/nomUser/cron.log 2>&1




## ARCHIVER ET COMPRESSER

    1. Sert à réunir les fichiers dans un seul gros fichier appelé archive.
    2. compresser le gros fichier ainsi obtenu à l'aide de gzip ou de bzip2


    1. On crée une archive tar. pour cela on recupere tous les fichiers dans 1 dossier nom_dossier/

        tar

                tar -cvf nom_archive.tar nom_dossier/
                    -c : signifie créer une archive tar ;
                    -v : signifie afficher le détail des opérations ;
                    -f : signifie assembler l'archive dans un fichier

                tar -cvf archive.tar fichier1 fichier2 fichier3
                    => crée une archive avec ces 3 fichiers

                tar -tf nomArchive
                    ->affiche le contenu de l'archive sans l'extraire

                tar -rvf nomArchive fichier_supplementaire
                    -> ajoute 1 fichier supplementaire à 1 archive

                tar -xvf nomArchive
                    -> extraire les fichiers de l'archive


    2. Maintenant que tous les fichiers sont dans 1 archive on peut les compresser.

        gzip : c'est le plus connu et le plus utilisé ;
        bzip2 : il est un peu moins fréquemment utilisé. Il compresse mieux mais plus lentement que gzip.
            nomArchive.tar = 130ko
                -> nomArchive.tar.gz = 35ko
                -> nomArchive.tar.bz2 = 29ko

            gzip nomArchive.tar
                -> compresse en nomArchive.tar.gz
            gunzip nomArchive.tar.gz
                -> décompresse en nomArchive.tar

            bzip2 nomArchive.tar
                -> compresse en nomArchive.tar.bz2
            bunzip2 nomArchive.tar.bz2
                -> décompresse en nomArchive.tar



    Archiver ET compresser

        tar -zcvf tutoriels.tar.gz tutoriels/
            => en gzip
        tar -zxvf tutoriels.tar.gz
            => pour decompresser

        tar -jcvf tutoriels.tar.bz2 tutoriels/
            => en bzip2
        tar -jxvf tutoriels.tar.bz2 tutoriels/
            => pour decompresser



    zcat : équivalent de cat, capable de lire un fichier compressé (gzippé)
    zmore : équivalent de more, capable de lire un fichier compressé (gzippé)
    zless : équivalent de less, capable de lire un fichier compressé (gzippé)        

        zcat nomArchive.gz


    DECOMPRESSER UN .ZIP

        unzip
                Il faut d'abord l'installer:
                    sudo apt-get install unzip
                Ensuite on peut décompresser
                    unzip archive.zip

        unrar
                Il faut d'abord l'installer:
                    sudo apt-get install unrar
                Ensuite on peut décompresser
                    unrar e tutoriels.rar




## CONNEXION SECURISE SSH

    ssh est un protocole connexion sécurisée à distance sur une machine linux.
    Un protocole est une langue qui permet à 2 ordi de communiquer entre  eux.
        - HTTP (HyperText Transfer Protocol)        -> pour échanger des pages web
        - FTP (File Transfer Protocol)              -> pour le transfert de fichiers
        - IMAP (Internet Message Access Protocol)   -> pour les echanges de mails
        - ssh (Secure Shell)                        -> pour une connexion sécurisée


    CREATION D'UN TUNNEL SECURISE

        SSH utilise les deux chiffrements : asymétrique et symétrique. Cela fonctionne dans cet ordre.

            - On utilise d'abord le chiffrement asymétrique pour s'échanger discrètement une clé secrète de chiffrement symétrique.
            - Ensuite, on utilise tout le temps la clé de chiffrement symétrique pour chiffrer les échanges.


            Ce serait possible mais il y a un défaut : le chiffrement asymétrique demande beaucoup trop de ressources au processeur. Le chiffrement asymétrique est 100 à 1 000 fois plus lent que le chiffrement symétrique !
            Les ordinateurs s'échangent donc la clé de chiffrement symétrique de manière sécurisée (grâce au chiffrement asymétrique) et peuvent ensuite communiquer plus rapidement en utilisant en permanence le chiffrement symétrique.

            Le chiffrement asymétrique est donc utilisé seulement au début de la communication, afin que les ordinateurs s'échangent la clé de chiffrement symétrique de manière sécurisée. Ensuite, ils ne communiquent que par chiffrement symétrique.


            Toutes ces étapes se font automatiquement. Vous allez juste avoir à entrer un login et un mot de passe pour vous connecter à votre machine à distance.



    TRANSFORMER SA MACHINE EN SERVEUR

        1. Installer le paquet openssh-server:

               D'abord vérifier la version de ssh:

							ssh -V

				et la version des bibliotheuqe ssl

							dpkg -l libssl*

				ensuite:

							sudo apt-get install openssh-server

                Au cours de l'installation on doit voir des étapes automatiques:
                    RSA et DSA sont deux algorithmes de chiffrement asymétrique.C'est l'étape de création d'une clé publique et d'une clé privée pour chacun des deux algorithmes (RSA et DSA).
                    Ensuite, le programme de serveur SSH (appelé sshd) est lancé.
                    Normalement, le serveur SSH sera lancé à chaque démarrage. Si ce n'est pas le cas, vous pouvez le lancer à tout moment avec la commande suivante :

                        sudo /etc/init.d/ssh start

                    Et l'arrêter avec:

                        sudo /etc/init.d/ssh stop


                    REMARQUE:
                            Vous ne devriez pas avoir besoin de configurer quoi que ce soit, mais sachez au besoin que le fichier de configuration se trouve dans /etc/ssh/ssh_config. Il faudra recharger SSH avec la commande

                                sudo /etc/init.d/ssh reload

                            pour que les changements soient pris en compte.


            => votre machine est désormais un serveur SSH ! Vous pouvez vous y connecter depuis n'importe quel ordinateur sous Linux ou sous Windows dans le monde (pour peu que vous ne soyez pas derrière un pare-feu).                



        2. Se connecter via ssh à partir d'une machine linux

            Toutes les machines équipées de Linux proposent la commande ssh qui permet de se connecter à distance à une autre machine.(C'est le paquet openssh-client)

                ssh login@ip -p <num_port>
                                login: votre login
                                ip: celle de votre ordi(sur lequel on veut se connecter)
                                -p <num_port> n'est pas obligatoire. Si rien ecrit, par defaut port 22


            Un message va apparaitre avec un "fingerprint" (code long qui correspond au serveur auquel on veut se connecter)
                => taper "yes"
            Le serveur et le client vont alors s'échanger une clé de chiffrement.
            Le serveur devrait vous demander au bout de quelques secondes votre mot de passe :
                Vous pouvez l'entrer en toute sécurité, la communication est chiffrée. ;-)




    AUTHENTIFICATION AUTOMATIQUE PAR CLE

        Il y a 2 moyens de s'authentifier:
            - par mot de passe
            - par clé

        Avec cette nouvelle méthode d'authentification, c'est le client qui va générer une clé publique et une clé privée. Les rôles sont un peu inversés.
        L'avantage, c'est que l'on ne vous demandera pas votre mot de passe à chaque fois pour vous connecter. Si vous vous connectez très régulièrement à un serveur, c'est vraiment utile. Si vous faites bien les choses, cette méthode est tout aussi sûre que l'authentification par mot de passe.    



        Opérations sur la machine du client:

            1. Il faut tout d'abord vous rendre sur la machine du client et taper la commande suivante pour générer une paire de clés publique / privée :

                    ssh-keygen -t rsa

                             Vous pouvez remplacer rsa par dsa si vous voulez utiliser l'autre algorithme de chiffrement.

                => Plusieurs messages s'afficher et il vous sera demandé quelques petites précisions :        

                    - le client génère une paire de clés (« Generating public/private rsa key pair »).
                        Il doit ensuite sauvegarder ces clés dans des fichiers (un pour la clé publique, un pour la clé privée). On vous propose une valeur par défaut : je vous conseille de ne rien changer et de taper simplement Entrée.

                    - on vous demande une passphrase. C'est une phrase de passe qui va servir à chiffrer la clé privée pour une meilleure sécurité.
                        écrire une phrase pass, et la clé sera chiffrée.

            2. Envoyer la clé publique au serveur

                Il faut l'envoyer pour que le serveur puisse chiffrer les messsages.
                    - Votre clé publique devrait se trouver dans ~/.ssh/id_rsa.pub (pub comme public)
                    - Votre clé privée, elle, se trouve dans ~/.ssh/id_rsa. Ne la communiquez à personne ! Elle est normalement chiffrée si vous avez entré une passphrase, ce qui constitue une sécurité de plus.

                - pour commencer aller dans le dossier .ssh

                        cd  /.ssh

                - faire un ls

                        ls

                            => 3 fichiers:
                                - id_rsa : votre clé privée, qui doit rester secrète
                                - id_rsa.pub : la clé publique que vous pouvez communiquer à qui vous voulez, et que vous devez envoyer au serveur
                                - known_hosts : c'est la liste des fingerprint que votre PC de client tient à jour


                    => envoyer la clé publique (id_rsa.pub) au serveur et à l'ajouter à son fichier authorized_keys (clés autorisées). Le serveur y garde une liste des clés qu'il autorise à se connecter.

                - faire une copie de la clé publique avec ssh-copy-id

                        ssh-copy-id -i id_rsa.pub login@ip
                                                            => remplacer le login et l'ip par ce qu'on souhaite

                    Vous vous connectez par mot de passe encore une fois, pour pouvoir ajouter votre clé publique sur le serveur.
                    La clé est ensuite automatiquement ajoutée à ~/.ssh/authorized_keys sur le serveur. On vous invite à vérifier si l'opération s'est bien déroulée en ouvrant le fichier authorized_keys.  



        3. Se connecter

            Vous pouvez vous connecter au serveur comme avantage

                        ssh login@ip

            On vous demande la phrase de passe pour déchiffrer votre clé privée. Entrez-la.
            => vous êtes connecté



            REMARQUE: A quoi ça sert, à la place d'un mot de passe je dois entrer une phrase pass....

            => L'agent SSH est un programme qui tourne en arrière-plan en mémoire. Il retient les clés privées pendant toute la durée de votre session.
                Tout ce que vous avez à faire est de lancer le programme ssh-add sur le PC du client :

                        ssh-add

                Il va automatiquement chercher votre clé privée. Pour la déchiffrer, il vous demande la passphrase. Entrez-la.
                Maintenant que c'est fait, chaque fois que vous vous connecterez à un serveur, vous n'aurez plus besoin d'entrer la passphrase        





## TRANSFERER DES FICHIERS


    wget: téléchargement de fichiers        => wget [options] [url]

                wget propose énormément d'options => man wget pour les voir



                apt-get update && apt-get install wget


            wget permet de télécharger directement des fichiers depuis la console. il suffit d'y mettre l'adresse http ou ftp d'un fichier à télécharger.

                wget http://cdimage.debian.org/debian-cd/4.0_r5/i386/iso-cd/ debian-40r5-i386-businesscard.iso

                => on veut télécharger le fichier iso de debian. Une barre de progression va s'afficher
                Pour récupérer l'adresse du fichier, navigateur web, clic D, copier le lien.
                Il existe des navigateurs en console : lynx (qui est basique) et links (plutot complet)


            Reprendre un téléchargement arrêté: => option -c

                wget -c http:.....


            Lancer un téléchargement en tache de fond   => option --background

                wget --background -c http://cdimage.debian.org/debian-cd/4.0_r5/ i386/iso-cd/debian-40r5-i386-businesscard.iso


            Télécharger vers 1 repertoire cible

                wget -P /chemin/du/répertoire_cible http://www.site.org/fichier_distant


            Télécharger vers 1 fichier cible

                wget -O /chemin/du/fichier_cible http://www.site.org/fichier_distant


            Télécharger les URLs contenues dans 1 fichier

                wget -i fichier





    scp: copier des fichiers sur le reseau (Secure Copy)          

        Fonctionne avec des serveurs ou pc où ssh est installé

            D'1 pc local vers 1 pc distant:

                scp /chemin local/fichier-envoi user@IPadressRéception:/chemin-de-réception


            D'1 distant vers 1 local

                scp user@IPadressEnvoie:/chemin/distant1/fichier-envoi /chemin-local-de-reception

            D'1 distant vers 1 autre distant

                scp user@IPadressEnvoie:/chemin-distant1/fichier-envoi IPadressReception:/chemin-distant2-de-reception


            ATTENTION: si le port ssh n'est pas celui par défaut(22)

                scp -P n°port /chemin-origine/ user@IPadressReception:/chemin-de-réception





    ftp et sftp: transfert de fichiers

        sftp est ftp avec ssh

            ftp:

                - télécharger un fichier depuis un serveur FTP public. En général, les navigateurs web font cela
                - de manière anonyme. Transférer des fichiers vers un serveur FTP privé. Lorsque l'on prend un hébergement pour son site web, l'hébergeur nous donne en général des accès FTP pour aller y déposer les fichiers du site. La connexion se fait en mode authentifié.


                Connexion a un serveur ftp:

                    ftp ftp.debian.org

                        => une connexion se fait    => >ftp dans la console. Maintenant on peut naviguer avec les commandes linux habituelles (cd, ls, ...)

                Transfert de fichiers

                    put             => envoie 1 fichier vers le serveur (impossible sur les serveurs publics, seul les téléchargement sont autorisés)
                    get             => télécharge 1 fichier du serveur


                        get nomFichier          => ça télécharge le fichier nomFichier dans le dossier dans lequel on se trouve sur l'ordi (!pwd pour savoir où on se trouve)


            sftp:

                connexion:          sftp nom_utilisateur_distant@hôte_distant
                utilisation:
							- aide:

									?		=> correspond au man

                            - recuperer un fichier:

                                    get /répertoires_distants/fichier /répertoires_locaux/

                            - recuperer un dossier:

                                    get -r /répertoires_distants/dossier /répertoires_locaux/


                            - envoyer un fichier:

                                    put /répertoires_locaux/fichier /répertoires_distants/

                            - envoyer un dossier:

                                il faut créer un dossier:
                                    mkdir /répertoires_distants/$dossier
                                on peut envoyer:
                                    put -r /répertoires_locaux/$dossier /répertoires_distants/

							- quitter sftp:

									quit


    rsync: synchroniser des fichiers pour une sauvegarder

        rsync est le plus souvent utilisé pour effectuer des sauvegardes incrémentielles.



            installation:
                vérifier l'état du paquet:  ( dpkg --list 'rsync' 2>&1 > /dev/null ) && echo "rsync est installé" || echo "rsync n'est pas installé"
                installer le paquet:        apt-get install rsync -y

            utilisation:

                rsync -arv dossierAsauver/ dossierSauvegarde/

                    -a          => conserve toutes les infos sur les fichiers (droits, date, format...)
                    -r          => sauvegarde aussi tous les sous-dossiers
                    -v          => verbose





                Supprimer les fichiers:
                    Par defaut rsync ne supprime pas les fichiers sur le dossierSauvegarde.

                        rsync -arv --delete dossierAsauver/ dossierAsauver/

                            => va supprimer les fichiers dans dossierSauvegarde qui ne sont pas dans dossierAsauver





                Sauvegarder des fichiers supprimés:
                    La suppression d'1 fichier était accidentelle -> il est possible de garder de coté des fichiers qu'on a supprimés: option --backup

                        rsync -arv --delete --backup --backup-dir=/home/nomUser/backups_supprimes dossierAsauver/ dossierSauvegarde/

                            --backup-dir=/home/nomUser/backups_supprimes       => déplacer les fichiers supprimés dans un dossier qui leur est dédié
                                                                                    Il est recommandé d'indiquer le répertoire backup-dir en absolu comme fait. Sinon, le répertoire des fichiers supprimés sera placé à l'intérieur du répertoire de sauvegarde et vous risquez d'avoir plus de problèmes qu’autre chose lors de la synchronisation.



                Sauvegarde des fichiers supprimés vers 1 pc distant avec connexion ssh sur le port 12473

        rsync -arv --delete --backup --backup-dir=/home/nomUser/fichiers_supprimes dossierAsauver/ login1@IP_du_serveur:dossierSauvegarde/ -e "ssh -p 12473"


## DEMARRER, STOPPER ET VERIFIER LE STATUS DES SERVICES NETWORK


	1. Connaitre les systemes network:

					systemctl list-units | grep network

						=> on voit que network.target est le seul service qui est actif

	2. on redemarre les services network

					systemctl restart network.service

	3. on vérifie que le service soit lancé

					systemctl is-enabled network

						=> si c'est écrit enabled c'est que le service est lancé


	REMARQUE: il y a beaucoup de service network, il est donc conseillé, afin de limiter les problèmes, de séparer les services network sur différents serveurs (1 par serveur)


	4. arrêter un service network

					systemctl stop <nomService>

	5. connaitre le statut d'un service network

					systemctl status <service>

						ex: systemctl status network
						=> il est noté dans la ligne "Active" si le service network est actif ou inactif








## ANALYSER LE RESEAU ET FILTRER LE TRAFIC AVEC 1 PARE-FEU


    host et whois

        Chaque ordinateur est relié à une adresse IP. On peut associer chaque IP à un nom d'hote (hostname). Ce sont des noms plus facile à mémoriser.

            => host         -> permet de conversion une IP en hostname et inversement

                host nomDomaine             -> renvoie une IP ()
                host IP                     -> renvoie "normalement" le nom de domaine (souvent ovh lorsque le site est hébergé chez ovh)


            Pour gérer les noms d'hotes on fait appel aux serveurs DNS.
            On ne peut pas modifier la liste des correspondance IP<->nomHote sur le DNS, mais, on peut établir une liste de correspondances personnalisée sur son ordinateur => fichier /etc/hosts

                sudo nano /etc/hosts

                    => il suffit de rajouter dans ce fichier l'ip et le nom correspondant



            => whois

                Tout savoir sur un nom de domaine.

                    whois nomDomaine








    ifconfig et netstat

        Chaque ordinateur possede en général plusieurs interfaces réseaux.

            => ifconfig

                differentes interfaces:

                    - eth0              => la connexion par câble réseau. Certains serveurs ont plusieurs sorties filaires (=> eth1, eth2, ...)
                    - lo                => la boucle locale.Elle correspond à une connexion à… vous-mêmes. C'est pour cela qu'on l'appelle la boucle locale :                     tout ce qui est envoyé par là vous revient automatiquement. Cela peut paraître inutile, mais on a parfois besoin de                      se connecter à soi-même pour des raisons pratiques.
                    - wlan0             => le wifi



                ifconfig permet aussi de configurer des réglages réseaux.
                        ifconfig interface etat

                            - interface         eth0; wlan0...
                            - etat              up ou down pour activer ou désactiver l'interface



            => netstat

                statistiques sur le reseau:

                    Cette commande est incontournable quand on veut savoir ce que notre machine est en train de faire sur le réseau. Elle peut afficher beaucoup d'informations => il faut se servir d'options.


                    netstat -i              => statistiques des interfaces réseau
                                 colonne RX-ERR     => on voit les interface où il y a de l'activités (lo en a 1 peu aussi)   

                    netstat -uta            => lister toutes les connexions ouvertes
                                -u : afficher les connexions UDP ;
                                -t : afficher les connexions TCP ;
                                -a : afficher toutes les connexions quel que soit leur état.        
                        Ce tableau vous indique qui, depuis l'adresse locale, est connecté à qui (à une adresse distante).
                        Chaque connexion a un état. Ici, on repère les états LISTEN et ESTABLISHED. Il y en a d'autres.
                            ESTABLISHED : la connexion a été établie avec l'ordinateur distant ;
                            TIME_WAIT : la connexion attend le traitement de tous les paquets encore sur le réseau avant de commencer la fermeture ;
                            CLOSE_WAIT : le serveur distant a arrêté la connexion de lui-même (peut-être parce que vous êtes restés inactifs trop longtemps ?) ;
                            CLOSED : la connexion n'est pas utilisée ;
                            CLOSING : la fermeture de la connexion est entamée mais toutes les données n'ont pas encore été envoyées ;
                            LISTEN : à l'écoute des connexions entrantes

                         Le port sur lequel ces connexions écoutent (après le symbole « : ») est probablement l'information la plus intéressante. En effet, on peut se connecter à chaque ordinateur via différentes « portes » appelées ports. Chaque service utilise un port différent.
                                - *:ssh, ce qui signifie que SSH est en train d'écouter sur le port de SSH au cas où quelqu'un veuille se connecter à votre machine. C'est logique puisque j'ai activé le serveur SSH pour pouvoir m'y connecter à distance au besoin.

** (pour supprimer la couleur rose dans atom)

                Si on rajoute l'option -n on aura les n° de ports qui s'affichent

                        netstat -utan

                    netstat -lt             => liste des connexions en état d'écoute
                                -l permet de filtrer les connexions à l'état LISTEN et donc de savoir quels ports de serveur sont susceptibles d'être utilisés en ce moment sur votre machine

                    netstat -s              => statistiques résumées




        => iptables             le pare-feu de reference


            installation:       apt-get install iptables

            Il permet d'établir un certain nombre de règles pour dire par quels ports on peut se connecter à votre ordinateur, mais aussi à quels ports vous avez le droit de vous connecter.
            La technique ne consiste pas à bloquer certains ports mais plutôt à bloquer par défaut tous les ports et à en autoriser seulement quelques-uns.



            Parametres de iptables

                -t : vaudra par défaut « filter » ;
                -A : servira à indiquer le sens du trafic : INPUT (entrant) ou OUTPUT (sortant) ;
                -p : indique le protocole (TCP ou UDP en principe) ;
                --dport et --sport : respectivement port destination et port source (comme nous sommes le serveur, nous utiliserons principalement dport) ;
                -j : comment traiter le paquet (nous nous servirons d'ACCEPT et de DROP pour respectivement accepter et refuser le paquet).


                => syntaxe d'une regle:

                    iptables -t filter -A INPUT/OUTPUT -p protocole --dport port_a_ouvrir -j ACCEPT

                    REMARQUE: si vous voulez un échange, il faut toujours ouvrir le port dans les deux sens (INPUT et OUTPUT)

                                Ex: si l’on a un serveur web (port 80) :
                                    iptables -t filter -A OUTPUT -p tcp --dport 80 -j ACCEPT
                                    iptables -t filter -A INPUT -p tcp --dport 80 -j ACCEPT







            iptables s'utilise en « root ».
                    => sudo su


                    iptables -L             => afficher les regles        
                            On repere 3 sections:
                                - Chain INPUT : correspond aux règles manipulant le trafic entrant ;
                                - Chain FORWARD : correspond aux règles manipulant la redirection du trafic ;
                                - Chain OUTPUT : correspond aux règles manipulant le trafic sortant.

                            Tant qu'aucune regle n'est établie ces listes sont vides. De plus il est écrit policy ACCEPT, qui signifie que tout le trafic est accepté par defaut. => le pare-feu est inactif, rien n'est bloqué

                    iptables -F             => réinitialise toutes les regles


        Le principe des regles:
                - l'ordre des règles est important. iptables les lit de haut en bas et la position de ces règles influe sur le résultat final. Sachez donc que les règles sont numérotées

                    iptables -L --line-numbers  => indique les n° des regles

                            - target : ce que fait la règle. Ici c'est ACCEPT, c'est-à-dire que cette ligne autorise un port et / ou une IP ;
                            - prot : le protocole utilisé (tcp, udp, icmp). Je rappelle que TCP est celui auquel on a le plus recourt. ICMP permet à votre ordinateur de répondre aux requêtes de type « ping » ;
                            - source : l'IP de source. Pour INPUT, la source est l'ordinateur distant qui se connecte à vous ;
                            - destination : l'IP de destination. Pour OUTPUT, c'est l'ordinateur auquel on se connecte ;
                            - la dernière colonne : elle indique le port après les deux points « : ». Ce port est affiché en toutes lettres, mais avec -n vous pouvez obtenir le numéro correspondant

                - ajouter et supprimer des regles

                    -A chain : ajoute une règle en fin de liste pour la chain indiquée (INPUT ou OUTPUT, par exemple).
                    -D chain rulenum : supprime la règle n° rulenum pour la chain indiquée.
                    -I chain rulenum : insère une règle au milieu de la liste à la position indiquée par rulenum. Si vous n'indiquez pas de position rulenum, la règle sera insérée en premier, tout en haut dans la liste.
                    -R chain rulenum : remplace la règle n° rulenum dans la chain indiquée.
                    -L : liste les règles (nous l'avons déjà vu).
                    -F chain : vide toutes les règles de la chain indiquée. Cela revient à supprimer toutes les règles une par une pour cette chain.
                    -P chain regle : modifie la règle par défaut pour la chain. Cela permet de dire, par exemple, que par défaut tous les ports sont fermés, sauf ceux que l'on a indiqués dans les règles.

                        => syntaxe:
                                    iptables -A (chain) -p (protocole) --dport (port) -j (décision)
                                        => Remplacez chain par la section qui vous intéresse (INPUT ou OUTPUT), protocole par le nom du protocole à filtrer (TCP, UDP, ICMP…) et enfin décision par la décision à prendre : ACCEPT pour accepter le paquet, REJECT pour le rejeter ou bien DROP pour l'ignorer complètement.

                                    ex: iptables -A INPUT -p tcp --dport ssh -j ACCEPT


                - authoriser les pings          le protocole icmp est utilisé pour le ping

                        iptables -A INPUT -p icmp -j ACCEPT


                - authoriser les connexions locales déjà ouvertes

                        iptables -A INPUT -i lo -j ACCEPT
                        iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

                            => Ces deux règles utilisent des options un peu différentes de celles que nous avons vues jusqu'ici. Voici quelques explications.
                                    - La première règle autorise tout le trafic sur l'interface de loopback locale grâce à -i lo. Il n'y a pas de risque à autoriser votre ordinateur à communiquer avec lui-même, d’autant plus qu’il en a parfois besoin !
                                    - La seconde règle autorise toutes les connexions qui sont déjà à l'état ESTABLISHED ou RELATED. En clair, elle autorise toutes les connexions qui ont été demandées par votre PC. Là encore, cela permet d'assouplir le pare-feu et de le rendre fonctionnel pour une utilisation quotidienne.

                - refuser toutes les autres connexions par defaut

                        iptables -P INPUT DROP

                            Le filtrage est radical. Nous n'avons pas autorisé beaucoup de ports et il se pourrait que vous vous rendiez compte que certaines applications n'arrivent plus à accéder à l'internet.


                - appliquer les regles au démarrage

                        Si vous redémarrez votre ordinateur, les règles iptables auront disparu !
                        Le seul moyen pour qu'elles soient chargées au démarrage consiste à créer un script qui sera exécuté au démarrage.





## COMPILER UN PROGRAMME DEPUIS LES SOURCES


    La plupart des programmes dont vous aurez besoin sous Ubuntu sont référencés dans des dépôts et accessibles via une simple commande : apt-get. Toutefois, certains programmes récents ou encore en développement ne sont pas disponibles via apt-get.

    Quand apt-get ne propose pas le programme que l'on recherche, il est parfois possible de trouver sur le site web du logiciel un paquetage .deb. C'est en quelque sorte l'équivalent du programme d'installation, mais celui-ci est spécifique à Debian et à ses distributions dérivées (dont fait partie Ubuntu). Les .deb ne fonctionnent pas sur les distributions utilisant d'autres outils ; Red Hat utilise des .rpm par exemple.
    Notez que le programme alien est capable de convertir un .rpm en .deb au besoin.

    Si on trouve ce .deb, le télécharger, 2*clic -> 1 fenetre apparait et propose d'installer le logiciel.




    Si le programme que vous recherchez n'est pas dans les dépôts (apt-get) et que vous ne parvenez pas non plus à trouver de .deb prêt à l'emploi sur le web, vous allez devoir le compiler depuis ses sources.

    La compilation est un procédé qui permet de transformer le code source d'un programme en un exécutable que l'on peut utiliser.

    Pour compiler des programmes, vous aurez besoin avant toute chose d'installer les outils de compilation: installer le paquet build-essential

        sudo apt-get install build-essential

    Nous pouvons à présent nous intéresser à la compilation.

    Etapes de la compilation:
        1. aller sur le site et télécharger le programme qu'on desire
        2. le décompresser                      tar -xvfz ...
        3. aller dans ce dossier                cd nomDossier/
        4. lancer configure                     ./configure
                        configure est un programme qui analyse votre ordinateur et qui vérifie si tous les outils nécessaires à la compilation du logiciel que vous souhaitez installer sont bien présents.
                        Malheureusement, il arrivera fréquemment que configure affiche une erreur en raison d’un manque de dépendances.
        5. chercher dans google à quoi correspond cette erreur      => donne le nom du paquet à installer
        6. installer ce paquet                  sudo apt-get install nomPaquet
        7. une fois installé, relancer configure    ./configure
        8. soit erreur et recommencer la demarche, soit ok
        9. si ok, le programme est pret à etre compilé      make
        10. une fois la compilation terminée, l'executable a du etre cree => il faut l'installer
                        sudo make install
        11. pour l'utiliser taper son nom
        12. pour le desinstaller            sudo make uninstall             à partir du dossier où il a été compilé
