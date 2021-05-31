# CREATION DE CLE SSH

## DEFINITION

**SSH signifie Secure SHell**. C'est un protocole qui permet de faire des connexions sécurisées (i.e. cryptées) entre un serveur et un client SSH.
**OpenSSH** est la version libre du client et du serveur SSH.


## PRINCIPES

Installer un serveur SSH permet aux utilisateurs d'accéder au système à distance, en rentrant leur login et leur mot de passe (ou avec un mécanisme de clés). Cela signifie aussi qu'un pirate peut essayer d'avoir un compte sur le système (pour accéder à des fichiers sur le système ou pour utiliser le système comme une passerelle pour attaquer d'autres systèmes) en essayant plein de mots de passes différents pour un même login (il peut le faire de manière automatique en s'aidant d'un dictionnaire électronique). On appelle ça une attaque en force brute.

Il y a 3 contraintes majeures pour garder un système sécurisé après avoir installé un serveur SSH :

* avoir un serveur SSH à jour au niveau de la sécurité

* que les mots de passes de TOUS les utilisateurs soient suffisamment complexes pour résister à une attaque en force brute ;

* surveiller les connexions en lisant régulièrement le fichier de log `/var/log/auth.log`.

> _Remarque_: Pour vérifier que les mots de passe des utilisateurs du système sont vraiment complexes, le root peut les soumettre à un cracker de mots de passe... et voir combien de temps ils résistent !
Les mots de passes des utilisateurs sont stockés dans le fichier `/etc/shadow`. Seul l'utilisateur root peut lire ce fichier. Pour tester la complexité des mots de passes, le root peut donc installer le programme john et le lancer sur le fichier /etc/shadow :
      `apt-get install john
      john /etc/shadow`
Quand john a trouvé un mot de passe, il l'affiche avec le login associé.
Attention, john utilisera le processeur à 100 % ! Il est donc conseillé de lui donner un priorité faible (commande `nice` ou `renice`) si la machine doit être utilisée pendant ce temps. Plus le nombre d'utilisateurs est grand, plus il faudra laisser tourner john longtemps pour que le test soit significatif.

## LES SYSTEMES DE CLES SSH

1. **La théorie de la cryptographie asymétrique**

  **SSH utilise la cryptographie asymétrique RSA ou DSA**.
  En cryptographie asymétrique, chaque personne dispose d'un couple de clé : une clé publique et une clé privée. La clé publique peut être librement publiée tandis que la clé privée doit rester secrète. La connaissance de la clé publique ne permet pas d'en déduire la clé privée.

  >_Si la personne A veut envoyer un message confidentiel à la personne B, A crypte le message avec la clé publique de B et l'envoie à B sur un canal qui n'est pas forcément sécurisé. Seul B pourra décrypter le message en utilisant sa clé privée_.

2. **La théorie de la cryptographie symétrique**

  **SSH utilise également la cryptographie symétrique**.

  >_Si A veut envoyer un message confidentiel à B, A et B doivent d'abord posséder une même clé secrète. A crypte le message avec la clé secrète et l'envoie à B sur un canal qui n'est pas forcément sécurisé. B décrypte le message grâce à la clé secrète. Toute autre personne en possession de la clé secrète peut décrypter le message_.

  **La cryptographie symétrique est beaucoup moins gourmande en ressources processeur que la cryptographie asymétrique... mais le gros problème est l'échange de la clé secrète entre A et B**.

  Dans le protocole SSL, qui est utilisé par SSH et par les navigateurs Web, la cryptographie asymétrique est utilisée au début de la communication pour que A et B puissent s'échanger une clé secrète de manière sécurisée... puis la suite la communication est sécurisée grâce à la cryptographie symétrique en utilisant la clé secrète échangée.

