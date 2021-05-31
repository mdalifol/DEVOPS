############ SECURISER UN SERVEUR LINUX ##########


## FILTRER LE TRAFIC

    1. Installer iptables

        Il faut installer 1 firewall: iptables

                apt-get install iptables

    2. Declaration des règles

    REMARQUE: la commande iptables -L -v vous permettra de consulter les règles courantes

        - Filtrage Intégral

            1. creer le script
                nano /etc/init.d/firewall       -> #!/bin/sh        à écrire pour le déclarer en tant que script shell

            2. effacer les anciennes regles pour repartir à 0
                iptables -t filter -F
                iptables -t filter -X

            3. bloquer par défaut tout le trafic
            REMARQUE: si vous etes connecté en ssh, n'executez pas encore le script sinon vous allez perdre la connexion
                iptables -t filter -P INPUT DROP
                iptables -t filter -P FORWARD DROP
                iptables -t filter -P OUTPUT DROP

            4. on ne ferme pas les connexions déjà établies
                iptables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
                iptables -A OUTPUT -m state --state RELATED,ESTABLISHED -j ACCEPT

            5. on autorise le loopback
                iptables -t filter -A INPUT -i lo -j ACCEPT
                iptables -t filter -A OUTPUT -o lo -j ACCEPT

            Tout est bloqué, il ne nous reste plus qu’à ouvrir les ports utilisés.

        - Ouvrir les Ports A Utiliser    

           6. cas particulier du ping
                iptables -t filter -A INPUT -p icmp -j ACCEPT
                iptables -t filter -A OUTPUT -p icmp -j ACCEPT

                Le protocole icmp n'a pas de port pre-defini MAIS il faut absolument l'autoriser

            7. instaurer les regles
                iptables -t filter -A INPUT/OUTPUT -p protocole --dport port_a_ouvrir -j ACCEPT     (la syntaxe)

                    ex: avec un serveur web
                        iptables -t filter -A OUTPUT -p tcp --dport 80 -j ACCEPT
                        iptables -t filter -A INPUT -p tcp --dport 80 -j ACCEPT

        - Démarrage du Firewall                

                chmod +x /etc/init.d/firewall
                /etc/init.d/firewall

            Tester abondamment que tout se passe bien. Vous pouvez notamment utiliser l’utilitaire nmap pour vérifier qu’il n’y a pas plus de ports ouverts que voulu.  



    REMARQUE: Il est important de charger ce script au démarrage de la machine afin qu’un simple reboot ne vous laisse pas sans protection :

                update-rc.d firewall defaults






## EXEMPLE DE SCRIPT


    script basique autorisant le minimum vital pour un serveur web (HTTP, FTP, mail et résolution de DNS)


            Script:

                    #!/bin/sh

                    # Réinitialise les règles
                    sudo iptables -t filter -F
                    sudo iptables -t filter -X

                    # Bloque tout le trafic
                    sudo iptables -t filter -P INPUT DROP
                    sudo iptables -t filter -P FORWARD DROP
                    sudo iptables -t filter -P OUTPUT DROP

                    # Autorise les connexions déjà établies et localhost
                    sudo iptables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
                    sudo iptables -A OUTPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
                    sudo iptables -t filter -A INPUT -i lo -j ACCEPT
                    sudo iptables -t filter -A OUTPUT -o lo -j ACCEPT

                    # ICMP (Ping)
                    sudo iptables -t filter -A INPUT -p icmp -j ACCEPT
                    sudo iptables -t filter -A OUTPUT -p icmp -j ACCEPT

                    # SSH
                    sudo iptables -t filter -A INPUT -p tcp --dport 22 -j ACCEPT
                    sudo iptables -t filter -A OUTPUT -p tcp --dport 22 -j ACCEPT

                    # DNS
                    sudo iptables -t filter -A OUTPUT -p tcp --dport 53 -j ACCEPT
                    sudo iptables -t filter -A OUTPUT -p udp --dport 53 -j ACCEPT
                    sudo iptables -t filter -A INPUT -p tcp --dport 53 -j ACCEPT
                    sudo iptables -t filter -A INPUT -p udp --dport 53 -j ACCEPT

                    # HTTP
                    sudo iptables -t filter -A OUTPUT -p tcp --dport 80 -j ACCEPT
                    sudo iptables -t filter -A INPUT -p tcp --dport 80 -j ACCEPT

                    # FTP
                    sudo iptables -t filter -A OUTPUT -p tcp --dport 20:21 -j ACCEPT
                    sudo iptables -t filter -A INPUT -p tcp --dport 20:21 -j ACCEPT

                    # Mail SMTP
                    iptables -t filter -A INPUT -p tcp --dport 25 -j ACCEPT
                    iptables -t filter -A OUTPUT -p tcp --dport 25 -j ACCEPT

                    # Mail POP3
                    iptables -t filter -A INPUT -p tcp --dport 110 -j ACCEPT
                    iptables -t filter -A OUTPUT -p tcp --dport 110 -j ACCEPT

                    # Mail IMAP
                    iptables -t filter -A INPUT -p tcp --dport 143 -j ACCEPT
                    iptables -t filter -A OUTPUT -p tcp --dport 143 -j ACCEPT

                    # NTP (horloge du serveur)
                    sudo iptables -t filter -A OUTPUT -p udp --dport 123 -j ACCEPT






