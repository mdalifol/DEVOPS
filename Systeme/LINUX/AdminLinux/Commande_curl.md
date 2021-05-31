# CURL

## PRESENTATION
Curl est un outil open source, en ligne de commande permettant de télécharger n’importe quels fichiers via les divers protocoles. Il est très léger et fonctionne sur pratiquement n’importe quelle plateforme : MAC, Windows, Linux. Il peut être utilisé dans des scripts, sous Linux pour télécharger du contenu. Mais, on oubli très souvent que cet outil permet également de faire un diagnostic précis, de tester ou "benchmarker" des applications.

Par exemple, il peut tester les fonctions web `GET, POST, PUT, ...`

On peut aussi lui faire exécuter des tâches de maintenance comme de la sauvegarde de fichiers ou encore l’envoi de dossiers au travers d’un site FTP, et ce, en traversant des protocoles sécurisés tels que HTTPS, SFTP, etc.


## FONCTION DE TELECHARGEMENT


* Télécharger un fichier à partir d'un site: option `-o`

	`curl –o <fichierDestinationDeTelechargement> http://<nomSite>/<nomFichierATélécharger>`

* Télécharger une page html depuis un site: option `-o`

	`curl –o <mypage.html> http://<nomSite>/<nomPage.html>`

* Télécharger plusieurs fichiers en 1 seule fois: option `-O`

	`curl –O http://<nomSite>/file1 -O http://<nomSite>/file2`

* Télécharger un fichier avec un autre protocole:

	`curl –u phil: --key ~/.ssh/id_rsa --pubkey ~/.ssh/id_rsa.pub scp://site-ssh.mydmn.org`

 _Explication_ :
 * `-u`: connexion utilisateur
 * espace après ":" après nom utilisateur (dans l'option `-u`) est là pour que le mot de passe soit demandée lorsqu'on lance la commande
 * Pour renseigner le mot de passe on note `-u <nomUtilisateur>:<password>`
 * `--key`: précise la clé privée du compte
 * `--pubkey`: renseigne la clé publique

* Télécharger en FTP:

	`curl ftp://<nomutilisateur>:<mot2Passe>@ftp.<nomServeur>:<numPort>/<nomFichierATélécharger>`

 _est équivalent à_

	`curl –u <nomutilisateur>:<mot2Passe> ftp://<nomServeur>:<numPort>/<nomFichierATélécharger>`

* Télécharger sur un serveur FTP-SSL

	`curl --ftp-ssl –u <nomutilisateur>:<mot2Passe> ftp://<nomServeur>:<numPort>/<nomFichierATélécharger>`

* Reprendre un téléchargement interrompu: option `-C - `

	`curl –C - -O http://<nomSite>/<nomFichierATélécharger>`

* Limiter le débit de téléchargement: option `--limit-rate`

	`curl --limit-rate 5MB –O http://<nomSite>/<nomFichierATélécharger>`
 On limite ici le débit pour notre réléchargement à 5MB par seconde.

 * récupérer un fichier que s'il a été modifié après une certaine date: option `-z`

 	`curl –z <JJ-MMM-AA> http://<nomSite>/<nomFichierATélécharger>`

* Uploader un fichier: option `-T`

	`curl –u <nomutilisateur>:<mot2Passe> –T <nomFichierAUploader> ftp://<nomServeurDestination>/`

## COLLECTEUR D'INFORMATION

* Récupération de données: option `-v`

	`curl –v http://<nomSite>/`
Permet de récupérer plus des informations concernant les entêtes http (ou du protocole utilisé)


* Récupération d'une image complète des requêtes effectuées: option `--trace`

	`curl –-trace <fichierDeTrace> http://<nomSite>/`
Dans le fichier _fichierDeTrace_ seront mis les résultat de ce curl (donc l'image complète des requêtes)


* Récupération de la durée des échanges: option `--trace-time`

	`curl –-trace <fichierDeTrace> --trace-time http://<nomSite>/`		

* Récupération de l'entête d'identification du protocole: option `--head`

	`curl –-head http://<nomSite>/`	 

* récupération des réponses à l'entête envoyée précédemment au client: option `-i` (pour include)

	`curl –i http://<nomSite>/`		

## FONCTIONS AVANCEES

* Envoie de message: option `--mail`

	`curl --mail-from <adresseMailExpéditeur> --mail-rcpt <adresseMailDestinataire> <protocoleDeMail>://<serveurdeMail>`

A la suite de cette commande, il est demandé de saisir le message à envoyer.
Cette commande est compatible avec _IMAP_ et _POP3_ qui permettent de télécharger des messages au lieu de les envoyer.

* Fixer un proxy par lequel passer: option `-x`

	`curl -x <nomProxy>:<numeroDePrt> hhtp://<nomSite>`

 * _Remarque_: le numéro de port est celui du port d'achange du proxy.

 * **ATTENTION**: Si des variables concernant un proxy (http_proxy, https_proxy....) ont été mises en place, il **FAUT** les invalider avec:

 	`unset http_proxy`

 	`unset https_proxy`


* Simuler en 1 seule requête l'envoie d'une entête et d'un message de type _GET_:

	`curl –l http://<nomSite> --next <nomSite>`

* Effectuer un message _POST_ suivi d'un _GET_:

	 `curl -d user=<nomUser> http://<nomSite>/post.cgi --next http://<nomSite>/page.html`

_Remarque_: on peut faire de même avec les autres fonctions _PUT_, _DELETE_... du protocole _http_	 


* Enregistrer et ertourner un cookie: option `--cookie`

	`curl --cookie "<nomFichierCookie> http://<nomSite>`

## CURL ET LA SECURITE

Curl supporte les certificats de type client. Dnas ce cas il est nécessaire de renseigner la _passphrase_

	`curl -H "Authorization:Basic NDYxOWVjNDQtNmJjYi00ZGQxLWJmNDktNjA1NTJiMDU3YTM1OjUwZDEzOTJmLWMzYjgtNDhjYy1hNWViLTE0YzM0MWJmMDk5MQ==" https://$(hostname):10002/healthcheckose -kv`

* avec l'authentification codée en base64
* option `-H` pour spécifier l'entête
* option `-k` pour forcer l'accès insécuriser ssl
