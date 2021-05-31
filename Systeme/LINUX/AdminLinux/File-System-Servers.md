########### CREATE AND CONFIGURE FILE SYSTEM ############


## VFAT, EXT4, XFS

	- tous les files systeme seront sur des disques	=> voyons la liste de ces disques

						cd /dev

	- on crée une partition (pas la peine de faire un LVM => autre methode)

						fdisk nomDisqueAUtiliserPourPartition
								ex: fdisk xvdf

						m 		pour afficher l'aide

						n 		pour créer une nouvelle partition
						p 		pour dire que c'est une primary
						on laisse les valeurs par defaut (on note rien) => on va utiliser toute la taille du disque

						w 		pour enregistrer (écrire et sauver)

				=> la partition est créée (xvdf1)

	- on va formater cette partition avec un file system specifique

			VFAT: extension de FAT créée par Microsoft => utile pour les machines windows
			EXT4: utilisé sur les OS linux
			XFS:  robuste et évolutif: à préférer ce systeme

			* VFAT:

				- création de la partition au format VFAT
					mkfs.vfat /urlPartitionAUtiliser
							ex: mkfs.vfat /dev/xvdf1

				- on crée le dossier pour monter le disque:
					mkdir /mnt/nomPartitionAMonter
							ex: mkdir /mnt/vfat

				- on monte le file system
					mount /dev/xvdf1 /mnt/vfat

				- on vérifie que le file system soit bien monter
					df -h
							=> il apparait

				- on rend ce file system "persistant"
					vim /etc/fstab

					=> on rempli le fichier:

						/dev/xvdf1	/mnt/vfat	vfat defaults	1	2
					=>	nomPartition nomFSystem	formatFSystem	permissions (defaults)	1	2
								1: peut démarrer dessus
								2: pas prioritaire au démarrage

					=> on sauve le fichier
						wq




			* EXT4:

				- création de la partition au format EXT4
					mkfs.ext4 /urlPartitionAUtiliser
							ex: mkfs.ext4 /dev/xvdf1


				- on crée le dossier pour monter le disque:
					mkdir /mnt/nomPartitionAMonter
							ex: mkdir /mnt/ext4

				- on monte le file system
					mount /dev/xvdf1 /mnt/ext4

				- on vérifie que le file system soit bien monter
					df -h
							=> il apparait

				- on rend ce file system "persistant"
					vim /etc/fstab

					=> on rempli le fichier:

						/dev/xvdf1	/mnt/ext4	ext4 defaults	1	2
					=>	nomPartition nomFSystem	formatFSystem	permissions (defaults)	1	2
								1: peut démarrer dessus
								2: pas prioritaire au démarrage

					=> on sauve le fichier
						wq


	REMARQUE: pour les VFAT et EXT4 on agit de la même manière pour vérifier et réparé les problèmes rencontrés:

			VFAT:
				fsck.vfat /urlPartitionAVérifier
							ex: fsck.vfat /dev/xvdf1
			EXT4:				
					fsck /urlPartitionAVérifier
							ex: fsck /dev/xvdf1

			ATTENTION: pour lancer la commande fsck il faut que les volumes soient unmount

				=> 	unmount /dev/ext4
					fsck /dev/xvdf1





			* XFS:

				- création de la partition au format XFS
					mkfs.xfs /urlPartitionAUtiliser
							ex: mkfs.xfs /dev/xvdf1


				- on crée le dossier pour monter le disque:
					mkdir /mnt/nomPartitionAMonter
							ex: mkdir /mnt/xfs

				- on monte le file system
					mount /dev/xvdf1 /mnt/xfs

				- on vérifie que le file system soit bien monter
					df -h
							=> il apparait

				- on rend ce file system "persistant"
					vim /etc/fstab

					=> on rempli le fichier:

						/dev/xvdf1	/mnt/xfs	xfs defaults	1	2
					=>	nomPartition nomFSystem	formatFSystem	permissions (defaults)	1	2
								1: peut démarrer dessus
								2: pas prioritaire au démarrage

					=> on sauve le fichier
						wq


				=> En cas de problèmes:

					- pour connaitre les infos de notr file system XFS

							xfs_info /dev/xvdf1

					- on peut créer un label:

							xfs-admin -L nomLabel /dev/xvdf1

					- on peut réparer les problèmes sur le file system

							xfs_repair /dev/xvdf1

						=> ça répare tout seul (ATTENTION: la partition doit être unmount comme pour ext4)




## CIFS, NFS

	- CIFS: Common Internet File System => utilise le protocole SMB (Server Message Block). Utilisé pour le partage des File System, imprimantes, ports en série sur un network. Il permet le partage de fichier entre linux et windows.

	- NFS: Network File System => permet aux hôtes distants de monter les systèmes de fichiers sur un réseau et d'interagir avec ces systèmes de fichiers


	1. installer les outils necessaires:

							yum -y samba-client cifs-utils nfs-utils

	2. on va monter ces file system et les rendre persistant


---------------------------------------------------------------------------
---------------------------------------------------------------------------
---------------------------------------------------------------------------