## ALLER PLUS LOIN

    Le firewall, outre que c’est l’outil de base de tout système de sécurité, permet des manipulations plus poussées que filtrer des ports.

    1. Flood ou deni de service

        - Ce genre d’attaque vise à surcharger la machine de requête. Il est possible de s’en prémunir pas mal directement au niveau du firewall :

            iptables -A FORWARD -p tcp --syn -m limit --limit 1/second -j ACCEPT

        Le flags TCP syn engendre des demandes de connexions, et le but de cette règle est donc de les limiter à une par seconde (champs limit).
        Il est cependant déconseillé de monter au-delà de la seconde (sous peine de gêner le contrôle de flux et la récupération d’erreur de TCP).


        - On peut faire de même avec les protocoles UDP et ICMP :

            iptables -A FORWARD -p udp -m limit --limit 1/second -j ACCEPT
            iptables -A FORWARD -p icmp --icmp-type echo-request -m limit --limit 1/second -j ACCEPT

        ce type d’attaque permet de faire tomber le serveur, mais pas d’en prendre l’accès. C’est pourquoi le risque d’en être la cible est assez mince (sauf si l’on s’appelle Google). En général, un logiciel simple anti-intrusion comme fail2ban est suffisant, suivant le niveau de sécurité recherché.


    2. Scan de ports

        On peut aussi limiter un tant soit peu le scan de ports (qui consiste à tester tous vos ports afin de détecter ceux qui sont ouverts).

            iptables -A FORWARD -p tcp --tcp-flags SYN,ACK,FIN,RST RST -m limit --limit 1/s -j ACCEPT

        Sachant qu’une connexion TCP en bon et due forme requiert trois paquets avec trois flags différents, on voit tout de suite la finesse de cette règle qui peut travailler paquet par paquet.



    3. Bannir une ip

        Si vous repérez dans les logs ou autre une adresse IP suspecte, vous pouvez la bannir aisément au niveau du firewall via la commande :

            iptables -A INPUT -s adresse_ip -j DROP








