########## SERVEURS LINUX ##########


## PARTITIONNEMENT

    ATTENTION /home NE DOIT PAS compromettre /boot
        => utiliser plusieurs partitions

        - swap      => 2.5 fois la taille de la RAM
                            type: primaire
                            emplacement: debut
                            utiliser comme: espace d'échange(swap)

        - /boot     => taille 100MB
                            type: primaire
                            utiliser comme: ext4
                            pt de montage: /boot
                            marquer la partition comme bootable(indicateur d'amorçage = present)

        - /         => 8Go minimum
                            type: primaire
                            pt de montage: /

        - /home     =>
                            type: primaire
                            pt de montage: /home


							
							
##### PARTITIONS							
							
	MBR: Master Boot Record
	
		Le MBR contient la table des partitions (4 primaires) et une routine d'amorçage (boot loader).
		MBR fonctionne avec des disques de 2 To max.
		Le Bios charge le MBR (512 premiers octets).
							
							
							
	GPT: Guid Partition Table

		Cette nouvelle norme permet de remplacer le MBR.
		Elle est associée à UEFI(qui remplace le Bios).
		Elle n'a pas de limite de taille de disque, pas de limite du nb de partitions.
		Les données de partitionnement sont copiées à différents endroits sur le disque dans le cas de données endomagées.
		
		Chaque partition est reconnaissable grace à son UUID.
		
		Pour créer les partitions utiliser GPT avec la commande gdisk
		
	


	Les partitions apparaissent dans /dev (xvdf...)
	
	
						gdisk /dev/xvdf 
						
						?					=> aide; correspond au man
						
		1. ajouter uen nouvelle partition (choix n)

				=> suivre les instructions
					size KMGTP: +500M
					L 					=> choisir quel linux filesystem installer
					noter son code 		(ex: 7f01: chrome OS)
					

		2. ecrire les tables et exit (choix w)
					Y				pour valider le choix d'écrire une nouvelle partition 


		3. vérifiacation de la création de la partition 
				cd /dev/
				ls
								=> on voit xvdf1 qui est créé, c'est la partition qu'on vient de créer 
								
		
		4. créer un system sur la partition 

						mkfs -t xfs xvdf1 
						
		5. il faut monter la partition
		
						mkdir /mnt/mymount
						mount /dev/xvdf1 /mnt/mymount 
						df -h 
								=> on voit quela partition est bien montée
								
			=> il est préférable de monter cette partition à partir de son UUID 
			
						unmount /mnt/mymount
						blkid
								=> on copie le UUID de xvdf1 
						mount -U <UUID> /mnt/mymount
						
						
						
		
	On peut créer autant de partitions qu'on veut.
				


	Effacer une partition:
	
		1. on demonte la partition 
						
						unmount /mnt/mymount
						gdisk xvdf
						d						=> pour delete
						Y 						=> pour confirmer
							=> c'est fait






#### LVM

	LVM: Logical Volume Manager
	Il permet la création et la gestion de volumes logiques.
	Cela remplace l'utilisation de partitions.
	
	Il permet de diminuer la taille d'un système de fichier pour pouvoir en agrandir un autre, sans se préoccuper de leur emplacement sur le disque.
	Il n'y a pas de limitations « étranges » comme avec les partitions (primaire, étendue, etc.).
	On ne se préoccupe plus de l'emplacement exact des données.
	On peut conserver quelques giga-octets de libres pour pouvoir les ajouter n'importe où et n'importe quand.
	Les opérations de redimensionnement deviennent quasiment sans risques, contrairement au redimensionnement des partitions.

	Si un des volumes physiques devient HS, alors c'est l'ensemble des volumes logiques qui utilisent ce volume physique qui sont perdus.
		=> faire du raid.
		
	
	Les partitions LVM sont formatées en tant que volumes physiques. Un ou plusieurs volumes physiques sont combinés afin de former un groupe de volumes. La capacité totale de stockage de chaque groupe de volumes est ensuite divisée en un ou plusieurs volumes logiques. Les volumes logiques fonctionnent de manière similaire à des partitions standard. Ils possèdent un type de système de fichiers, comme xfs, ainsi qu'un point de montage.
	
	Pour mieux comprendre LVM, pensez à un volume physique comme s'il s'agissait d'une pile de blocs. Un bloc est une unité de stockage utilisée pour stocker des données. Plusieurs piles de blocs peuvent être combinées pour faire une pile de taille bien plus importante, tout comme les volumes physiques peuvent être combinés pour former un groupe de volumes. La pile en résultant peut être sous-divisée en plusieurs piles de plus petites tailles, leurs tailles étant arbitraires, tout comme un groupe de volumes peut être alloué à plusieurs volumes logiques.
	
	
		1. Il faut créer des volumes physiques:
		
		
							pvcreate /urlVolumePhysiqueACreer 
									ex: pvcreate /dev/xvdf1 
					
					
			Pour visualiser les volumes:
			
							pvdisplay
							
					=> nous montre les infos des volumes physiques 		
		
		2. on crée un groupe de volume 
		
		
							vgcreate nomGroupeDeVolume /urlVolumePhysique
										ex: vgcreate battlestar /dev/xvdf1 
							
							
					
			Pour visualiser les groupes:
			
							vgdisplay
					
					=> nous montre tous les groupes de volumes 
							
		
		3. on crée dans ce groupe des volumes logiques
	
							lvcreate -n nomVolumeLogique -L taille nomGoupeDeVolume
										ex: lvcreate -n galactica -L 5G battlestar 
									
				REMARQUE: marche aussi avec le swap, il suffit de noter "swap" comme nomVolumeLogique, par contre dans ce cas l'opération de config sera légèrement différente					
							
							
					
			Pour visualiser les volumes:
			
							lvdisplay
							
					=> nous montre tous les volumes logiques		
					
					
		4. on supprime les volumes:
		
			4.1. volume logique 
			
			4.2 groupe de volume
			
			4.3 volume physique
			
			
		5. on veut étendre le volume logique d'un groupe (on ajoute un nouveau volume logique):
		
							vgextend nomGroupeDeVolume /urlVolumeLogiqueAAjouter 
										ex: vgextend battlestar /dev/xvdj 
		
		6. on veut étendre le groupe de volume après avoir ajouter un volume physique (on ajoute le volume physique au groupe de volume à un volume logique):
		
							lvextend -L +size /urlVolumeLogique 
										ex: lvextend -L +20G /dev/battlestar/galactica 
					
					=> on a ajouté 20G sur le volume logique galactica (après avoir ajouter un volume physique d'au moins 20G au groupe de volume)
					
					
		7. après avoir augmenté la taille du volume logique, on veut que cette augmentation soit prise en compte:
		
							xfs_growfs /mnt/nomVolumeMonté
										ex: xfs_growfs /mnt/xfs 
										
										
		8. on déplace une extension physique vers un auter volume physique associé au même groupe de volume
		
							pvmove /nomExtensionADeplacer 
										ex: pvmove /dev/xvdf1
										
		9. reduire un groupe de volume en supprimant une partition de ce groupe 
		
							vgreduce nomGroupeDeVolume /urlPartition 
										ex: vgredure battlestar /dev/xvdf1
								
		
		

## CONFIGURATION

    1. Cas du swap (en LVM)
	
		- Il faut formater ce volume en lui donnat une signature indiquant que c'est un swap 
		
						mkswap /urlVolumeLogiqueSwap
										ex: mkswap /dev/battlestar/swap 
										
		- L'UUID du volume est indiqué, nous allons l'utiliser pour monter ce swap.

						swapon /urlDiskAUtiliser			(urlVolumeLogiqueSwap)
										ex: swapon /dev/battlestar/swap
		
		- On vérifie la mémoire qui est libre 
		
						free -m 
						
			=> le swap apparait avec tout qui doit être libre (=> ila bien été créé)
			
		- On "éteint" le swap 
						
						swapoff /urlDiskAUtiliser
										ex: swapoff /dev/battlestar/swap
										
		- On vérifie la mémoire libre 
		
						free -m 
						
			=> le swap a disparu 
	
		- on va le rendre "persistant" (au mount)
		
						cd /etc 
						vim fstab
						
			=> on va monter le swap disk 
			
		- on rentre l'UUID en précisant que c'est un swap 
		
						UUID=numUUIDRécupéréPrécédement 	swap	0 0
				
								swap: indique que c'est un swap 
								0: indique qu'on ne veut pas démarrer (ou dump) sur le swap
									si on veut le faire: 1 à la place de 0
								0: priorité de démarrage (ici non prioritaire)
									sinon 1 est important, 2 un peu moins, 3...., 4....
				
			Remarque: On sépare les infos par des tabulations
			
						wq				save & exit
				
		- on va démarrer le swap 
		
						swapon -a 
						
			Remarque: s'il y a plusieurs entrées "swap" dans le fichier fstab, tous démarreront
			
		- On vérifie qu'il a bien démarré:
		
						free -m 
				=> il apparait bien, c'est OK 
				
		- Si on va voir dans /etc on verra swap avec son nom qui apparait dans la liste
	
	
		
	
	
	
	


## FICHIERS ET REPERTOIRES DE CONFIGURATION A SAUVEGARDER

    /etc/dhcp.conf
    /etc/bind/named.conf
    /etc/firehol/firehol.conf
    /etc/apache2/sites-available/
    /var/cache/bind/