3. **L'établissement d'une connexion SSH**

  Un serveur SSH dispose d'un couple de clés RSA stocké dans le répertoire `/etc/ssh/` et généré lors de l'installation du serveur.
  - Le fichier `ssh_host_rsa_key` contient la **clé privée** et a les **permissions 600**.
  - Le fichier `ssh_host_rsa_key.pub` contient la **clé publique** et a les **permissions 644**.


  * Nous allons suivre par étapes l'établissement d'une connexion SSH :

    * Le serveur envoie sa clé publique au client.

    * Le client génère une clé secrète et l'envoie au serveur, en cryptant l'échange avec la clé publique du serveur (cryptographique asymétrique). Le serveur décrypte la clé secrète en utilisant sa clé privée, ce qui prouve qu'il est bien le vrai serveur.

    * Pour le prouver au client, il crypte un message standard avec la clé secrète et l'envoie au client. Si le client retrouve le message standard en utilisant la clé secrète, il a la preuve que le serveur est bien le vrai serveur.

    * Une fois la clé secrète échangée, le client et le serveur peuvent alors établir un canal sécurisé grâce à la clé secrète commune (cryptographie symétrique).

    * Une fois que le canal sécurisé est en place, le client va pouvoir envoyer au serveur le login et le mot de passe de l'utilisateur pour vérification. La canal sécurisé reste en place jusqu'à ce que l'utilisateur se déloggue.

    > La seule contrainte est de s'assurer que la clé publique présentée par le serveur est bien sa clé publique... sinon le client risque de se connecter à un faux serveur qui aurait pris l'adresse IP du vrai serveur (ou toute autre magouille). Une bonne méthode est par exemple de demander à l'administrateur du serveur quelle est le fingerprint de la clé publique du serveur avant de s'y connecter pour la première fois. Le fingerprint d'une clé publique est une chaîne de 32 caractères hexadécimaux unique pour chaque clé ; il s'obtient grâce à la commande `ssh-keygen -l`.

## INSTALLATION ET CONFIGURATION SSH

1. **Installation du client et du serveur SSH**

  Le client et le serveur SSH sont dans le même package ssh. Ce package est installé dès la première utilisation de _dselect_.
  Maintenant que votre système est à jour niveau sécurité, vous pouvez activer le serveur SSH, si vous le souhaitez. Pour cela, supprimez le fichier `/etc/ssh/sshd_not_to_be_run` et lancer SSH :

      `rm /etc/ssh/sshd_not_to_be_run
      /etc/init.d/ssh start
      Starting OpenBSD Secure Shell server: sshd`

2. **Configuration du serveur SSH**

  Le **fichier de configuration du serveur SSH** est `/etc/ssh/sshd_config`.
  A ne pas confondre avec le fichier `/etc/ssh/ssh_config`, qui est le **fichier de configuration du client SSH**.

  Nous allons vous commenter les lignes les plus importantes de ce fichier de configuration :

      `Port 22`

> Signifie que le serveur SSH écoute sur le port 22, qui est le port par défaut de SSH. Vous pouvez le faire écouter sur un autre port en changeant cette ligne. Vous pouvez aussi le faire écouter sur plusieurs ports à la fois en rajoutant des lignes similaires.

    `Protocol 2`

> Signifie que votre serveur SSH accepte uniquement la version 2 du protocole SSH. C'est une version plus sécurisée que la version 1 du protocole. Seuls certains vieux clients SSH ne savent faire que du SSH version 1. Si vous voulez que le serveur accepte les deux protocoles, changez la ligne en :
    `Protocol 2,1`

    `PermitRootLogin yes`

> Signifie que vous pouvez vous logguer en root par SSH. Vous pouvez changer et mettre "no", ce qui signifie que pour vous connecter en root à distance, vous devrez d'abord vous connecter par SSH en tant que simple utilisateur, puis utiliser la commande su pour devenir root. C'est une sorte de double protection.

    `X11Forwarding yes`

> Signifie que vous allez pouvoir travailler en export display par SSH.