## SE PREMUNIR CONTRE LES INTRUSIONS

            Actuellement, le firewall va bloquer toutes tentatives de connexions sur les ports fermés. Mais qu’en est-il des ports ouverts ? Afin de contrôler plus précisément ce qui se passe dessus, le firewall n’est pas suffisant et nous allons devoir utiliser d’autres outils, appelés IDS (Intrusion Detection System) et IPS (Intrusion Prevention System). Ces deux catégories de logiciels vont - comme leur nom l’indique - surveiller toute tentative d’intrusion sur le serveur.



            La démarche qui va suivre va vous montrer comment réagir à chaque étape d’une tentative intrusion classique, à savoir :
                1. le scan de port (plus généralement, la collecte d’informations) afin de trouver les vulnérabilités ;
                2. les attaques « simples », témoins d’une faible sécurité ;
                3. l’intrusion (via des techniques qui ne seront pas décrites ici car dépassant notre cadre de travail) ;
                4. l’installation d’un moyen de se logguer sur le serveur à volonté (si l’attaquant parvient jusqu’ici avec succès, on peut dire que la machine lui appartient).



    1. Portsentry (scan de ports)    

            Cet utilitaire permet de bloquer en temps réel la plupart des scans de port connus (même très discrets et échappant aux règles de filtrage du firewall basiques). Je rappelle au passage que scanner les ports signifie tester tous les ports d’une machine afin de déterminer ceux qui sont ouverts (les portes d’entrées en gros). Cependant, il ne faut pas paniquer si votre serveur est la cible d’un simple scan de port, cela sera monnaie courante, et si vous êtes bien protégé, le pirate passera sa route.


            - installation:

                    apt-get install portsentry

            - configuration:

                    nano /etc/portsentry/portsentry.conf

                -> Commentez les lignes KILL_HOSTS_DENY
                -> Décommentez la ligne KILL_ROUTE="/sbin/iptables -I INPUT -s $TARGET$ -j DROP"
                    => Portsentry ajoutera une règle dans le firewall (iptables) pour rejeter les paquets en cas de scans.

            - démarrage:
                On démarre le logiciel (il faut le lancer deux fois, pour TCP et UDP) :

                    portsentry –audp
                    portsentry –atcp

            REMARQUE:   - Vous pouvez tester tout ça avec nmap (si vous voulez tester en local, il vous faut modifier le fichier portsentry.ignore en enlevant le localhost).
                        - Si vous souhaitez que vos réglages restent même après un nouveau lancement de portsentry, vous devrez modifier le fichier portsentry.ignore.static




    2. Fail2ban (brute-force, dictionnaire, déni de service)    (https://www.fail2ban.org/wiki/index.php/Main_Page)

            Les ports ouverts sur la machine sont à priori sans grande protection, et sujet à des attaques simples telles que la tentative de connexion par brute-force ou par dictionnaire (par exemple, tester toutes les combinaisons de mots de passe pour se logguer en ssh), le déni de services (surcharger le serveur de requêtes) ou - plus bêtement - la recherche d’utilisateurs sans mots de passe... Si votre machine est infiltrée aussi facilement, l’attaquant sera vraiment content.

            Fail2ban est un petit utilitaire qui se base sur les logs de la machine pour chercher des actions suspectes répétées (par exemple, des erreurs de mots de passe) dans un laps de temps donné. S’il en trouve, il bannira l’IP de l’attaquant via iptables. Ce type de logiciel est indispensable, car, bien que léger, il offre une bonne protection contre les attaques basiques indiquées ci-dessus.


            - installation:

                    apt-get install fail2ban


            - configuration:

                    nano /etc/fail2ban/jail.conf

                -> remplir le champ:
                    - destemail : indiquez une adresse mail si vous voulez recevoir des mails d’alerte de la part de fail2ban
                -> Le niveau de protection peut être modulé via les champs suivants (notez que la configuration par défaut suffit normalement) :
                    - bantime : temps de bannissement des IP suspectes
                    - maxretry : nombre de tentatives de connexion permise avant bannissement
                -> Notez que dans la partie JAILS (dans nano : ctrl w => rechercher JAILS) figure tous les services que fail2ban surveillera. Si vous avez modifié les ports par défaut, il faut les indiquer là aussi. Par exemple avec ssh :

                        nano /etc/fail2ban/jail.conf
                        ctrl+w => chercher [ssh]
                        port : indiquer le port    

            - redémarrage:

                    /etc/init.d/fail2ban restart



    3. Snort (détection d’intrusions)   (https://www.snort.org/)


                Le problème quand on commence à sécuriser est de savoir s’arrêter à un moment donné. Snort est un outil très puissant, pouvant en fait détecter la plupart des attaques qui échapperaient à un utilitaire comme fail2ban. Bien entendu, il ne servira pas dans 90 % des cas et comme ce n’est qu’un outil de détection, ce sera à vous de rendre les mesures nécessaires s’il détecte une intrusion. Enfin, comme il analyse le trafic en temps réel, cela ralentit forcément un peu les flux. L’installer n’est donc pas indispensable, cela dépend du degré de sécurité recherché !





    4. Rkhunter (rootkit et backdoors)         (http://rkhunter.sourceforge.net/)


                Les backdoors. Si par malheur un attaquant arrive à prendre possession de votre machine, il y a fort à parier qu’il y laisse une backdoor (porte dérobée) qui lui permettrait d’en reprendre le contrôle plus tard, ainsi qu’un rootkit pour la dissimuler : l’attaquant maintient ainsi un accès frauduleux à votre machine.

                Rkhunter est un utilitaire qui est chargé de détecter d’éventuels rootkits sur votre serveur. Il est relativement léger (s’exécute une fois par jour par défaut) donc on aurait tort de se priver.


                - installation:

                        apt-get install rkhunter


                - configuration:

                        nano /etc/default/rkhunter

                    -> REPORT_EMAIL : indiquez un mail pour recevoir des alertes de Rkhunter
                    -> CRON_DAILY_RUN : mettez « yes » pour une vérification quotidienne de votre machine via un cron





## SURVEILLER LES LOGS


        La plupart des logiciels cités plus haut vous enverront des notifications par mail en cas d’alerte. Cependant, surveiller les logs est important, car ils reflètent la « vie » de votre serveur. Les logs les plus intéressants sont notamment :

            - /var/log/auth.log qui contient toutes les tentatives d’accès au serveur. Il peut être utile de filtrer le contenu,
                    par exemple : cat /var/log/auth.log | grep authentication failure ;
            - /var/log/message et /var/log/syslog contient un peu de tout (erreurs, bugs, informations, etc) ;
            - /var/log/fail2ban est le log d’alerte de fail2ban. Cherchez notamment : cat /var/log/fail2ban | grep ban ;
            - /var/log/snort/alert vous indiquera les logs d’alertes de Snort ;
            - /var/log/rkhunter pour voir les rapports quotidien de Rkhunter. Faites attention aux erreurs trouvées, ce n’est pas bon signe (même si le risque de faux positifs existe ici).


    1. Logwatch (https://doc.ubuntu-fr.org/logwatch)

            Il est aussi possible d’utiliser des utilitaires qui vous simplifient un peu ce travail de lecture des logs. Logwatch notamment permet de résumer plusieurs logs afin de ne vous retourner que des anomalies si possible. Cela évite un long et fastidieux travail de recherche.


            - installation:

                    apt-get install logwatch


            - configuration:

                    nano /usr/share/logwatch/default.conf/logwatch.conf

                -> Spécifiez l’option « MailTo » car logwatch envoie ses résumés de logs par mail.
                -> Il va normalement s’exécuter tous les jours (ls -l /etc/cron.daily/ | grep logwatch pour s’en assurer).    



## DU BON USAGE DE SON SERVEUR


    1. Le bon sens

            90 % des problèmes informatiques relèvent de l’utilisateur. C’est pourquoi, avant même de penser à sécuriser sa machine, il faut garder en mémoire quelques règles de bon sens :

                - interdire les utilisateurs sans mot de passe (ce sont d’énormes failles potentielles) ;
                - toujours choisir de bons mots de passe : 8 caractères minimum, pas un mot qui se trouve dans le dictionnaire, si possible des chiffres, des majuscules, des symboles... au besoin, un outil comme pwgen vous en génèrera automatiquement des bons (apt-get install pwgen) ;
                - maintenir son système à jour (apt-get update et apt-get upgrade) ;
                - toujours utiliser ssh pour l’accès à distance (et non telnet ou des services graphiques, sauf s’ils sont en tunnel à travers ssh).



    2. Configurer les applications

            Il peut aussi être bon d’améliorer un peu la sécurité des logiciels installés sur la machine, car il seront en première ligne pour traiter les paquets autorisés.


    3. ssh

            En premier lieu, il faut regarder du côté de ssh, puisque c’est tout de même un accès direct à votre machine.


                nano /etc/ssh/sshd_config

            il est conseillé de changer les champs suivants :
                - Port : le port par défaut est 22... et n’importe quel attaquant le sait. Changer le port force à effectuer un scan (ou équivalent) avant de réfléchir à attaquer (attention de bien changer le port dans le firewall) ;
                - PermitRootLogin : mettre à « no » afin d’interdire le login en root ;
                - AllowUsers : indique une liste d’utilisateur autorisé à se connecter via ssh. Cela peut être utile si vous avez des utilisateurs qui ne sont pas censés se connecter sur la machine.    

            Puis redémarrer:

                /etc/init.d/ssh restart



    4. apache


            Apache - le serveur web le plus courant - donne par défaut de nombreuses informations à quiconque s’y connecte. Vu que cela ne sert à rien que votre serveur web donne au monde votre distribution Linux, autant limiter cela :

                nano /etc/apache2/apache2.conf

            -> Passez ServerSignature à « off » et ServerTokens à « Prod » pour rendre votre serveur web plus discret.


            Redemarrer le serveur:

                /etc/init.d/apache2 restart



    5. autres


            Pour la plupart des logiciels de base, il existe quelques recommandations de prudence. Voici une liste non exhaustive :

            mysql           => interdire les accès sans mot de passe (on peut exécuter l’utilitaire
                                  /usr/bin/mysql_secure_installation fourni avec mysql-server)

            ftp             => ne surtout pas créer de FTP anonymes

            mail            => utiliser un anti-spam (spamassassin par exemple) et - si possible - utiliser les connexions sécurisées (SSL ou TLS) offertes par tout serveur de mail qui se respecte

            ...






## TESTER LA SURETE DE SON SERVEUR



    1. Scanner de port

            nmap est le meilleur outil de scan de ports : il va tenter d’ouvrir des connexions sur un grand nombre de ports de votre machine afin de déterminer s’ils sont ouverts ou non.


            - installation:

                    apt-get install nmap

            - test le plus incisif possible:

                    nmap -v ip_ou_nom_de_la_machine

                -> Vous aurez alors la liste des ports ouverts
                -> Vous pouvez aussi tester un port en particulier avec l’argument -p port


    2. Scanner la vulnérabilité

            Là, le but est de chercher en général les failles de votre machine. Et il convient de les régler toutes si possible, car l’attaquant peut les trouver aussi bien que vous.

            Pour cela, Nessus est un des utilitaires les plus performants. Comme le logiciel est propriétaire et qu’il s’utilise via une interface graphique.
            => https://www.tenable.com/products/nessus-vulnerability-scanner

            ou son équivalent OpenVAS
            => http://www.openvas.org/
