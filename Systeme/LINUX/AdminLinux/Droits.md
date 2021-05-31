########## DROITS ##########

## LISTE DES DROITS

    `ls -la`
    dossier         utilisateur     groupe          tous            taille      date        nom
    d/-/l           rwx/r--/...     rwx/r--/...     rwx/r--/...     taille(MO)  date        /path/nomDossier ou nomFichier



## COMMANDE chmod

    `chmod [utilisateur][groupe][tous]`       => permet de modifier les droits d'un dossier ou d'un fichier

    r:4 w:2 x:1

        => chmod 777        => tous les droits à tout le monde pour 1 réperoire
        => chmod 666        => tous les droits à tout le monde pour 1 fichier

    umask:  droit de fichier et répertoire (w=2)
                on peut le modifier par defaut dans /etc/profile

## RECAPITULATIF DES DROITS

    umask           fichier                     repertoire
    000             666(rwxrwxrwx)              777(rwxrwxrwx)
    002             664(rw-rw-rw-)              775(rwxrwxrw-)
    022             644(rw-r--r--)              755(rwxr--r--)
    027             640(rw-r-----)              750(rwxr-----)
    077             600(rw-------)              700(rwx------)
    277             400(r--------)              500(r--------)

## UMASK

	Umask pour User mask.
	Umask est un outil de gestion des droits.
	C'est là qu'on crée les permissions par defaut (ou avant la création de fichiers).

	L'umask par defaut est 022.

	Pour vérifier l'umask définie:
						`umask -p` 	(p pour print)

	- Calcul des droits d'un fichier à partir de l'umask:

			666 (tous les droits) - umask = droits d'un fichier par defaut
						=>	666-022=644

	- Pour changer la valeur de l'umask de façon temporaire:
						umask 077
							=> nous avons changé sa valeur par defaut de manière temporaire à 077
							=> tous les fichiers créés maintenant auront ces droits par defaut

		=> L'umask sera réinitilalisé à sa valeur par defaut à la fermeture de la session.

	- Parametrer l'umask d'un groupe utilisateur:

		On peut faire en sorte que seul le groupe qui cré un fichier ait des droits dessus => umask 077).
		Pour cela on peut changer ce parametre dans /etc/login.defs (ou dans /etc/pam.d/common-session suivant les distributions => il faut utiliser le module pam_umask.so pour générer l'umask).

		=> il faut aussi créer un umask pour root sinon cet umask s'appliquera aussi à root
			=> dans /root/.profile décommenter et modifier la ligne


	Les privilèges par defaut sont créés pour 2 groupes: root (permissed user) et wheel (non-permissed user)
	Lorsqu'on veut modifier ses privilèges il faut penser qu'on veut cacher les droits avec umask:

		umask  => 022 veut dire:
			- 0		pour le ower, il a tous les droits, donc on ne cache rien => 0
			- 2		pour le groupe, il n'a que les droits de lecture => 2
			- 2		pour le reste, il n'a que les droits de lecture => 2

				cela se calcule là aussi avec:	r:4 w:2 x:1


## COMMANDE chown

    change owner => changer le proprietaire

        `chown [nouveauProprietaire] [nomFichier]`
        `chown [nouveauProprietaire]:[nomGroupe] [nomFichier]`


## COMMANDE chgrp

    change group    => change 1 fichier de groupe propriétaire

        `chgrp [nomGroupe][nomFichier]`




## SETUID, SETGID

	Ce sont des droits speciaux. Ils s'ajoutent aux cliassiques (lecture, écriture, execution).
	Ils fonctionnent différement suivant qu'on les applique sur un fichier ou un répertoire.

	- setuid

							`ls -l /usr/bin/passwd`

			=> on voit un "s" dans les droits de l'utilisateur (-rwsr-xr-x)

		Ceci est parce qu'on souhaite que tout le monde puisse changer son mot de passe SANS pour autant pouvoir aller modifier le fichier des mot de passe.
		Donc pour l'action de changer son propre mot de passe on a les droits root, mais c'est tout.

		La notion de setuid n'existe pas pour les dossiers.




	- setgid

		C'est le même fonctionnement que setuid mais cette fois-ci pour le groupe et pas pour l'utilisateur.

---------------------------------------------------------
---------------------------------------------------------
---------------------------------------------------------		


## ACCES CONTROL LISTS (ACLs)

--------------------------------------------------------------
--------------------------------------------------------------
--------------------------------------------------------------