Si vous avez modifié le fichier de configuration du serveur, il faut lui dire de relire son fichier de configuration :

    `/etc/init.d/ssh reload
    Reloading OpenBSD Secure Shell server's configuration`    

## SE LOGGUER EN SSH

1. **Authentification par mot de passe**

	C'est la méthode la plus simple. Depuis la machine cliente, tapez :

	`ssh login@nom_DNS_du_serveur_SSH`

-   Si c'est la première connexion SSH depuis ce client vers ce serveur, il vous demande si le fingerprint de la clé publique présentée par le serveur est bien le bon. Pour être sûr que vous vous connectez au bon serveur, vous devez connaître de façon certaine le fingerprint de sa clé publique et la comparer à celle qu'il vous affiche. Si les deux fingerprints sont identiques, répondez  **_yes_**, et **la clé publique du serveur est alors rajoutée au fichier  `~/.ssh/known_hosts`**.

-   Si vous vous êtes déjà connecté depuis ce client vers le serveur, sa clé publique est déjà dans le fichier  ~/.ssh/known_hosts  et il ne vous demande donc rien.


	Ensuite, entrez votre mot de passe... et vous verrez apparaître le prompt, comme si vous vous êtiez loggué en local sur la machine.

2. **Authentification par clé**

	Au lieu de s'authentifier par mot de passe, les utilisateurs peuvent s'authentifier grâce à la cryptographie asymétrique et son couple de clés privée/publique, comme le fait le serveur SSH auprès du client SSH.

* **Générer ses clés**

	Pour générer un couple de clés DSA, tapez :

		`ssh-keygen -t dsa`


	Par défaut (il demande confirmation lors du processus de création), la **clé privée** est stockée dans le fichier  `~/.ssh/id_dsa`  avec les **permissions 600** et la **clé publique** est stockée dans le fichier  `~/.ssh/id_dsa.pub`  avec les **permissions 644**.

	Lors de la création, il vous demande une  _pass phrase_  qui est un mot de passe pour protéger la clé privée. Cette  _pass phrase_  sert à crypter la clé privée. La  _pass phrase_  vous sera alors demandée à chaque utilisation de la clé privée, c'est à dire à chaque fois que vous vous logguerez en utilisant cette méthode d'autentification. Un mécanisme appelé  _ssh-agent_  permet de ne pas rentrer le mot de passe à chaque fois.

	_Remarque_: Vous pouvez à tout moment changer la  _pass phrase_  qui protège votre clé privée avec la commande  **ssh-keygen -p**.

* **Autoriser votre clé publique**

	Pour cela, il suffit de **copier votre clé publique dans le fichier**  `~/.ssh/authorized_keys`  de la machine sur laquelle vous voulez vous logguer à distance. La commande suivante permet de réaliser cette opération via SSH :

		`ssh-copy-id -i ~/.ssh/id_dsa.pub login@nom_DNS_du_serveur`

et entrez le mot de passe de votre compte sur le serveur.

* **Se logguer**

La commande est la même que pour une authentification par mot de passe.


## TRANSFERT DE FICHIERS PAR SSH

#### En console

Le Transfert de fichiers se fait  avec  **scp**  (comme Ssh CoPy), qui s'utilise la même manière que la commande  **cp**.

-   _pour transférer le fichier  **test1.txt**  situé dans le répertoire courant vers le home du compte  **_toto_**  de la machine  **_ordi1.exemple.org_**  sur laquelle tourne un serveur SSH_ :

	    `scp test1.txt toto@ordi1.exemple.org:`

-   _pour récupérer le fichier  **test2.txt**  situé le home de l'utilisateur  **_toto_**  de la machine  **_ordi2.exemple.org_**  et l'**écrire dans le répertoire courant**_ :

	    `scp toto@ordi2.exemple.org:test2.txt`

-   _pour récupérer **tous les fichiers ayant l'extension  .txt**  situés dans le répertoire  **/usr/local**  de la machine  **_ordi2.exemple.org_ ** et l'**écrire dans le sous-répertoire  test-scp  du répertoire courant**_ :

	    `scp toto@ordi2.exemple.org:/usr/local/*.txt test-scp`

