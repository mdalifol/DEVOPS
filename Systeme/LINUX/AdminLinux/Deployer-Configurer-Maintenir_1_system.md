################## DEPLOY, CONFIGURE MAINTAIN SYSTEM ###################



## RECUPERATION DES INFOS HOSTNAME ET NETWORKING


	- Remarque: ipconfig est remplacé par ip dans redHat 7

		man ip:
				ip addr								montre toutes les machines locales et leurs adresses
				ip addr show eth0			montre toutes les machines sur le reseau eth0

				ip -s 								montre des statistiques
						ex: ip -s link show eth0


	- Pour savoir si une machine est connectée sur le réseau:

				ping <IPadresse>
				ping6 <IPV6adresse>			pour la connexion avec une adresse IPV6

	- pour connaitre le chemin des paquets

				tracepath <IPadresse>		tous les routeurs ne supportent pas tracepath 	
				traceroute <IPadresse>

						yum install traceroute

	-  récupération d'informations du reseau:

				netstat						vieux => remplacé par ss
				ss 		
						ss [OPTIONS] [NOM-FILTRE] [ARGUMENTS]

						ss | less			liste toutes les connexions
						ss -a 				idem
						ss -t 				liste les connexiosn TCP (attention: ne sont affichées que les connexions established ou connected)
						ss -u 				liste les connexions UDP
						ss -x 				liste les connexions unix

						ss -lt				liste les connexions TCP en "listening"
						ss -lpt 			liste le nom du processus lié à son pid

						ss -s 				affiche des stats (comme ss)

## SAVOIR SI LA MACHINE EST CONNECTEE A UN RESEAU

				ls /sys/class/net			affiche les différents device (eth0, eth1..., lo)



## CREER UNE CONNEXION

	- les fichiers de config reseau sont dans:

							/etc/sysconfig/network-scripts/

			nm

				nmcli dev status			affiche si notre device est connecté

						nmcli: network control manager

				nm-connection-editor		affiche le GUI network connections

				nmtui						affiche la fenetre de configuration du reseau

				nmcli con show				affiche toutes les connexions de la machine

				nmcli con add con-name "maConnexion"
											crée une nouvelle connexion (attention terminer la commande)


				nmcli con add con-name "maConnexion" autoconnect yes type ethernet ifname eth0
							autoconnect	yes 			pour que la connexion soit automatique
							type ethernet				via ethernet
							ifname eth0 				via eth0 (on peut en ajouter)

						par defaut si on ne dit pas par quel moyen se fait cette connexion, un fichier de configuration est créé (/etc/sysconfig/network-scripts/ifcfg-maConnexion) et on voit que le moyen est DHCP(BOOTPROTO) et autoconnect (ONBOOT=yes)


				nmcli con add con-name "maConnexion" autoconnect yes type ethernet ifname eth0 ip4 10.0.0.16 gw4 10.0.0.1

						ip4 10.0.0.16		on a forcer la création sur l'adresse IP 10.0.0.16
						gw4 10.0.0.1		passerelle à utiliser avec cette connexion
							=> on peut forcer la route des paquets

						ici BOOTPROTO n'est pas DHCP

				nmcli con mod nomParametre		permet de modifier le parametre
						ex: nmcli con mod connection.autoconnect no
								=> modifie le parametre ONBOOT à no



				nmcli con down "maConnexion"	stop la connexion

				nmcli con del "maConnexion"		supprime la connexion et son fichier de conf




## 	DNS et  HOSTNAME

	=> Les fichiers de config sont dans /etc

	- Fichier resolv.conf

			search: 		affiche le hostname
			nameserver		affiche l'ip du serveur

	- Fichier /etc/hosts

			On peut éditer ce fichier (donc le poisonner 'pentest') => on peut forcer la redirection de noms de domaines vers des ip modifiées dans ce fichier.
			=> Si on modifie ce fichier et qu'on utilise le DHCP, il faut s'assurer que cela fonctionne toujours => il y a des modifications à faire

	- Fichier /etc/nsswitch.conf

			Sur la ligne "hosts" on voit
						files	dns		myhostname
				=> c'est l'ordre dans lequel il va chercher les adresse des domaines.
				Si on modifie cet ordre cela peut tout changer

	- nom hostname de ma machine

							hostname		affiche le hostname de la machine

			=> on peut changer ce nom de manière persistante:

							hostnamectl set-hostname nomChoisit.com




## CRON

	Tout d'abord s'assurer que les at utilities soient installées (at permet de planifier des taches)

								yum install at

	On rend cela utilisable
								systemctl enable atd
								systemctl start atd


	On crée une tache planifiée

				at 12:00am			crée une tache palnifiée tous les jours à midi (=> maintenat at demande la commande à faire)
						reboot		un reboot se fera tous les jours à midi
					ctrlD pour valider

				at now +1 minute	crée une tache dans 1 minute



	On liste les taches planifiée

				atq					affchie le liste des taches avec un numéro de tache en début de ligne

	On supprime une tache planifiée

				atrm numTache
					ex: atrm 1	supprime la tache 1 de la liste atq

















##