-   _pour transférer l'**intégralité du sous-répertoire  test-scp  du répertoire courant** vers le **sous répertoire  incoming**  du home de l'utilisateur  **_toto_**  de la machine  **_ordi1.exemple.org_**_  :

	    `scp -r test-scp toto@ordi1.exemple.org:incoming`

## SE LOGGUER EN SSH SANS TAPER DE MOT DE PASSE

Cette section s'adresse à ceux qui utilisent un **couple de clés publiques / privées**, et qui ont crypté leur clé privée avec une  _pass phrase_  (c'est la configuration la plus sûre). Par conséquent, le client SSH demande la  _pass phrase_  à chaque utilisation des clés pour s'autentifier.

Pour éviter d'avoir à taper systématiquement sa  _pass phrase_, il faut utiliser  _ssh-agent_  : ce programme tourne en tâche de fond et garde la clef en mémoire. La commande  `ssh-add`  permet de donner sa clé à  _ssh-agent_. Ensuite, quand vous utilisez le client SSH, il contacte  _ssh-agent_  pour qu'il lui donne la clé.

##### En console

Dans une console, ouvrez un screen avec  _ssh-agent_  en tâche de fond :

	`ssh-agent screen`

Puis donnez votre clé à l'agent :

	`ssh-add`

Il vous demande alors votre  _pass phrase_. Maintenant que votre clé a été transmise à l'agent, vous pouvez vous connecter sans entrer de mot de passe à toutes les machines pour lesquelles vous avez mis votre clé publique dans le fichier  ~/.ssh/authorized_keys.


## FAIRE DES TUNNELS SSH

Faire un tunnel SSH est un moyen simple de crypter n'importe quelle communication TCP entre votre machine et une machine sur laquelle vous avez un accès SSH.
Par exemple, pour établir un tunnel SSH pour une connexion HTTP vers la machine  _serveur.exemple.org_  :

	`ssh -L 2012:serveur.exemple.org:80 toto@serveur.exemple.org`


> où  _2012_  est le port sur la machine cliente à partir duquel la connexion entre dans le tunnel SSH (le port doit être supérieur à 1024 si on ne veut pas avoir à lançer le tunnel en tant que  _root_).

Ensuite, il suffit de lançer un navigateur Web en lui demandant de se connecter en local sur ce port :

	`w3m http://localhost:2012`

## CREATION DE CLE

  1. Aller dans le répertoire personnel et vérifier qu'un fichier ".ssh" existe.
                `cd ~
                ls -lisah`

  2. Si c'est le cas on peut générer une clé ssh, sinon il faut créer le fichier .ssh
                `mkdir .ssh/`

  3. Créer la clé ssh (on peut le faire de n'importe où?)
                `ssh-keygen -t dsa`

  4. On peut voir dans le dossier .ssh les fichiers id_dsa et id_dsa.pub pour les clés privés et publiques.
                `ls -lisah`

  5. Ce sont les clés dsa en 1024 bits qui sont générées (.pub pour publique, l'autre est donc la privée)
    Pour générer une clé rsa2 utiliser l'option -t rsa
                `ssh-keygen -t rsa`

  6. Générer une passphrase (10 à 30 caractères)

  7. Pour modifier la passphrase sur la clé privée DSA
                `ssh-keygen -p -f ~/.ssh/id_dsa`

  8. Copier la clé publique sur le serveur distant dans ~/.ssh/authorized_keys
                `scp <nomFichierClePublique> <nomUtilisateur>@<nomServeur>:<nomRepertoireDeSortie>`

  9. Le système demande le mot de passe => 1 seule fois, ensuite on peut se connecter sans à avoir à redemander le mot de passe, par contre la passphrase est demandée

  10. Utiliser un agent ssh pour ne pas à avoir à réécrire la passphrase à chaque connexion
