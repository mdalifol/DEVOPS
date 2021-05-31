# DOCKER

Voilà un résumé de plusieurs formations docker que j'ai faite afin d'essayer d'avoir une vision suffisamment complète de l'outil.

## Liens
[docker](https://www.docker.com/)
[documentation docker](https://docs.docker.com/)
[docker hub](https://hub.docker.com/)

## Bienvenue avec Docker

**Docker est une solution de virtualisation légère**: une virtualisation de système basée sur des conteneurs Linux.

Ce que nous allons voir:
* **comment exécuter des conteneurs Docker à partir d'images** contenant des applications proposées par les éditeurs ou construire nos propres images avec nos propres applications spécifiques.
* **comment stocker des données et des applications** dans des volumes persistants et comment faire communiquer les conteneurs.


## Comparer les solutions de virtualisation

>Qu'est-ce qu'un conteneur Docker et quelle différence avec une VM?

#### Qu'est-ce  qu'une machine physique ?
- au premier niveau le matériel, le hardware
- au deuxième niveau, l'OS qui gère le matériel
- au troisième niveau, l'application qui s'exécute sur le système d'exploitation.

![architecture docker vs VM](https://www.researchgate.net/profile/Ling-Hong-Hung/publication/299771559/figure/fig4/AS:359778707623937@1462789336136/A-comparison-of-the-architecture-of-virtual-machines-and-Docker-software.png)
----
Avec la multiplicité des applications est apparu énormément de matériel qui coûte cher en maintenance, en consommation électrique...
Un autre problème majeur: le matériel est sous-utilisé, puisque, pour une question de sécurité ou de facilité, nous installons une application par serveur. À un moment, la consolidation est arrivée avec une solution de virtualisation pour pouvoir exécuter plusieurs serveurs sur la même plateforme.

Attention, il faut toujours préciser ce qui est virtualisé.

1. Dans le premier cas, **le matériel**: on parle de machine virtuelle.
**Sur une machine physique**, le _hardware_, nous installons un _operating system_ qui, soit contient automatiquement l'_hyperviseur_, soit nous installons l'**hyperviseur** par-dessus.
Souvent, ces 2 couches fusionnent pour identifier un hyperviseur qui va créer une _virtual machine_ du virtual hardware: du matériel virtuel. **Nous créons des _processeurs virtuels_, de la _mémoire virtuelle_, du _disque virtuel_ et du _réseau virtuel_**.
> On constitue ainsi une machine virtuelle sur laquelle on installe un _operating system_, et ensuite une _application_.

> Sur le même hardware, il est possible d'avoir 2 machines virtuelles qui s'exécutent, qui exécutent 2 operating system différents et 2 applications différentes.

> L'**étanchéité est presque totale**: elle va aller jusqu'au hardware. Donc, les machines virtuelles sont totalement étanches.

Cette technique d'hyperviseur est proposée par exemple par VMware avec vSphere.
Dans le monde Linux: KVM.
Et dans le monde Microsoft, la solution s'appelle Hyper- V.
On parle de virtualisation de hardware, ou de machine virtuelle.

2. Dans le **cas des conteneurs** il n'y a **pas machine virtuelle**.
- au premier niveau: **le matériel**
- puis, l'**OS**
- enfin, les **applications**.
Utilisons une solution qui va créer des **conteneurs** qui permettent de créer un environnement ou un **espace d'exécution étanche par rapport aux autres**.
Nous retrouvons les 3 niveaux: le **hardware**, l'**OS** et l'**espace d'exécution des applications**.
Sauf que, l'**espace d'exécution des applications est segmenté et cloisonné pour pouvoir exécuter les applications dans des conteneurs qui sont étanches**.

> Lorsqu'on se retrouve dans l'application « APP1 », l'application fait appel à l'OS pour lui fournir des ressources matérielles.
On retrouve les 3 couches : applications, OS, matériel.
Donc, c'est l'**équivalent d'une machine**.

>Lorsqu'on se retrouve à l'intérieur de l'application 2, elle aussi fait appel à l'OS et à l'hardware, on a l'impression qu'on se retrouve dans une nouvelle machine.

**Chaque conteneur** doit pouvoir avoir son **propre hostname et sa propre IP**.
Nous sommes dans le contexte d'un vrai serveur, sauf que **les applications 1 et 2 ne se connaissent pas**, sont étanches, ne se voient pas, et elles ont l'impression d'utiliser leur propre OS, alors que l'OS est mutualisé.

Nous parlons de virtualisation d'operating system, de système d'exploitation, ou d'OS à l'inverse de la virtualisation de hardware.

- L'un des premiers intérêts des **conteneurs**, c'est qu'il y a **moins de couches** dans la partie conteneur. Ça veut dire que **l'application « APP1 » ici va accéder plus rapidement aux ressources**.
 - À l'inverse, l'application « APP1 » va accéder à des ressources virtuelles, qui sont interprétées par l'hyperviseur, pour ensuite aller au niveau du hardware.
- Donc, sur le même type de hardware, **l'application 1 dans un conteneur sera beaucoup plus performante** que l'application 1, qui est dans une machine virtuelle.

Un autre aspect positif au niveau des **conteneurs**:
	* **déploiement beaucoup plus rapide et beaucoup plus simple**.
	* **facilement redémarrer un conteneur dans le cas où il y a une panne hardware**,
	*  **redémarrer le conteneur sur un autre système qui exécute une version de Docker**.

**L'inconvénient d'un conteneur, c'est que les 2 applications qui s'exécutent doivent s'exécuter sur le même OS**.

Si vous avez des applications qui doivent s'exécuter sur des OS différents, il faudra revenir sur la solution de virtualisation hardware, pour proposer 2 operating system différents.

Il existe différentes solutions de conteneurs dans les différents systèmes d'exploitation qu'on retrouve sur le marché.
Par exemple, les Zones sur le système Solaris, LXC comme Linux Containers a été une des premières solutions dans le monde Linux, et on retrouve principalement la solution de Docker sur tous les Linux du marché, mais aussi depuis Windows Server 2016, eh bien, Docker existe aussi nativement pour exécuter des conteneurs Windows. Voilà pour ce qui est des conteneurs.

## Architecture de Docker

![architecture docker ](https://docs.docker.com/engine/images/architecture.svg)
------------------------

1. **Docker est composé d'un moteur**, qu'on appelle **docker engine** ou _docker daemon_. **Il s’exécute en mode de service** et va **gérer les conteneurs**.
> Il a la charge d’**exécuter les conteneurs et de gérer l'aspect isolation et l'aspect sécurité des conteneurs**. Il permet aussi de **gérer des ressources**: la consommation mémoire et CPU des conteneurs
> Il est possible d'affecter des ressources particulières à une application qui s’exécute dans un conteneur (ex: en prod + de ressources qu'en recette)

2. **Docker est composé d'un client** qui va **se connecter au serveur docker** et lui soumettre des actions.
> Il peut être installé sur la machine locale ou sur un poste de travail.
> Il y a 2 formes de clients:
> - lignes de commandes
> - interface graphiques (windows et mac)

3. Nous allons **utiliser des images qui contiennent les applications qui vont être exécuter**. **Le conteneur étant l'instance de l'image en cours d’exécution**.
> Les éditeurs doivent fournir les applications sous forme d'images docker pour qu'elles puissent être exécutées dans un conteneur.
> Une image docker est toujours fournie sous forme d'image en lecture seulement.
> Pour les **exécuter**, les **images doivent être présentes sur le serveur** qui exécute docker. **Les images sont distribuées par des registries**, ce qu'on appelle des registres ou des serveurs d'images.
>Le serveur d'images qui est contacté par le daemon docker est le serveur hub.docker.com


**Un conteneur est donc un espace d’exécution, isolé et étanche** par rapport aux autres.
> - Il contient l'**application à exécuter et toutes les dépendances nécessaires** pour l’exécution de cette application.
> - Il est **complet, indépendant et facile à redéployer** sur un autre serveur docker.
> - Un conteneur est  **une instance d'une image**.
> - **Une image est statique et exécutable**. (équivalent binaire)
> - **Un conteneur est dynamique** et représente l’exécution de l'image. (équivalent processus)
> - L’accès aux images peut être ouvert et donc public sans authentification ou il peut être privé: nous devons nous authentifier pour pouvoir accéder à l'image ou récupérer l'image, ou la modifier.


## Fonctionnement de Docker

### Le Docker Host
1. Le **docker host** est le serveur qui exécute le _Docker Engine_ (ou _Docker Daemon_).
2. Le **Client Docker se connecte sur un Docker Host** et va lui soumettre des requêtes: exemple, créer un conteneur à partir d'une image.
3. Exécutons la commande `docker run image1`
4. Le **Docker Daemon vérifie que l'image 1 est stockée sur le disque local**, si ce n'est pas le cas, le Daemon Docker va contacter le Docker Registry et va transférer l'image en local.
5. Une fois l'image en local, le **Daemon Docker va créer le conteneur**.
6. **Le conteneur** qui va s'exécuter sur le Docker Host, **va pointer sur l'image**.

![architecture docker ](https://i2.wp.com/www.docker.com/blog/wp-content/uploads/Blog.-Are-containers-..VM-Image-1.png?fit=1600%2C680&ssl=1)
------------------------

> Le conteneur 1 exécute l'image 1 donc un conteneur est toujours associé à une image et dépend de cette image.
> **On ne peut pas supprimer l'image tant que le conteneur pointe sur cette image**.
> On peut avoir plusieurs instances ou plusieurs conteneurs qui pointent sur la même image:
> - On peut lancer plusieurs serveurs web qui vont servir des données différentes et qui vont exécuter la même application de type serveur web, Apache: Une image Apache, qui sera pointée par plusieurs conteneurs.

## Prérequis d'installation de Docker

### Linux

Il faut installer Docker sur un Linux 64 bits avec une version minimum du noyau à 3.10.

### Windows
On a 2 possibilités :
* installer tout le box Windows dans le cas des Windows inférieurs à Windows 10. Dans ce cas, il faudra aussi installer virtual box qui sera utilisée par Docker pour créer une machine virtuelle avec un Linux qui va supporter Docker.
* Pour les versions qui sont faites basées sur du Windows 10 en fait, on va pouvoir installer Docker directement sur une VM, cette fois-ci basée sur Hyper-V. On n'aura pas besoin d'installer virtual box.

### Mac
* Avant les versions 10.11 de Mac OS, il fallait installer un tool box qui nécessite la présence d'une virtual box qui va créer une même machine virtuelle supportant un Linux avec un Docker.
* Pour toutes les autres versions supérieures à Mac OS 10.11, Docker va automatiquement créer une machine virtuelle basée sur l'hyper-viseur hyperkit natif de Mac. Cette VM va, de la même manière que sur Windows, installer un Linux qui va supporter Docker.
* OU nous pouvons utiliser le gestionnaires de paquets homebrew:
```docker
brew install docker
brew install --cask docker
brew install docker-machine
brew install docker-compose
```

### Installation de Docker

1. Désinstaller les anciennes versions, si elles existent.
2. Installer sur un système vierge.
3. Il y a 2 façons d'installer Docker sur CentOS :
	* soit en utilisant le **dépôt** (**repository**)
	* soit en utilisant **directement les packages**.
4.  Repository ou packages
	4.1. **Repository**
	* Pour la partie repository, il faut installer **yum** .
	* Configurons le fichier de repository, qui fait référence au dépôt sur lequel nous allons nous connecter pour pouvoir installer Docker. `yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo`
	*  lançons la commande Installation de Docker: `yum install -y docker-ce` .

	4.2. **Packages**
	* Aller sur le site où se trouvent les paquets et choisir le bon paquet (avec la bonne version).

5. Une fois que Docker est installé, démarrer le système Docker, qui équivaut à démarrer le moteur Docker.
6. Exécuter un `docker run` d'une image afin de vérifier en exécutant une image à partir d'un registry que tout est ok (docker, l'image et la connexion au registy).


## Installer Docker sur Linux

Une fois les pré-requis, cités précédemment, vérifiés, nous allons installer les outils de Yum.
* `yum-utils` 	Pour la **persistance de données** sur le _device mapper_ qui est associé au volume logique.
* `yum-config-manager` 	Pour **mettre à jour la configuration de Yum**.
* Ajouter le repository pour **installer les paquets de Docker**.
*  `yum install -y` 	Pour **finaliser l'installation de Docker**.
* `systemctl start docker` et  `systemctl enable docker` 	Pour **démarrer Docker** et refaire de même à chaque reboot
* `docker version` 	Pour **vérifier la version de Docker** installée.

> _Remarque_:  il y a une partie client, qui est la commande `docker`, et une partie serveur qui est justement la version du Docker daemon.
> Si vous avez la version client seulement, ça veut dire que le client est installé, mais il ne peut pas se connecter sur le serveur soit parce que le serveur est arrêté, soit parce que le client et mal configuré.

* `docker run hello-world`. Est une autre manière de vérifier que toute l'installation est ok.
* En post-installation, vous pouvez affecter à un user particulier, le groupe de Docker. Sachant que Docker par défaut s'exécute en mode root, nous ne donnerons que l'accès à Docker pour qu'il s'exécute avec l'UID qui sera différent de celui de root: `usermod -aG. <le groupe docker> <pour le user1>`
*  Se déconnecter, et se connecter avec _user1_ pour vérifier qu'il est possible d'utiliser Docker en tant qu'utilisateur simple.
*  Connecté en tant que user1, voyons si le user a le droit d'utiliser Docker.

## Installer Docker sur Windows

Une fois les pré-requis, cités précédemment, vérifiés, allons voir les produits Docker disponibles, télécharger la version Docker Communauté et aller chercher Docker à partir du Docker Store.
* Une fois le téléchargement terminé, lancer l'installateur Docker: double-clic sur l'installateur qui lance l'installation.
* Docker s'installe.
*  À la fin de l'installation, nous avons été déconnecté, après le login, le Docker Daemon est démarré.
* Lancer un PowerShell à partir duquel nous aurons accès à la commande Docker pour créer des conteneurs: `docker run` et exécuter un premier conteneur à partir de l'image "hello_world". L'image n'étant pas en local, elle est téléchargée et à la fin de l'image, on exécute bien l'image hello_world.
*  `docker ps -a`: un conteneur a été créé.

## Installer Docker sur Mac

Une fois les pré-requis, cités précédemment, vérifiés, télécharger donc la version Docker pour Mac.
* double clic pour lancer les actions (monter le volume, déplacer dans Applications...): l’application est en train de démarrer.
* Cliquer sur le symbole Docker: Docker est _running_.
* Ouvrir un terminal:  `docker version`: on a accès à Docker.
* Exécuter  `docker run hello-world`:  L'image n’étant pas locale, elle a été téléchargée et ensuite le conteneur a été exécuté.
* `docker ps -a`, le conteneur s'est créé, s'est exécuté et, après l'affichage, il s'est terminé.

```docker
dali@Mac ~ % docker version
Client: Docker Engine - Community
 Cloud integration: 1.0.4
 Version:           20.10.2
 API version:       1.41
 Go version:        go1.13.15
 Git commit:        2291f61
 Built:             Mon Dec 28 16:12:42 2020
 OS/Arch:           darwin/amd64
 Context:           default
 Experimental:      true

Server: Docker Engine - Community
 Engine:
  Version:          20.10.2
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.13.15
  Git commit:       8891c58
  Built:            Mon Dec 28 16:15:23 2020
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          v1.4.3
  GitCommit:        269548fa27e0089a8b8278fc4fc781d7f65a939b
 runc:
  Version:          1.0.0-rc92
  GitCommit:        ff819c7e9184c13b7c2607fe6c30ae19403a7aff
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0


dali@Mac ~ % docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
0e03bdcc26d7: Pull complete
Digest: sha256:95ddb6c31407e84e91a986b004aee40975cb0bda14b5949f6faac5d2deadb4b9
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.
To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.
To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash
Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/
For more examples and ideas, visit:
 https://docs.docker.com/get-started/

dali@Mac ~ % docker ps -a
CONTAINER ID   IMAGE                    COMMAND                  CREATED          STATUS                      PORTS                   NAMES
dbcf48c0ee94   hello-world              "/hello"                 18 seconds ago   Exited (0) 16 seconds ago                           dazzling_payne
```

## Exploiter les images Docker

#### Etapes de Docker
> Lorsque nous exécutons un container à partir d'une image:
> * Docker vérifie si l'image est stockée en local
	- Si ce n'est pas le cas, Docker télécharge l'image à partir du hub Docker
> * puis l'exécute.

#### Quelques commandes docker utiles

1. Pour **connaitre les images en local**, on lance la commande `docker images`.

```docker
dali@Mac ~ % docker images
REPOSITORY 				TAG 		IMAGE ID 		CREATED 		SIZE
docker/getting-started 	latest  	3ba8f2ff0727 	13 days ago		27.9MB
hello-world  			latest  	d1165f221234 	3 weeks ago 	13.3kB
```

[réferences commande docker images](https://docs.docker.com/engine/reference/commandline/images/)

* L'image de « hello-world » qui a été téléchargée lorsque nous faisons le test `docker run hello-world` pour pouvoir vérifier si Docker était bien installé est présente.
* La colonne **REPOSITORY** identifie le **nom de l'image ou le nom du dépôt**
* La colonne **TAG** étant la **version de l'image**
* La colonne **IMAGE ID** étant l'**identifiant de l'image**
* Ensuite, nous avons la **date de création et la taille de l'image**.


2. Pour **rechercher une image sur le hub** de Docker on va utiliser la commande suivante : `docker search <nomImage>`

```docker
dali@Mac ~ % docker search nginx

NAME 						DESCRIPTION 					STARS 		OFFICIAL 	AUTOMATED
nginx  						Official build of Nginx  		14439					[OK]
jwilder/nginx-proxy  		Automated Nginx… 				1962 					[OK]
richarvey/nginx-php-fpm  	Container running Ng… 			807 		 			[OK]
jc21/nginx-proxy-manager 	Docker container… 				144
linuxserver/nginx  			An Nginx contain… 				141
tiangolo/nginx-rtmp  		Docker image with… 				113  					[OK]
bitnami/nginx  				Bitnami nginx …  				94 						[OK]
jlesage/nginx-proxy  		Docker container…  				93 						[OK]
…
centos/nginx-110-centos7 Platform for running nginx 1.10 or building … 0
```


[réferences commande docker search](https://docs.docker.com/engine/reference/commandline/search/)
	 * Par exemple, l'image « nginx ». « nginx » étant l'image d'une application de type serveur Web ou serveur proxy, qui est une application open source.
	 * La colonne **NAME** nous donne le **nom de l'image**.
		 * Si nous avons un nom sans préfixe, ça veut dire que l'image est associée à un éditeur, que c'est une image officielle, et elle se trouve sur ce qu'on appelle le namespace [root].
		 * Si l'image est précédée par un identifiant, cet identifiant est l'identifiant d'un user ou d'une organisation qui propose l'image.
	 * La colonne **DESCRIPTION** donne la **description de l'image (tronquée)**
		 * pour pouvoir afficher les images avec une description non tronquée on va rajouter l'option `––no-trunc`.
	 * La colonne **STARS** donne la **renommée de l'image**.
	 * La colonne **OFFICIAL** pour voir **si l'image est officielle**:  **[OK]**
	 * La colonne **AUTOMATED** pour savoir **si l'image est générée de manière automatique** grâce à un gestionnaire de code source, par exemple GitHub ou BitBucket.


3. Pour **télécharger une image**, la commande `docker pull <nomImage>`

```docker
dali@Mac ~ % docker pull nginx
Using default tag: latest
latest: Pulling from library/nginx
45b42c59be33: Pull complete
d0d9e9ea897e: Pull complete
66e650438339: Pull complete
76a3dfe4406b: Pull complete
410ff9d97480: Pull complete
Digest: sha256:8e10956422503824ebb599f37c26a90fe70541942687f70bbdb744530fc9eba4
Status: Downloaded newer image for nginx:latest
docker.io/library/nginx:latest
```

[réferences commande docker pull](https://docs.docker.com/engine/reference/commandline/pull/)

 Lançons le téléchargement de l'image (nginx), et regardons si dans les commandes Docker on identifie une image... ...sans spécifier le tag.
 Dans ce cas-là, nous utilisons le **tag par défaut : latest**.
>_Remarque_: on peut spécifier un tag pendant le téléchargement. Exemple le tag 1.13 .
> Il n'y a pas de réel pull.
> En réalité, il y a eu des pull, l'image est composée de 3 couches, alors qu'il n'y a pas eu de pull.
> Pourquoi ? parce que la version 1.13 est la version latest.
> En refaisant `docker image`, nous voyons que _nginx TAG 1.13_ et _nginx TAG latest_ ont le **même _IMAGE ID_**.



4. Pour **supprimer une image**, la commande  `docker rmi <nomImage>`

```docker
dali@MacBookProDali ~ % docker images
REPOSITORY 					TAG 				IMAGE ID 		CREATED  			SIZE
nginx  						latest  			7ce4f91ef623 	14 hours ago 		133MB
docker/getting-started 		latest  			3ba8f2ff0727 	13 days ago  		27.9MB
hello-world  				latest  			d1165f221234 	3 weeks ago  		13.3kB
docker/desktop-kubernetes 	kubernetes-v1.19.7 	93b3398dbfde 	8 weeks ago 		285MB


dali@MacBookProDali ~ % docker rmi nginx
Untagged: nginx:latest
Untagged: nginx@sha256:bae781e7f518e0fb02245140c97e6ddc9f5fcf6aecc043dd9d17e33aec81c832
Deleted: sha256:7ce4f91ef623b9672ec12302c4a710629cd542617c1ebc616a48d06e2a84656a
Deleted: sha256:263c1d3321ceeba1030d165a4c6ce1d2abe1926fb5a58d3290aa1434d4a3b251
Deleted: sha256:c32d51bcf68473072475a2396d0e6a04c8a0500c4e87bcbe1ffc9868d778e4b6
Deleted: sha256:9a43e6d83e127ff7e38b1321df5919e86fb90c1a109b1218c888ba685a3f865f
Deleted: sha256:cdb66505811b778bf0ea0cb7df0b5d4424a92bdb9a3ec281b087d70aad16deee
Deleted: sha256:8f72638764ada0022a461b1194683aacbec0eb0defe9f8cb1c0e46d0a1758671
Deleted: sha256:346fddbbb0ff19bdb026afb053df490c0c73981a8a985882cf8ee3d716735e87

dali@MacBookProDali ~ % docker images
REPOSITORY  				TAG 				IMAGE ID 		CREATED 				SIZE
docker/getting-started  	latest  			3ba8f2ff0727 	13 days ago 			27.9MB
hello-world 				latest  			d1165f221234 	3 weeks ago 			13.3kB
docker/desktop-kubernetes 	kubernetes-v1.19.7 	93b3398dbfde 	8 weeks ago 			285MB
```

[références commande docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/)

>Comme **rm image** : remove image + le nom de l'image.
> Nous **supprimons la latest**.
> En réalité, elle n'est pas réellement supprimée elle est juste ce qu'on appelle "untaggé", nous avons supprimé le tag.
> Pourquoi ? Parce qu'il y avait 2 tags qui faisaient référence à la même image.
> Maintenant, si nous supprimons la version 1.13 l'image qui commençait par  « 3F » est maintenant réellement supprimée.
> Il reste l'image _hello-world_, que nous allons essayer de supprimer.
> Qu'est-ce qui se passe ? Il y a un conflit. Pourquoi ? Parce que c'est l'image que nous avons utilisé pour créer un container: pour tester l'installation.
> Dans ce cas-là, le container n'existe plus, ne fonctionne plus, mais il y a une trace au niveau du disque.

> `docker ps` nous donne **la liste des conteneurs actifs**
> Il n'y a aucun conteneur en exécution, mais avec l'option `-a`: `docker ps -a`, **nous voyons tous les conteneurs qui ont existés, ont été utilisés**.

```docker
dali@MacBookProDali ~ % docker ps
CONTAINER ID IMAGE COMMAND CREATED STATUS  PORTS NAMES

dali@MacBookProDali ~ % docker ps -a
CONTAINER 		ID IMAGE  				COMMAND  				CREATED  		STATUS 							PORTS  				NAMES
ad3f9c9481d3 	hello-world  			"/hello" 				25 minutes ago 	Exited (0) 16 minutes ago 							sad_rhodes
243160e0a79b 	docker/getting-started 	"/docker-entrypoint.…" 	27 minutes ago 	Exited (255) 9 minutes ago 		0.0.0.0:80->80/tcp 	serene_greider

dali@MacBookProDali ~ % docker rmi hello-world
Error response from daemon: conflict: unable to remove repository reference "hello-world" (must force) - container ad3f9c9481d3 is using its referenced image d1165f221234
```

> Le statut est **Existed**, donc **terminé**.
> Par contre, il reste une trace au niveau du disque. Pour supprimer une image qui est utilisée ou référencée par un conteneur, même s'il est terminé, **il faut forcer la suppression de l'image**: `docker rmi -f <nomImage>`
> **Attention**: on ne force pas la suppression d'une image si elle est utilisée par un conteneur qui doit redémarrer.
```docker
dali@MacBookProDali ~ % docker images
REPOSITORY 					TAG 		IMAGE ID 			CREATED 			SIZE
docker/getting-started 		latest  	3ba8f2ff0727 		13 days ago 		27.9MB
hello-world  				latest  	d1165f221234 		3 weeks ago 		13.3kB


dali@MacBookProDali ~ % docker rmi -f hello-world
Untagged: hello-world:latest
Untagged: hello-world@sha256:308866a43596e83578c7dfa15e27a73011bdd402185a84c5cd7f32a88b501a24
Deleted: sha256:d1165f2212346b2bab48cb01c1e39ee8ad1be46b87873d9ca7a4e434980a7726

dali@MacBookProDali ~ % docker images
REPOSITORY 					TAG 		IMAGE ID 			CREATED 			SIZE
docker/getting-started 		latest  	3ba8f2ff0727 		13 days ago 		27.9MB
```


5.  Pour **obtenir l'aide docker**, `docker --help | more` affiche l'aide docker page par page

```docker
dali@Mac ~ % docker --help | more

Usage:  docker [OPTIONS] COMMAND
A self-sufficient runtime for containers
Options:
      --config string      Location of client config files (default "/Users/mdali/.docker")
  -c, --context string     Name of the context to use to connect to the daemon (overrides DOCKER_HOST env var and default context set with "docker context use")
  -D, --debug              Enable debug mode
  -H, --host list          Daemon socket(s) to connect to
  -l, --log-level string   Set the logging level ("debug"|"info"|"warn"|"error"|"fatal") (default "info")
      --tls                Use TLS; implied by --tlsverify
      --tlscacert string   Trust certs signed only by this CA (default "/Users/mdali/.docker/ca.pem")
      --tlscert string     Path to TLS certificate file (default "/Users/mdali/.docker/cert.pem")
      --tlskey string      Path to TLS key file (default "/Users/mdali/.docker/key.pem")
      --tlsverify          Use TLS and verify the remote
  -v, --version            Print version information and quit

Management Commands:
  app*        Docker App (Docker Inc., v0.9.1-beta3)
  builder     Manage builds
  buildx*     Build with BuildKit (Docker Inc., v0.5.1-docker)
  config      Manage Docker configs
```

## Utiliser le hub

On va rechercher des images au niveau du hub en passant par un navigateur.
L'URL du hub étant hub.docker.com, il y a une partie recherche à utiliser si nous connaissons le nom de l'image souhaitée:
*  un ensemble de repositories: le dépôt officiel et les dépôts publics, non officiels.
*  les **STARS**
* le nombre de fois que l'image a été descendue ou téléchargée
*  le détail de l'image.
	* Suivant l’éditeur, vous aurez un ensemble d'onglets, les informations du repository, une description courte et à droite regardez vous avez la commande `pull` qui permet de récupérer ou de télécharger l'image
	*  une description complète avec les différentes versions

## Commande Docker

Comme vu précédemment, pour obtenir l'aide docker utiliser la commande `docker --help | more`.

 `docker <Espace> <Commande>` est la **syntaxe à utiliser**.

Docker étant le client docker qui va se connecter au daemon docker. A travers la commande `docker`, nous allons **exécuter des commandes ou des actions dans un contexte particulier** ou sur des objets de certains types.
Les contextes sont **container**, **network** ou **volume**.
> Le contexte **container** est le **contexte par défaut**.

 Spécifions le type d'objet sur lequel travailler et ensuite lui associer des commandes: la commande `attach`, `run`, `create` ...
> Ces commandes sont associées à certains types de management commands.

> **La documentation des commandes docker** se situent sur ces pages
[références commandes docker](https://docs.docker.com/engine/reference/commandline/docker/)


#### Options spécifiques de commande

Nous allons voir quelles sont les options spécifiques d'une commande spécifique par rapport à un certain contexte: 		`docker container run`
 * **docker** qui est la commande client,
 * **container** qui est le contexte
 *  **run** qui est une commande qui permet d’exécuter un conteneur.

**- -help** (`docker --help`) va avoir une sortie standard qui est très longue.
Utilisons **more** et **des options spécifiques de la commande run du contexte container**.

> **Les options sont toujours des mots clés ou des noms complets précédés par 2 tirets et parfois il y a un raccourci sous forme d'1 seul tiret et d'un caractère ou d'une lettre**.

## Cycle de vie d'un conteneur

Il existe **2 cycles de vie de conteneur** différents :
* le **cycle de vie de base**: ce cycle de vie est **utilisé pour les compilations ou un traitement spécifique** (ex: base de données, ...)
	* Le principe est de lancer le conteneur à partir d'une image, lorsque le conteneur est créé, il doit exécuter un processus spécifique.
	* Une fois ce processus terminé, le conteneur se termine aussi, puisque le conteneur a été créé pour l'exécution spécifique de ce processus (ou tâche).
	* Le conteneur persiste sur le système ou sur le disque avec l'état terminé, il n'est plus en mémoire. Comme la tâche a été réalisée, le conteneur n'a plus lieu d'être, c'est à vous de le détruire.
	* Par la commande docker `rm`, vous pouvez aussi, lors du lancement, ajouter l'option `--rm` pour le détruire automatiquement lorsqu'il se termine.
	**`docker run -it --rm <nomImage>:<tag>`**, _tag_ est en général _latest_

```docker
dali@MacBookProDali ~ % docker ps -a
CONTAINER ID 				IMAGE  				COMMAND  			CREATED  				STATUS  								PORTS  					NAMES
ad3f9c9481d3 				d1165f221234 		"/hello" 			2 days ago 				Exited (0) 2 days ago  											sad_rhodes
243160e0a79b 			docker/getting-started 	"/docker-ent…" 		2 days ago 				Exited (255) 2 days ago 				0.0.0.0:80->80/tcp 		serene_greider

dali@MacBookProDali ~ % docker run -it --rm alpine
/ # hostname
70dc4a7bb0b4
/ # exit

dali@MacBookProDali ~ % docker ps -a
CONTAINER ID 				IMAGE  				COMMAND  			CREATED  				STATUS  								PORTS  					NAMES
ad3f9c9481d3 				d1165f221234 		"/hello" 			2 days ago 				Exited (0) 2 days ago  											sad_rhodes
243160e0a79b 			docker/getting-started 	"/docker-ent…" 		2 days ago 				Exited (255) 2 days ago 				0.0.0.0:80->80/tcp 		serene_greiderr

dali@MacBookProDali ~ % docker run -it alpine
/ # hostname
5be6e07e4cfd

dali@MacBookProDali ~ % docker ps -a
CONTAINER ID 				IMAGE  				COMMAND  			CREATED  				STATUS  								PORTS  					NAMES
5be6e07e4cfd 				alpine 				"/bin/sh"  			10 seconds ago 			Exited (0) 2 seconds ago  										zen_banach
ad3f9c9481d3 				d1165f221234 		"/hello" 			2 days ago 				Exited (0) 2 days ago  											sad_rhodes
243160e0a79b 			docker/getting-started 	"/docker-ent…" 		2 days ago 				Exited (255) 2 days ago 				0.0.0.0:80->80/tcp 		serene_greider
```

* Le **cycle de vie avancé** : ce cycle de vie correspond au **fonctionnement d'une application sous forme de service** (ex: un serveur Web, un serveur base de données, ...)
	* Lançons le conteneur à partir d'une image. Lorsque le conteneur est créé, il doit exécuter un daemon ou un service: exemple, dans le cas d'un serveur Web Apache, ce sera le daemon HTTPD.
	* Les clients peuvent avoir accès au service
		* les clients de type navigateurs peuvent accéder au service Web. Pour des raisons de maintenance, applicative ou système ou même une maintenance matériel, hardware, nous pouvons arrêter l'application et donc arrêter le conteneur.
	* Une fois que la maintenance est terminée, nous pouvons relancer le conteneur, et donc relancer aussi l'application.
	* C'est le mode de service applicative, c'est le mode de fonctionnement le plus répandu.

## Exécuter un conteneur

[références commande docker run](https://docs.docker.com/engine/reference/commandline/run/)

La commande **`docker run`** crée un conteneur à partir d'une image, et exécute une commande par défaut qui est configurée dans l'image elle-même.

##### Comment utiliser des images de type système comme Ubuntu.

Attention lorsque nous parlons de **système**, c'est une **image qui contient uniquement la partie binaire et la partie library** stockées dans une arborescence au format Ubuntu. **Elle ne contient en aucun cas un noyau.**
Lorsque nous lançons un conteneur basé sur une image de type Ubuntu, nous créons un environnement dans l'espace user, qui reproduit un environnement Ubuntu.
Le noyau reste toujours celui sur lequel nous avons installé Docker, ici CentOS.

1.  `docker run ubuntu`
	* comme l'image n'a pas été encore téléchargée, au moment du lancement elle n'est pas en local, elle va être téléchargée à partir du Docker Hub.
	* Que s'est-il passé ? Il y a eu téléchargement de l'image, et il y a eu exécution, mais comme la commande de base est une image de type OS, généralement, c'est un bash, ou "interpréteur de commandes", le bash nécessite un terminal.
	*  Comme le bash qui a été utilisé ici, ne trouvant pas de terminal, s'est arrêté.
	* Alors, qu'est-ce qu'on va faire ?

```docker
dali@MacBookProDali ~ % docker run ubuntu
Unable to find image 'ubuntu:latest' locally
latest: Pulling from library/ubuntu
a70d879fa598: Pull complete
c4394a92d1f8: Pull complete
10e6159c56c0: Pull complete
Digest: sha256:3c9c713e0979e9bd6061ed52ac1e9e1f246c9495aa063619d9d695fb8039aa1f
Status: Downloaded newer image for ubuntu:latest
```


2.  `docker ps`, pour connaitre les conteneurs qui s'exécutent.
	* Rien ne s'affiche car aucun conteneurs n'est en cours d'exécution
	* Par contre, `docker ps -a`: nous voyons tous les conteneurs, même ceux qui sont arrêtés.
	* Qu'y a-t-il ? Une image ubuntu qui s'exécutait il y a 2 minutes, et qui s'est terminée.
	* La commande `ps` donne en
		* 1ere colonne, l'**ID du conteneur**
		* 2e colonne, l'**image** sur laquelle se base le conteneur
		* La **commande qui a été exécutée à l'intérieur du conteneur**. La commande par défaut étant le bash.
		* La **date de création**
		* Le **statut**, entre parenthèses, la valeur de l'Exit.
		* Pour certaines images qui fonctionnent sur le réseau, le **port** apparait
		* Le **nom du conteneur**. Les conteneurs vont avoir des noms aléatoires. Un conteneur qui exécute une application de type service doit avoir un nom spécifique et aussi, un hostname spécifique: il faudra donc les nommer spécifiquement.

```docker
dali@MacBookProDali ~ % docker ps
CONTAINER ID IMAGE COMMAND CREATED STATUS  PORTS NAMES

dali@MacBookProDali ~ % docker ps -a
CONTAINER ID 				IMAGE  				COMMAND  			CREATED  				STATUS  								PORTS  					NAMES
54e4aac3a363 				ubuntu 				"/bin/bash"  		2 minutes ago  			Exited (0) 2 minutes ago 										keen_maxwell
5be6e07e4cfd 				alpine 				"/bin/sh"  			10 seconds ago 			Exited (0) 2 seconds ago  										zen_banach
ad3f9c9481d3 				d1165f221234 		"/hello" 			2 days ago 				Exited (0) 2 days ago  											sad_rhodes
243160e0a79b 			docker/getting-started 	"/docker-ent…" 		2 days ago 				Exited (255) 2 days ago 				0.0.0.0:80->80/tcp 		serene_greider
```

3. `docker run` avec **les options nécessaires pour pouvoir avoir le mode interactif en mode bash**.
Le mode interactif avec processus bash qui s'exécute au niveau du conteneur.
* l'option `-t` qui permet de **créer un terminal**
* l'option `-i` qui permet de **se connecter à ce terminal en mode interactif**
* Exécuter la commande bash avec ubuntu.

```docker
dali@MacBookProDali ~ % docker run -it ubuntu
root@c1c43fcdb5ed:/# hostname
c1c43fcdb5ed
root@c1c43fcdb5ed:/# exit
exit

dali@MacBookProDali ~ % docker ps -a
CONTAINER ID 				IMAGE  				COMMAND  			CREATED  				STATUS  								PORTS  					NAMES
c1c43fcdb5ed 				ubuntu 				"/bin/bash"  		23 seconds ago 			Exited (0) 8 seconds ago 										funny_mcnulty
54e4aac3a363 				ubuntu 				"/bin/bash"  		2 minutes ago  			Exited (0) 2 minutes ago 										keen_maxwell
5be6e07e4cfd 				alpine 				"/bin/sh"  			10 seconds ago 			Exited (0) 2 seconds ago  										zen_banach
ad3f9c9481d3 				d1165f221234 		"/hello" 			2 days ago 				Exited (0) 2 days ago  											sad_rhodes
243160e0a79b 			docker/getting-started 	"/docker-ent…" 		2 days ago 				Exited (255) 2 days ago 				0.0.0.0:80->80/tcp 		serene_greider
```


> **Nous avons un prompt au niveau du système, avec le compte root, et comme hostname l'identifiant du conteneur**.

4. Vérifions certaines choses.
* la commande `ps` pour voir qu'un bash s'exécute dans le conteneur par défaut.
* la commande `hostname` pour voir que le nom du host, le nom du hôte de la machine, est en réalité l'ID du conteneur.

> A l'intérieur du conteneur, nous avons une vision du conteneur comme étant un environnement système qui accède à l'operating system, le système d'exploitation, et qui nous donne accès au hardware: l'impression d'être dans un vrai serveur avec la notion de hostname.

* Une fois terminé, faire un `exit`: **nous revenons sur le système**.

5.  `docker ps -a`: nous voyons le conteneur qui vient d'être utiliser. Nous l'identifions car son ID est le hostname lorsque nous étions à l'intérieur du conteneur.

6. Autre manière d'exécuter la même commande, `docker run ubuntu`, mais, en passant comme argument une commande.
`docker -it run ubuntu bash`: même comportement +  un shell.
Par contre, nous pouvons lancer une autre commande, par exemple `ps`. La commande ps s'exécute, et elle se termine.
Elle fait un exit, et dans ce cas-là, le conteneur est arrêté.
Le conteneur n'est pas supprimé.
**Si on passe une commande, on spécifie une autre commande à exécuter, et non pas la commande par défaut qui est configurée dans l'image**.

## Exécuter en mode attaché et détaché

Voici quelques options d'exécution des conteneurs.

Lorsqu'on lance `docker run`, on a quelques options supplémentaires.
*  `-- name` permet de positionner le **nom du conteneur**: _cont01_
*  `-- hostname` permet de positionner le **nom du système**: _host01_

> _Exemple_: `docker run --name=cont01 --hostname=host01 -it ubuntu `
> On voit qu'au niveau du prompt, on a le nom du host, qu'on peut vérifier avec la commande hostname.
>  On arrête le conteneur avec commande exit.
>  On vérifie par `docker ps -a` que le conteneur qu'on vient de lancer s'appelle bien cont01 et qu'il y a été arrêté il y a quelques secondes.

```docker
dali@MacBookProDali ~ % docker run --name=cont01 --hostname=host01 -it ubuntu
root@host01:/# hostname
host01
root@host01:/# exit
exit

dali@MacBookProDali ~ % docker ps -a
CONTAINER ID 		IMAGE  		COMMAND  			CREATED  			STATUS 						PORTS  			NAMES
f04576a1e72b 		ubuntu 		"/bin/bash"  		52 seconds ago 		Exited (127) 5 seconds ago  				cont01
```


#### Mode d'exécution en avant-plan (mode attaché)

Par défaut, **Docker lance un conteneur en mode attaché**, sauf si on utilise l'option `-d` ou bien `--detach`.

#### Mode d'exécution en arrière-plan (mode détaché)

 > Si on lance donc un conteneur en mode detach, pour passer du mode attaché au mode détaché, on utilise une **combinaison de touches** `Ctrl+P+Q` .


* Lançons Docker, avec l'option `-d` ou `--detach` (mode détaché), `-it` pour créer le terminal en mode interactif. Même en mode détaché, ces options sont nécessaires pour pouvoir s'attacher au conteneur.
* Lançons une nouvelle image CentOS en utilisant la commande `ping 127.0.0.1` (permet au host de se pinger lui-même sur son adresse IP locale).
La commande `docker run -d` a passé le conteneur en mode détaché.
Pour vérifier `docker -ps`.
CentOS s'est lancé avec la commande `ping`.
La commande ping est en train de s'exécuter en mode arrière-plan.

```docker
dali@MacBookProDali ~ % docker run -it --name=cont02 --hostname=host02 centos ping 127.0.0.1
Unable to find image 'centos:latest' locally
latest: Pulling from library/centos
7a0437f04f83: Pull complete
Digest: sha256:5528e8b1b1719d34604c87e11dcd1c0a20bedf46e83b5632cdeac91b8c04efc1
Status: Downloaded newer image for centos:latest

PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.031 ms
64 bytes from 127.0.0.1: icmp_seq=2 ttl=64 time=0.079 ms
64 bytes from 127.0.0.1: icmp_seq=3 ttl=64 time=0.074 ms

dali@MacBookProDali ~ % docker ps -a
CONTAINER ID 		IMAGE  		COMMAND  			CREATED  			STATUS 						PORTS  			NAMES
e26d2a83a200 		centos 		"ping 127.0.0.1" 	About a minute ago 	Up 1 minute   								cont02
f04576a1e72b 		ubuntu 		"/bin/bash"  		52 seconds ago 		Exited (127) 5 seconds ago  				cont01
```

####  Visualiser les sorties standard: `docker logs`

[références commandes docker logs](https://docs.docker.com/engine/reference/commandline/logs/)

**Les sorties standards d'un conteneur qui fonctionne en mode détaché s'appelle avec** `docker logs <containerID>`.

> **Attention, `logs` n'a rien à voir avec les journaux ou les logs des systèmes**,

```docker
dali@MacBookProDali ~ % docker logs e26d2a83a200
PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.031 ms
64 bytes from 127.0.0.1: icmp_seq=2 ttl=64 time=0.079 ms
64 bytes from 127.0.0.1: icmp_seq=3 ttl=64 time=0.074 ms
^C
--- 127.0.0.1 ping statistics ---

3 packets transmitted, 6 received, 0% packet loss, time 109ms
rtt min/avg/max/mdev = 0.031/0.064/0.079/0.018 ms
```


[références commandes docker attach](https://docs.docker.com/engine/reference/commandline/attach/)


Pour passer en mode avant-plan ou en mode attaché, nous utilisons la commande `docker attach <containerID>`
En mode attaché et nous voyons la sortie standard de la commande ping.

```docker
dali@MacBookProDali ~ % docker attach e26d2a83a200
64 bytes from 127.0.0.1: icmp_seq=1004 ttl=64 time=0.031 ms
64 bytes from 127.0.0.1: icmp_seq=1005 ttl=64 time=0.079 ms
64 bytes from 127.0.0.1: icmp_seq=1006 ttl=64 time=0.074 ms
```


Pour pouvoir passer du mode attaché au mode détaché, on utilise la combinaison `Ctrl+P+Q`.
On appelle, une **read escape sequence**. C'est la manière "propre" de se détacher d'un container en le laissant fonctionner en arrière plan.

```docker
dali@MacBookProDali ~ % docker attach e26d2a83a200
64 bytes from 127.0.0.1: icmp_seq=1034 ttl=64 time=0.031 ms
read escape sequence
```


Faisons un `docker ps`, le conteneur continue de s'exécuter, mais cette fois-ci, en mode détaché.
Rattachons-nous à nouveau sur le conteneur, assorti de la commande ping.
Utilisons la séquence `Ctrl+C`: un signal d'interruption au processus courant est envoyé.
Dans notre cas, c'est la commande ping.
Donc, le processus ping s'arrête et le conteneur s'arrête aussi.
Faisons un `docker ps`, le conteneur n'est plus en fonctionnement
Faisons un `docker ps -a`, nous observons que le conteneur s'est arrêté.

```docker
dali@MacBookProDali ~ % docker attach e26d2a83a200
64 bytes from 127.0.0.1: icmp_seq=1004 ttl=64 time=0.031 ms
64 bytes from 127.0.0.1: icmp_seq=1005 ttl=64 time=0.079 ms
64 bytes from 127.0.0.1: icmp_seq=1006 ttl=64 time=0.074 ms
^C
--- 127.0.0.1 ping statistics ---
3 packets transmitted, 6 received, 0% packet loss, time 109ms
rtt min/avg/max/mdev = 0.031/0.064/0.079/0.018 ms

dali@MacBookProDali ~ % docker ps
CONTAINER ID 		IMAGE  		COMMAND  			CREATED  			STATUS 						PORTS  			NAMES
e26d2a83a200 		centos 		"ping 127.0.0.1" 	About a minute ago 	Up 10 minutes   							cont02

dali@MacBookProDali ~ % docker attach e26d2a83a200
64 bytes from 127.0.0.1: icmp_seq=1034 ttl=64 time=0.031 ms
read escape sequence

dali@MacBookProDali ~ % docker ps
CONTAINER ID  IMAGE  COMMAND CREATED STATUS	PORTS  NAMES

dali@MacBookProDali ~ % docker ps -a
CONTAINER ID 		IMAGE  		COMMAND  			CREATED  			STATUS 						PORTS  			NAMES
e26d2a83a200 		centos 		"ping 127.0.0.1" 	About a minute ago 	Exited (127) 1 second ago   				cont02
```


## Connaître les autres modes d'exécution

> Nous allons voir une autre forme d'exécution de processus dans un conteneur, ainsi que les méthodes d'arrêt et de démarrage.

####  Commandes `stop` et `start`

[références commandes docker start](https://docs.docker.com/engine/reference/commandline/start/)
[références commandes docker stop](https://docs.docker.com/engine/reference/commandline/stop/)

> Pour illustrer ces commandes, nous lançons un conteneur de type serveur Web.

1.  `docker run -d -P nginx`
	*	`-d` pour être en mode _detaché_
	*	`-P` permet de mapper les ports réseau du conteneur sur ceux du hôte qui exécute le conteneur.

> Cela permet aux clients externes de pouvoir passer par l'hôte pour accéder au service du conteneur.
C'est du **mapping de port**.

* Pour vérifier que le serveur Web fonctionne, soit nous passons par un navigateur, soit nous utilisons un client HTTP, comme la commande `curl`.
* Vérifions qu'il est accessible à travers le réseau pour les clients externes.
* Il y a ce qu'on appelle un mapping de port qui est effectué par Docker sur le hôte.
* Nous avons un port aléatoire, le 55000, qui est mappé sur le port 80 du conteneur.
* Nous voyons que la commande `curl` fonctionne, donc, le serveur est disponible.

```docker
dali@MacBookProDali ~ % docker run -d -P nginx
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
75646c2fb410: Pull complete
6128033c842f: Pull complete
71a81b5270eb: Pull complete
b5fc821c48a1: Pull complete
da3f514a6428: Pull complete
3be359fed358: Pull complete
Digest: sha256:bae781e7f518e0fb02245140c97e6ddc9f5fcf6aecc043dd9d17e33aec81c832
Status: Downloaded newer image for nginx:latest
99c28791e53aa8b1ace06ee600201b60cddd494068335de4d878952b309c9f7e

dali@MacBookProDali ~ % docker ps
CONTAINER ID 		IMAGE 		COMMAND  						CREATED  			STATUS  			PORTS 						NAMES
99c28791e53a 		nginx 		"/docker-entrypoint.…" 			55 seconds ago 		Up 53 seconds 		0.0.0.0:55000->80/tcp 		charming_knuth

dali@MacBookProDali ~ % curl http://0.0.0.0:55000
<!DOCTYPE html>
  <html>
    <head>
      <title>Welcome to nginx!</title>
      <style>
        body {
          width: 35em;
          margin: 0 auto;
          font-family: Tahoma, Verdana, Arial, sans-serif;
        }
      </style>
    </head>
    <body>
      <h1>Welcome to nginx!</h1>
        <p>If you see this page, the nginx web server is successfully installed and working. Further configuration is required.</p>

        <p>For online documentation and support please refer to <a href="http://nginx.org/">nginx.org</a>.<br/>
        Commercial support is available at <a href="http://nginx.com/">nginx.com</a>.</p>
        <p><em>Thank you for using nginx.</em></p>
    </body>
  </html>
```

Pour arrêter un conteneur, on va utiliser la commande `docker stop <IDContainer>` (ou son nom)
* Si on exécute la commande `curl` le conteneur n'est plus disponible.
* Si on fait un `docker ps -a`, on voit qu'il a été arrêté il y a quelques secondes.

```docker
dali@MacBookProDali ~ % docker ps
CONTAINER ID 		IMAGE 		COMMAND  						CREATED  			STATUS  			PORTS 						NAMES
99c28791e53a 		nginx 		"/docker-entrypoint.…" 			55 seconds ago 		Up 53 seconds 		0.0.0.0:55000->80/tcp 		charming_knuth

dali@MacBookProDali ~ % docker stop 99c28791e53a
99c28791e53a

dali@MacBookProDali ~ % docker ps
CONTAINER ID IMAGE COMMAND CREATED STATUS  PORTS NAMES

dali@MacBookProDali ~ % docker ps -a
CONTAINER ID 			IMAGE  		COMMAND  			CREATED 			STATUS 							PORTS  		NAMES
99c28791e53a 			nginx  		"/docker-…" 		11 minutes ago  	Exited (0) 7 seconds ago  					charming_knuth
```

>La commande `stop`, envoie un signal de type **term** ce qu'on appelle un signal de terminaison ou PID1, qui est le premier processus de la hiérarchie des processus à l'intérieur du conteneur.
>Donc, c'est un **arrêt propre**.


On va le redémarrer par la commande `docker start <IDContainer>` (ou son nom)
* On vérifie par un `docker ps`.
* Le **port qui est mappé a été modifié**: c'est du **mapping dynamique**
* En **mapping statique**, le **port n'est pas modifié**.
* On refait un `curl`, sur le port 55001, le serveur Web est à nouveau disponible.

```docker
dali@MacBookProDali ~ % docker start 99c28791e53a
99c28791e53a

dali@MacBookProDali ~ % docker ps
CONTAINER ID 		IMAGE 		COMMAND  						CREATED  			STATUS  			PORTS 						NAMES
99c28791e53a 		nginx 		"/docker-entrypoint.…" 			55 seconds ago 		Up 53 seconds 		0.0.0.0:55001->80/tcp 		charming_knuth
```


> Une autre commande est disponible, la commande `docker kill <IDContainer>`.
Attention, la commande `kill` par défaut envoie le signal **term**, qui est le signal de terminaison normale.
Si vous faites un `kill -9` vous envoyez le signal **KILL**.
C'est un **arrêt qui n'est pas propre**, et qui permet en fait d'arrêter une application si elle ne fonctionne pas ou si elle est buggée et elle ne répond pas.

```docker
dali@MacBookProDali ~ % docker kill 99c28791e53a
99c28791e53a

dali@MacBookProDali ~ % docker ps
CONTAINER ID IMAGE COMMAND CREATED STATUS  PORTS NAMES

dali@MacBookProDali ~ % docker ps -a
CONTAINER ID 			IMAGE  		COMMAND  			CREATED 			STATUS 							PORTS  		NAMES
99c28791e53a 			nginx  		"/docker-…" 		19 minutes ago  	Exited (137) 10 seconds ago  					charming_knuth
```

* On fait un `ps -a` , on voit que le processus s'est arrêté, avec un code qui est différent de zéro. (colonne status: exited 137)
* Ce n'est pas un arrêt normal.

#### Commande `docker exec`

[références commandes docker exec](https://docs.docker.com/engine/reference/commandline/exec/)

Pour pouvoir **voir les différentes hiérarchies au sein d'un conteneur**.  Elle permet d'**exécuter un autre processus dans un conteneur en cours d'exécution**.

1. Créons un nouveau container avec un nouveau nom `docker run -it --name=cont03 --hostname=host03 centos`

2. Lançons la commande "docker run" en mode "detach", l'option "-it" pour pouvoir se connecter sur le terminal sur une image centos avec la commande ping pour simuler une activité . `docker run -d -ti --name=cont04 --hostname=host04 centos ping 127.0.0.1`

3. Lançons un autre processus en parallèle dans ce même conteneur avec la commande bash. `docker exec -it cont04 bash`
4. Nous sommes dans le bon conteneur. Si nous utilisons la commande `ps -ef` le PID1 qui est associé à votre application.

5. C'est la **hiérarchie principale**.

```docker
dali@MacBookProDali ~ % docker run -d -ti --name=cont04 --hostname=host04 centos ping 127.0.0.1
8b348b47461cf301f5ce3d74372473843f4294f38502df66bb89787435c09ad4

dali@MacBookProDali ~ % docker exec -it cont04 bash
[root@host04 /]# ps -ef
UID  	PID  	PPID  	C 	STIME 	TTY  		TIME 			CMD
root 	1 		0  		0 	12:57 	pts/0  		00:00:00 		ping 127.0.0.1
root 	7 		0  		0 	12:58 	pts/1  		00:00:00 		bash
root  	21 		7  		0 	12:58 	pts/1  		00:00:00 		ps -ef

[root@host04 /]#
```


> Par contre, lorsqu'on s'est connecté avec exec et bash sur le conteneur, on va avoir une nouvelle hiérarchie à partir de « 7 ». (colonne PID de la commande `bash`)
Ça veut dire que tous les processus-fils à partir du bash seront en fait associés à « 7 ».

> Or, la véritable hiérarchie, c'est « 1 ».
Donc, si on fait un docker stop d'un conteneur, on va envoyer un signal term « propre » de terminaison au PID1, et à tous ces fils.
Cette hiérarchie va être proprement arrêtée.

>À l'inverse, cette hiérarchie-là ne sera pas arrêtée.

**La commande docker exec ne doit pas être utilisée pour lancer votre application principale, mais des processus annexes**, par exemple, pour modifier une configuration ou des outils de surveillance.
**Votre application principale doit être toujours lancée au moment du `docker run`**.


## Inspecter un conteneur

#### Visualiser les propriétés des conteneurs

[références commandes docker inspect](https://docs.docker.com/engine/reference/commandline/inspect/)


> La commande **inspect** permet d'inspecter ou de visualiser les propriétés d'un objet, de volumes ou de réseaux.

1. Lançons un conteneur de type Web, docker run -d comme detach, -P pour mapper les ports, puisqu'on est sur un conteneur de type réseau, serveur réseau ou serveur Web, --name permet de donner le nom du conteneur et --hostname permet de donner le nom du host et on va lancer sur la base de l'image nginx.  `docker run -d -P --name=web05 --hostname=cont05 nginx`

2. `docker ps` nous montre que le conteneur est en cours d'exécution.
Ici, le contexte est donc par défaut, conteneur.
3.  `docker inspect` et le nom du conteneur avec l'option "| more" pour avoir une sortie paginée. `docker inspect web05 | more`

4. Le résultat est sous forme d'un tableau Json, tableau avec attribut/valeur.

```docker
dali@MacBookProDali ~ % docker run -d -P --name=web05 --hostname=cont05 nginx
1a8f7ba4cf91b19196c3edc783f40f71c9e2da5a17c49f6dba7f7f64295ff3dd

dali@MacBookProDali ~ % docker ps
CONTAINER ID 		IMAGE 		COMMAND  			CREATED  		STATUS  		PORTS 						NAMES
1a8f7ba4cf91 		nginx 		"/docker-entr…" 	23 seconds ago 	Up 22 seconds 	0.0.0.0:55002->80/tcp 		web05

dali@MacBookProDali ~ % docker inspect web05 | more
[
  {
   "Id": "1a8f7ba4cf91b19196c3edc783f40f71c9e2da5a17c49f6dba7f7f64295ff3dd",
   "Created": "2021-04-11T13:06:15.7720253Z",
   "Path": "/docker-entrypoint.sh",
   "Args": [
    "nginx",
    "-g",
    "daemon off;"
   ],
   "State": {
    "Status": "running",
    "Running": true,
    "Paused": false,
    "Restarting": false,
    "OOMKilled": false,
    "Dead": false,
    "Pid": 55259,
    "ExitCode": 0,
    "Error": "",
    "StartedAt": "2021-04-11T13:06:16.2374932Z",
    "FinishedAt": "0001-01-01T00:00:00Z"
   },
   "Image": "sha256:7ce4f91ef623b9672ec12302c4a710629cd542617c1ebc616a48d06e2a84656a",
   "ResolvConfPath": "/var/lib/docker/containers/1a8f7ba4cf91b19196c3edc783f40f71c9e2da5a17c49f6dba7f7f64295ff3dd/resolv.conf",
   "HostnamePath": "/var/lib/docker/containers/1a8f7ba4cf91b19196c3edc783f40f71c9e2da5a17c49f6dba7f7f64295ff3dd/hostname",
   "HostsPath": "/var/lib/docker/containers/1a8f7ba4cf91b19196c3edc783f40f71c9e2da5a17c49f6dba7f7f64295ff3dd/hosts",
   "LogPath": "/var/lib/docker/containers/1a8f7ba4cf91b19196c3edc783f40f71c9e2da5a17c49f6dba7f7f64295ff3dd/1a8f7ba4cf91b19196c3edc783f40f71c9e2da5a17c49f6dba7f7f64295ff3dd-json.log",
   "Name": "/web05",
   "RestartCount": 0,
   "Driver": "overlay2",
   "Platform": "linux",
   "MountLabel": "",
   "ProcessLabel": "",
   "AppArmorProfile": "",
   "ExecIDs": null,
   "HostConfig": {
   "Binds": null,
   "ContainerIDFile": "",
   "LogConfig": {
    "Type": "json-file",
    "Config": {}
   },
   "NetworkMode": "default",
    "PortBindings": {},
    "RestartPolicy": {
     "Name": "no",
     "MaximumRetryCount": 0
    },
   "AutoRemove": false,
   "VolumeDriver": "",
   "VolumesFrom": null,
   "CapAdd": null,
   "CapDrop": null,
   "CgroupnsMode": "host",
   "Dns": [],
   "DnsOptions": [],
   "DnsSearch": [],
   "ExtraHosts": null,
   "GroupAdd": null,
   ...
```

5. Quelques propriétés de ce container:
* Au début, nous avons la première ligne qui est l'**ID du conteneur** (ID longue), la date de création et on a la commande qui est exécutée par le conteneur.
* **State** : un tableau, avec attribut/valeur, le premier attribut étant Status et la valeur est running
* **Name** le nom du conteneur (web05)
* **Config**: le Hostname (host05)
*  **NetworkSettings**: la partie réseau avec les ports utilisés et l'adresse IP du container

```docker
    "Config": {
     "Hostname": "cont05",
     "Domainname": "",
     "User": "",
...

    "NetworkSettings": {
     "Bridge": "",
     "SandboxID": "6fa73d632416d0f9ec52713b90253a5c4b6c5d5fe7fd037259ec63ed45c5f75f",
     "HairpinMode": false,
     "LinkLocalIPv6Address": "",
     "LinkLocalIPv6PrefixLen": 0,
     "Ports": {
      "80/tcp": [
      {
       "HostIp": "0.0.0.0",
       "HostPort": "55002"
      }
     ]
    },

```


6. On peut extraire une valeur en utilisant l'option "--format".
Par exemple, `docker inspect --format <champsAvisualiser> <container>`
	* State, on veut visualiser l'attribut Status du conteneur web05: le status est running. `docker inspect --format='{{.State.Status}}' web05`

```docker
dali@MacBookProDali ~ % docker inspect --format='{{.State.Status}}' web05
running
```

7. dernière possibilité, on va faire un "inspect".
	* Nous allons extraire un tableau en entier, en utilisant le **mot-clé json** et en donnant le nom du tableau.

```docker
dali@MacBookProDali ~ % docker inspect --format='{{json .NetworkSettings}}' web05
{"Bridge":"","SandboxID":"6fa73d632416d0f9ec52713b90253a5c4b6c5d5fe7fd037259ec63ed45c5f75f","HairpinMode":false,"LinkLocalIPv6Address":"","LinkLocalIPv6PrefixLen":0,"Ports":{"80/tcp":[{"HostIp":"0.0.0.0","HostPort":"55002"}]},"SandboxKey":"/var/run/docker/netns/6fa73d632416","SecondaryIPAddresses":null,"SecondaryIPv6Addresses":null,"EndpointID":"fed5f919b313124e3457c87cbc28fd82fb29d57a6cc994d6ad75a45a6bf86d6e","Gateway":"172.17.0.1","GlobalIPv6Address":"","GlobalIPv6PrefixLen":0,"IPAddress":"172.17.0.2","IPPrefixLen":16,"IPv6Gateway":"","MacAddress":"02:42:ac:11:00:02","Networks":{"bridge":{"IPAMConfig":null,"Links":null,"Aliases":null,"NetworkID":"3a074d3cb93f120aef82abb1ad7c8ba270111403038dacba94e5b0f11b515856","EndpointID":"fed5f919b313124e3457c87cbc28fd82fb29d57a6cc994d6ad75a45a6bf86d6e","Gateway":"172.17.0.1","IPAddress":"172.17.0.2","IPPrefixLen":16,"IPv6Gateway":"","GlobalIPv6Address":"","GlobalIPv6PrefixLen":0,"MacAddress":"02:42:ac:11:00:02","DriverOpts":null}}}
```

Nous observons que son IP est 172.17.0.2.

## Lister et supprimer des conteneurs

Nous allons voir en détail comment lister les conteneurs.

[références commandes docker ps](https://docs.docker.com/engine/reference/commandline/ps/)


* `docker ps` permet de **lister les conteneurs en cours d'utilisation**: seulement ceux qui sont running (up dans la colonne status)

```docker
dali@MacBookProDali ~ % docker ps
CONTAINER ID 		IMAGE 		COMMAND  			CREATED  			STATUS  		PORTS 					NAMES
1a8f7ba4cf91 		nginx 		"/docker-entry…" 	31 minutes ago 		Up 31 minutes 	0.0.0.0:55002->80/tcp 	web05
```

* `docker ps -a` permet de **lister tous les conteneurs**, même ceux qui sont terminés. Voilà, on voit ici quelques conteneurs qui sont en exited.

```docker
dali@MacBookProDali ~ % docker ps -a
CONTAINER ID 			IMAGE 		COMMAND  			CREATED  			STATUS  				PORTS 					NAMES
1a8f7ba4cf91 			nginx 		"/docker-entry…" 	33 minutes ago 		Up 33 minutes 			0.0.0.0:55002->80/tcp 	web05
8b348b47461c 			centos  	"ping 127.0.0.1" 	42 minutes ago 		Exited (0) 40 minutes ago 						cont04
72e895037095 			centos  	"/bin/bash"  		45 minutes ago 		Exited (0) 45 minutes ago 						cont03
99c28791e53a 			nginx 		"/docker-entry…" 	8 days ago 			Exited (137) 8 days ago 						charming_knuth
e26d2a83a200 			centos  	"ping 127.0.0.1" 	8 days ago 			Exited (0) 8 days ago 							cont02
c1c43fcdb5ed 			ubuntu  	"/bin/bash"  		8 days ago 			Exited (0) 8 days ago 							funny_mcnulty
9a210b097f0c 			alpine  	"/bin/sh"  			8 days ago 			Exited (0) 8 days ago 							pensive_fermi
```

* `docker ps -l` (latest) permet d'afficher uniquement le dernier conteneur créé.

```docker
dali@MacBookProDali ~ % docker ps -l
CONTAINER ID 			IMAGE 		COMMAND  			CREATED  			STATUS  				PORTS 					NAMES
1a8f7ba4cf91 			nginx 		"/docker-entry…" 	33 minutes ago 		Up 33 minutes 			0.0.0.0:55002->80/tcp 	web05
```

* `docker ps --last <nombre>` permet, quand à elle, uniquement de visualiser les n derniers conteneurs.

```docker
dali@MacBookProDali ~ % docker ps --last 3
CONTAINER ID 			IMAGE 		COMMAND  			CREATED  			STATUS  				PORTS 					NAMES
1a8f7ba4cf91 			nginx 		"/docker-entry…" 	33 minutes ago 		Up 33 minutes 			0.0.0.0:55002->80/tcp 	web05
8b348b47461c 			centos  	"ping 127.0.0.1" 	42 minutes ago 		Exited (0) 40 minutes ago 						cont04
72e895037095 			centos  	"/bin/bash"  		45 minutes ago 		Exited (0) 45 minutes ago 						cont03
```
> Remarque: si des containers ont été supprimés entre temps le nombre affiché sera égal aux nombre créés - le nombre de supprimés

* `docker ps -q` qui permet d'afficher uniquement l'ID des conteneurs en marche, nous pouvons combiner cette option avec d'autres.

```docker
dali@MacBookProDali ~ % docker ps -q
1a8f7ba4cf91
```

* option --filter : `docker ps -a --filter name=web`  permet d'afficher tous les conteneurs qui contiennent web dans le nom du conteneur.

```docker
dali@MacBookProDali ~ % docker ps -a --filter name=web
CONTAINER ID 			IMAGE 		COMMAND  			CREATED  			STATUS  				PORTS 					NAMES
1a8f7ba4cf91 			nginx 		"/docker-entry…" 	33 minutes ago 		Up 33 minutes 			0.0.0.0:55002->80/tcp 	web05
```

* `docker ps --filter status=exited`: nous pouvons afficher uniquement les conteneurs d'un certain status.

```docker
dali@MacBookProDali ~ % docker ps --filter status=exited
CONTAINER ID 			IMAGE 		COMMAND  			CREATED  			STATUS  				PORTS 					NAMES
8b348b47461c 			centos  	"ping 127.0.0.1" 	42 minutes ago 		Exited (0) 40 minutes ago 						cont04
72e895037095 			centos  	"/bin/bash"  		45 minutes ago 		Exited (0) 45 minutes ago 						cont03
99c28791e53a 			nginx 		"/docker-entry…" 	8 days ago 			Exited (137) 8 days ago 						charming_knuth
e26d2a83a200 			centos  	"ping 127.0.0.1" 	8 days ago 			Exited (0) 8 days ago 							cont02
c1c43fcdb5ed 			ubuntu  	"/bin/bash"  		8 days ago 			Exited (0) 8 days ago 							funny_mcnulty
9a210b097f0c 			alpine  	"/bin/sh"  			8 days ago 			Exited (0) 8 days ago 							pensive_fermi
```

* Et on pourra même afficher uniquement les conteneurs avec une valeur spécifique du status exited=137 par exemple, on a vu que c'était le code erreur lorsqu'on a envoyé la commande kill à un conteneur. `docker ps -a --filter exited=137`

```docker
docker ps -a --filter exited=137
CONTAINER ID 			IMAGE 		COMMAND  			CREATED  			STATUS  				PORTS 					NAMES
99c28791e53a 			nginx 		"/docker-entry…" 	8 days ago 			Exited (137) 8 days ago
```

* Nous pouvons aussi modifier l'apparence de la sortie standard, et formater suivant les différentes colonnes de la commande docker ps et l'option "--format" qui va utiliser le nom de colonnes `docker ps -a --format "{{.Names}} : {{.Status}}"`

```docker
dali@MacBookProDali ~ % docker ps -a --format "{{.Names}} : {{.Status}}"

web05 : Up 51 minutes
cont04 : Exited (0) 58 minutes ago
cont03 : Exited (0) About an hour ago
charming_knuth : Exited (137) 8 days ago
cont02 : Exited (0) 8 days ago
funny_mcnulty : Exited (0) 8 days ago
pensive_fermi : Exited (0) 8 days ago
```

* La suppression des conteneurs:
	* `docker ps -a` affiche la liste des conteneurs
	* la suppression de conteneurs ne pourra s'effectuer que sur un conteneur qui s'est terminé. `docker rm <nomContainer>`

* Si on souhaite supprimer un conteneur qui est en cours d'exécution, on aura forcément un message d'erreur et, bien sûr, on peut toujours forcer par l'option "-f " pas un arrêt propre, mais il est supprimé. `docker rm -f <nomContainer>`

```docker
dali@MacBookProDali ~ % docker ps -a
CONTAINER ID 			IMAGE 		COMMAND  			CREATED  			STATUS  				PORTS 					NAMES
1a8f7ba4cf91 			nginx 		"/docker-entry…" 	33 minutes ago 		Up 33 minutes 			0.0.0.0:55002->80/tcp 	web05
8b348b47461c 			centos  	"ping 127.0.0.1" 	42 minutes ago 		Exited (0) 40 minutes ago 						cont04
72e895037095 			centos  	"/bin/bash"  		45 minutes ago 		Exited (0) 45 minutes ago 						cont03
99c28791e53a 			nginx 		"/docker-entry…" 	8 days ago 			Exited (137) 8 days ago 						charming_knuth
e26d2a83a200 			centos  	"ping 127.0.0.1" 	8 days ago 			Exited (0) 8 days ago 							cont02
c1c43fcdb5ed 			ubuntu  	"/bin/bash"  		8 days ago 			Exited (0) 8 days ago 							funny_mcnulty
9a210b097f0c 			alpine  	"/bin/sh"  			8 days ago 			Exited (0) 8 days ago 							pensive_fermi

dali@MacBookProDali ~ % docker rm pensive_fermi
pensive_fermi

dali@MacBookProDali ~ % docker ps -a
CONTAINER ID 			IMAGE 		COMMAND  			CREATED  			STATUS  				PORTS 					NAMES
1a8f7ba4cf91 			nginx 		"/docker-entry…" 	33 minutes ago 		Up 33 minutes 			0.0.0.0:55002->80/tcp 	web05
8b348b47461c 			centos  	"ping 127.0.0.1" 	42 minutes ago 		Exited (0) 40 minutes ago 						cont04
72e895037095 			centos  	"/bin/bash"  		45 minutes ago 		Exited (0) 45 minutes ago 						cont03
99c28791e53a 			nginx 		"/docker-entry…" 	8 days ago 			Exited (137) 8 days ago 						charming_knuth
e26d2a83a200 			centos  	"ping 127.0.0.1" 	8 days ago 			Exited (0) 8 days ago 							cont02
c1c43fcdb5ed 			ubuntu  	"/bin/bash"  		8 days ago 			Exited (0) 8 days ago 							funny_mcnulty


dali@MacBookProDali ~ % docker rm -f web05
web05

dali@MacBookProDali ~ % docker ps -a
CONTAINER ID 			IMAGE 		COMMAND  			CREATED  			STATUS  				PORTS 					NAMES
8b348b47461c 			centos  	"ping 127.0.0.1" 	42 minutes ago 		Exited (0) 40 minutes ago 						cont04
72e895037095 			centos  	"/bin/bash"  		45 minutes ago 		Exited (0) 45 minutes ago 						cont03
99c28791e53a 			nginx 		"/docker-entry…" 	8 days ago 			Exited (137) 8 days ago 						charming_knuth
e26d2a83a200 			centos  	"ping 127.0.0.1" 	8 days ago 			Exited (0) 8 days ago 							cont02
c1c43fcdb5ed 			ubuntu  	"/bin/bash"  		8 days ago 			Exited (0) 8 days ago 							funny_mcnulty
```


* Enfin, on peut aussi combiner les commandes, pour par exemple supprimer tous les conteneurs qui sont arrêtés. `docker rm $(docker ps -aq --filter status=exited)`
	* l'option "q" est pour récupérer automatiquement tous les ID des containers pour pouvoir supprimer tous ceux qui sont dans le status "exited"

```docker
dali@MacBookProDali ~ % docker ps -a
CONTAINER ID 			IMAGE 		COMMAND  			CREATED  			STATUS  				PORTS 					NAMES
8b348b47461c 			centos  	"ping 127.0.0.1" 	42 minutes ago 		Exited (0) 40 minutes ago 						cont04
72e895037095 			centos  	"/bin/bash"  		45 minutes ago 		Exited (0) 45 minutes ago 						cont03
99c28791e53a 			nginx 		"/docker-entry…" 	8 days ago 			Exited (137) 8 days ago 						charming_knuth
e26d2a83a200 			centos  	"ping 127.0.0.1" 	8 days ago 			Exited (0) 8 days ago 							cont02
c1c43fcdb5ed 			ubuntu  	"/bin/bash"  		8 days ago 			Exited (0) 8 days ago 							funny_mcnulty

dali@MacBookProDali ~ % docker ps -aq --filter status=exited
8b348b47461c
72e895037095
99c28791e53a
e26d2a83a200
c1c43fcdb5ed

dali@MacBookProDali ~ % docker rm $(docker ps -aq --filter status=exited)
8b348b47461c
72e895037095
99c28791e53a
e26d2a83a200
c1c43fcdb5ed

dali@MacBookProDali ~ % docker ps -a
CONTAINER ID IMAGE COMMAND CREATED STATUS  PORTS NAMES
```


## Comprendre les couches d'une image

### Image, couche et construire son image

####  Qu'est-ce qu'un Image?
> * **Une image est une collection de fichiers**: ces fichiers sont stockés dans des systèmes de fichiers et sont les fichiers binaires de l'application, les fichiers de configuration de l'application, ainsi que tout ce qui est libraries nécessaires au fonctionnement de l'application.
> * Une **image est composée de plusieurs couches qui sont en lecture**.
>  * Chaque **couche représente une brique logicielle** qui va se rajouter à la couche inférieure.
>  * Les **systèmes de fichiers sont fusionnés**, le terme utilisé est **_union file system_**.
>  * On a un **système de fichier par union**: empilés ou fusionnés.
>  * Une image contient aussi une couche de base. Dans certains cas, cette image de base ou cette couche de base peut être vierge.

####  Qu'est-ce qu'un Couche?
>  * Les couches des images sont toujours en lecture: ce sont des file system qui sont montés en lecture seule et ne peuvent jamais être modifiés pendant l'exécution du conteneur. Par exemple pour un serveur web, la couche Debian contient l'environnement de l'OS puis la couche Apache qui contient le serveur Web: ces 2 couches sont montées en lecture.
>  * **Lorsque nous lançons un conteneur, une dernière couche est créée ou un dernier file system est monté en écriture**: **toutes les modifications dans un conteneur se font toujours au niveau de cette couche**.
>  * Nous utilisons un mécanisme **COW** (Copy On Write): pour modifier quelque chose, il faut d'abord le copier puis le modifier: si le conteneur veut modifier un fichier qui se trouve dans une couche basse, qui elle est en lecture, le fichier est d'abord copié sur la couche haute, en écriture, ensuite il sera possible de le modifier à ce niveau-là.
>  * Si au contraire, nous accédons à un fichier en lecture, nous accédons au fichier qui se trouve au niveau de la couche en lecture, et il ne sera pas copié en écriture: il va toujours rester dans la couche où il se trouve.

#### Comment créer une image?

Il existe plusieurs manières de créer une image.

[références commandes docker commit](https://docs.docker.com/engine/reference/commandline/commit/)

1. **Modification d'un container**
	* Nous allons **créer un conteneur à partir d'une image**, **puis faire des modifications dans le conteneur**.
	* Une fois ces **modifications terminées**, **arrêtons proprement le conteneur, puis validons les modifications** par une commande `docker commit`
	* Dans ce cas-là, la couche Read-Write qui contient les modifications va être insérée dans une nouvelle image en lecture seulement.
	* C'est donc une **modification dynamique**.


2. **Créer un fichier Dockerfile** qui détaille la construction de l'image.
	* Cette **modification statique**, permet de **gérer**, par exemple **les versions des images** pour chaque version de Dockerfile.
	* Et on pourra aussi garder une trace des modifications apportées à l'image.
	* **C'est la bonne pratique**.


3. **Créer une image à partir d'une archive**
	* Une archive contient les fichiers que nous souhaitons intégrer dans l'image.
	* Cette archive est créée par une commande de sauvegarde dans une archive, comme une commande de type TAR.
	* Nous créons une sauvegarde du conteneur dans une archive, puis nous utilisons un mécanisme d'importation pour intégrer cette archive au niveau de la nouvelle image.

#### Les namespaces: espace de nommage

1. Le premier namespace qui existe est le **namespace Root**.
	* Le namespace Root ou Racine est réservé à Docker et à toutes les organisations officielles, c'est-à-dire les distributeurs.
	*  Le nom de l'image sera directement positionné au niveau de la racine donc il n'y aura pas de préfixe du type utilisateur ou organisation. ex: **ubuntu:14.04**

2. Pour les autres organisations et utilisateurs, **toutes les images sont préfixées par le nom du user** qui a créé un compte individuel au niveau du Docker Hub ou une organisation, et dans ce cas-là, le nom de l'image est préfixé par le nom du user ou le nom de l'organisation. ex: **<masociete/monappli:1.0>**

3. Vous pouvez **créer votre propre registry**
	* Un **registre interne pour distribuer vos images à vos utilisateurs**, au sein de votre entreprise.
	* Nous allons démarrer une application qui va servir de registry et vous allez stocker vos images avec un nom spécifique qui est préfixé par le nom du site Web de votre serveur registry par exemple **<registry.masociete.com:5000/monappli>**.



### Créer une image à partir d'un conteneur

Nous allons créer une image à partir de la modification d'un conteneur.
`docker commit <nomImage> <nomRepository:tag>`

1. Lancer un conteneur à partir de l'image de CentOS: `docker run -it --name=mycentos1 centos`
2. Donner un nom temporaire conteneur.
3. Alors, on va voir par exemple que la commande wget n'existe pas dans l'image officielle de CentOS donc on va l'installer par une commande `yum install -y wget`
4.  wget fonctionne et est disponible.
5. Arrêter proprement le conteneur par la commande `exit`.
6.  `docker ps -a` : le conteneur s'est arrêté.
7. On peut visualiser les différences apportées au conteneur, par rapport à son image.
	* Faisons un `docker diff mycentos1`. "mycentos1" étant l'image que l'on vient de modifier.
	*Nous observons un ensemble de modifications apportées et nous avons bien installé "usr/bin/wget".
		* Le premier caractère identifie en fait, l'opération :
			* **A** signifie un **add**
			* **C** signifie un **change**
			* **D** pour **delete**  
		* Bien sûr, il y a eu d'autres ajouts de fichiers temporaires, de mises à jour de /var /lib etc.
		* En fait, on a bien installé le wget et tous les composants nécessaires pour le fonctionnement de wget.
8. Valider la modification et créer une nouvelle image.
	* La commande est `docker commit <nomImage> <notreNameSpace:version>`.
		* dans notre cas c'est `docker commit mycentos1 masociete/moncentos:1.0`

9. Vérifions cette image: lançons un conteneur à partir de cette image, et vérifier la commande wget.
`docker run -it masociete/moncentos:1.0`
`wget`
	* l'image est vérifiée

```docker
dali@MacBookProDali ~ % docker run -it --name=mycentos1 centos
[root@b05430e32e96 /]# yum install -y wget

Failed to set locale, defaulting to C.UTF-8
CentOS Linux 8 - AppStream  562 kB/s | 6.3 MB 00:11
CentOS Linux 8 - BaseOS 601 kB/s | 2.3 MB 00:03
CentOS Linux 8 - Extras  17 kB/s | 9.6 kB 00:00
Dependencies resolved.
...
Installed:
libpsl-0.20.2-6.el8.x86_64 publicsuffix-list-dafsa-20180723-1.el8.noarch wget-1.19.5-10.el8.x86_64
Complete!

[root@b05430e32e96 /]# wget
wget: missing URL
Usage: wget [OPTION]... [URL]...
Try `wget --help' for more options.
[root@b05430e32e96 /]# exit
exit

dali@MacBookProDali ~ % docker ps -a
CONTAINER ID 		IMAGE 		COMMAND 		CREATED 		STATUS  							PORTS 		NAMES
b05430e32e96 		centos  	"/bin/bash" 	2 minutes ago 	Exited (1) About a minute ago 					mycentos1

dali@MacBookProDali ~ % docker images
REPOSITORY 	TAG 		IMAGE ID 		CREATED 		SIZE
ubuntu 		latest  	26b77e58432b 	8 days ago  	72.9MB
alpine 		latest  	49f356fa4513 	10 days ago 	5.61MB
nginx  		latest  	7ce4f91ef623 	11 days ago 	133MB



dali@MacBookProDali ~ % docker commit mycentos1 masociete/moncentos:1.0
sha256:f373c842872febe3c8c1c524cca0c1ee051a9a0312e5537dc8690a072ac1a090

dali@MacBookProDali ~ % docker images
REPOSITORY 				TAG 		IMAGE ID 		CREATED 		SIZE
masociete/moncentos  	1.0 		f373c842872f 	5 seconds ago 	242MB
ubuntu 					latest  	26b77e58432b 	8 days ago  	72.9MB
alpine 					latest  	49f356fa4513 	10 days ago 	5.61MB
nginx  					latest  	7ce4f91ef623 	11 days ago 	133MB

dali@MacBookProDali ~ % docker diff mycentos1
C /etc
C /etc/ld.so.cache
A /etc/wgetrc
C /usr
...
A /usr/share/doc/wget
...
C /usr/share/locale/en_GB
C /usr/share/locale/en_GB/LC_MESSAGES
A /usr/share/locale/en_GB/LC_MESSAGES/wget.mo
C /usr/bin
A /usr/bin/wget
C /root
A /root/.bash_history
C /var
C /var/log
...
```
>_Remarque_:
>* nous voyons avec le "diff" qu'il y a une différence au niveau de wget (ce que nous avons installé)
>* l'image que nous venons de créer ne se trouve pas encore dans notre docker hub, pour cela il faut faire un `docker push`.

### Créer une image à partir d'un Dockerfile

[références dockerfile](https://docs.docker.com/engine/reference/builder/)


> Un **Dockerfile est un fichier de configuration qui contient un ensemble d'instructions pour construire une image**.
> La bonne pratique étant de créer un dossier par image ou un dossier par projet.

```bash
dali@MacBookProDali DOCKER % mkdir DockerFiles
dali@MacBookProDali DOCKER % cd DockerFiles
dali@MacBookProDali DockerFiles % mkdir monPremierDockerFile
dali@MacBookProDali DockerFiles % ll
total 0
3500845 0 drwxrwxrwx  1 dali  staff 264B  1 mai 11:36 .
3500023 0 drwxrwxrwx  1 dali  staff 264B  1 mai 11:34 ..
3500846 0 drwxrwxrwx  1 dali  staff 264B  1 mai 11:36 monPremierDockerFile
dali@MacBookProDali DockerFiles % cd monPremierDockerFile
dali@MacBookProDali monPremierDockerFile % touch Dockerfile
dali@MacBookProDali monPremierDockerFile % ll
total 0
3500846 0 drwxrwxrwx  1 dali  staff 264B  1 mai 11:37 .
3500845 0 drwxrwxrwx  1 dali  staff 264B  1 mai 11:36 ..
3500847 0 -rwxrwxrwx  1 dali  staff 0B  1 mai 11:36 Dockerfile
```


1. La **première instruction** d'un Dockerfile doit toujours être **FROM** qui définit quelle est la première couche de base pour construire l'image.
	* On va partir d'une image de CentOS.

2. La **deuxième instruction** dans un Dockerfile, c'est **RUN** qui spécifie une commande qui doit être exécutée à l'intérieur du conteneur pendant la création de l'image.
	* Elle permet, d'installer des logiciels, des libraries ou de faire des modifications dans les fichiers de config.
	* Par exemple, on va faire un `yum install -y wget`.
```bash
dali@MacBookProDali monPremierDockerFile % nano Dockerfile
```
```dockerfile
FROM centos
RUN yum install -y wget
```


3. Nous allons maintenant lancer la commande pour construire l'image: `docker build -t masociete/mycentosdockerfile:1.0 .`
	* "-t" pour le tag
	* "masociete/mycentos:1.0" pour choisir l'image à créer
	* "." le répertoire dans lequel se situe le fichier docker fichier (ici le répertoire courant)
```docker
dali@MacBookProDali monPremierDockerFile % docker build -t masociete/mycentosdockerfile:1.0 .

[+] Building 20.6s (6/6) FINISHED
=> [internal] load build definition from Dockerfile  										0.1s
=> => transferring dockerfile: 78B 															0.1s
=> [internal] load .dockerignore 															0.0s
=> => transferring context: 2B 																0.0s
=> [internal] load metadata for docker.io/library/centos:latest  							0.0s
=> [1/2] FROM docker.io/library/centos 														0.0s
=> [2/2] RUN yum install -y wget  															20.1s
=> exporting to image  																		0.3s
=> => exporting layers 																		0.3s
=> => writing image sha256:ff95b95ed1d1bd46c64ab9199f5fd63560fdcbd66567195091071d212295b89c  0.0s
=> => naming to docker.io/masociete/mycentosdockerfile:1.0 									0.0s
```
> **_Remarque_: Ne pas oublier le "." à la fin de la commande docker build pour savoir où est le fichier Dockerfile**

> * **FROM centos** va charger comme première couche de base CentOS
> * Ensuite on va exécuter la commande `yum install -y wget`
> * Vérifiez qu'il y a bien une installation du package en question et une fois que le package est installé, nous allons construire la nouvelle image.
> * Tout ça s'est fait sur un conteneur temporaire et à la fin, ce conteneur temporaire est transformé en image. `docker images` permet de voir la nouvelle image

4. Nous allons maintenant rajouter un autre package ou un autre logiciel.
	* On va rajouter une nouvelle ligne dans le fichier dockerfile avec "vi", et on va, cette fois-ci, ajouter le package zip qui n'existe pas, de base, sur CentOS. `RUN yum install -y zip`
```dockerfile
FROM centos
RUN yum install -y wget
RUN yum install -y zip
```

5. Nous exécutons la commande build mais cette fois-ci, en rajoutant une version supplémentaire.  `docker build -t masociete/mycentosdockerfile:1.1 .`

```docker
dali@MacBookProDali monPremierDockerFile % docker build -t masociete/mycentosdockerfile:1.1 .
[+] Building 3.0s (7/7) FINISHED
=> [internal] load build definition from Dockerfile  									   	0.1s
=> => transferring dockerfile: 101B  														0.0s
=> [internal] load .dockerignore 															0.0s
=> => transferring context: 2B 																0.0s
=> [internal] load metadata for docker.io/library/centos:latest  							0.0s
=> [1/3] FROM docker.io/library/centos 														0.0s
=> CACHED [2/3] RUN yum install -y wget  													0.0s
=> [3/3] RUN yum install -y zip  															2.7s
=> exporting to image  																		0.1s
=> => exporting layers 																		0.1s
=> => writing image sha256:e8de9b4c39e7343b9dd1153b8acdc5e58a4264fc95ba21f894df7190d885cab3  0.0s
=> => naming to docker.io/masociete/mycentosdockerfile:1.1 									0.0s
```


> * 0n part toujours de la première ligne qui est le FROM on rajoute une nouvelle couche
> * comme cette couche a été déjà créée auparavant, on va juste la réutiliser (mentionné avec "---> using cache")
> * on ne va pas réinstaller le `yum install -y wget`
> on va utiliser la couche qui a été sauvegardée dans un cache du fait qu'elle correspond exactement au même produit qui est basé sur la même version de CentOS.
> * Ensuite on installe l'application zip. `yum install -y zip`
> * Chaque ligne du fichier Dockerfile correspond à une couche au niveau de l'image finale.
> * On voit bien qu'il y a une nouvelle image en version 1.1.
```docker
dali@MacBookProDali monPremierDockerFile % docker images
REPOSITORY 						TAG 	IMAGE ID 		CREATED  			SIZE
masociete/mycentosdockerfile 	1.1 	e8de9b4c39e7 	6 minutes ago  		257MB
masociete/mycentosdockerfile 	1.0 	ff95b95ed1d1 	14 minutes ago 		242MB
masociete/moncentos  			1.0 	f373c842872f 	2 weeks ago  		242MB
```

##### Connaitre les différentes couches: `docker history <nomImage>`
[références commandes docker history](https://docs.docker.com/engine/reference/commandline/history/)
```docker
dali@MacBookProDali monPremierDockerFile % docker history masociete/mycentosdockerfile:1.1
IMAGE  			CREATED  			CREATED BY  										SIZE  		COMMENT
e8de9b4c39e7 	8 minutes ago  		RUN /bin/sh -c yum install -y zip # buildkit  		14.5MB  	buildkit.dockerfile.v0
<missing>  		17 minutes ago 		RUN /bin/sh -c yum install -y wget # buildkit 		32.8MB  	buildkit.dockerfile.v0
<missing>  		4 months ago /		bin/sh -c #(nop)  CMD ["/bin/bash"]  				0B
<missing>  		4 months ago 		/bin/sh -c #(nop)  LABEL org.label-schema.sc… 		0B
<missing>  		4 months ago 		/bin/sh -c #(nop) ADD file:bd7a2aed6ede423b7… 		209MB
```


La commande `docker history masociete/mycentosdockerfile:1.1` nous apprend qu'**une image est composée de plusieurs couches**.

*  **colonne IMAGE** est en réalité l'image ou la couche, avec son identifiant, la date de création, comment a été créée cette image: par quelle commande elle a été créée et la taille de l'image.
	* Il faut toujours passer par la couche la plus basse, qui est en bas: le CentOS FROM centos est la première couche (correspond à la couche où il y a "ADD")
* Nous avons utilisé une commande qui s'appelle **ADD**
	*  toutes ces commandes, en majuscules, **ADD**, **LABEL**, **CMD** sont des **commandes du Dockerfile**, ou les instructions du Dockerfile.
* le **file** qui **est** ici est l'**archive qui contient CentOS**.
* Dans une image, il y a toujours ce qu'on appelle les LABELS.
	* Le **LABEL** est une étiquette qui comprend "attribut=valeur" ("name=centos"), c'est pour identifier que la couche en question est bien celle de CentOS.
	* Un LABEL ne consomme pas d'espace important puisque c'est juste du texte.
* Il y a aussi **CMD** qui est la commande par défaut qu'on exécute lorsqu'on lance l'image
	* pour CentOS sI on lance un `docker run -ti centos` sans commande, la commande de base est bash.
* puis nos deux lignes d'abord wget et zip.
* il faut toujours lire le résultat de l'historique du bas vers le haut: c'est un empilement de couches.

### Assimiler les instructions du Dockerfile

#### Principales instructions d'un Dockerfile.

[références fichier dockerfile](https://docs.docker.com/engine/reference/builder/)

_Exemple de fichier_:
```dockerfile
# ceci est un commentaire
FROM centos
LABEL version="1.0"
LABEL description="Serveur Web Intranet"
LABEL os="ubuntu"
ENV JAVA_HOME /usr/bin/java
RUN yum install -y wget
WORKDIR /opt
ADD archive.tar.gz /
COPY fichier.conf /etc/
CMD ["ping", "127.0.0.1", "-c", "30"]
ENTRYPOINT ["monappli-start.sh"]			
```

1.  un **commentaire** démarre toujours avec le caractère **#**.
2. l'instruction **FROM** qui permet de commencer à partir d'une **image de base**.
3. l'instruction **LABEL** permet de **rajouter à l'image des informations** sur l’éditeur, la personne qui maintient le code ou des informations concernant l'application elle-même ou les systèmes sur lesquels elle doit s’exécuter ou elle peut être déployée.
	* Le LABEL peut être utilisé par certaines commandes pour réaliser des filtrages ou pour cibler les hôtes sur lesquels on va pouvoir exécuter les conteneurs. C'est le cas par exemple des ordonnanceurs.
	* Ici on a LABEL pour définir la version, LABEL pour rajouter une description et LABEL pour cibler les OS de type Ubuntu.
4. l'instruction **ENV** permet d'**initialiser des variables d'environnement** dans tout conteneur créé à partir de l'image.
	* on va pouvoir définir une variable JAVA_HOME qui sera utilisée par Java lorsqu'on va exécuter un conteneur et qui nécessite justement de compiler Java, ou accéder aux outils Java.
5. l'instruction **WORKDIR** **définit le dossier de travail dans le conteneur**.
	* lorsqu'on va exécuter des commandes qui utilisent des fichiers cibles à l’intérieur du conteneur et si ces fichiers sont en relatif, on va utiliser en fait cette variable WORKDIR pour définir quelle est la cible.
	* les commandes qui utilisent WORKDIR sont par exemple RUN, CMD, COPY
	* Par défaut, WORKDIR est égale à slash
6.  l'instruction **ADD** permet de **faire des copies** du fichier à partir de la source: c'est le répertoire à partir duquel on va exécuter la commande `docker build` et le répertoire où se trouve le fichier Dockerfile.
	* on va exécuter la commande ADD pour faire des copies à partir de cette source vers le conteneur.
	* On peut aussi utiliser ADD pour extraire des archives même si elles sont compressées à l’intérieur du conteneur: `ADD archive.tar.gz /`: c'est ce qu'on va utiliser par exemple lorsqu'on va démarrer à partir d'une image vierge.
	* pour démarrer à partir d'une image vierge, on va utiliser l'instruction FROM et le mot clé « scratch » au lieu par exemple du mot clé « CentOS », « Ubuntu », etc.
	*  ADD peut aussi utiliser des URL.
		* on peut aller copier des fichiers qui seront sur des sites web vers votre conteneur.
7. l'instruction **COPY** permet de **copier des fichiers à partir de la source du hôte vers le conteneur**.
	* La différence avec ADD est que la commande COPY ne va pas extraire les archives.
	* si vous voulez copier réellement une archive vers le conteneur, il ne faut pas utiliser ADD mais utiliser COPY.
	* on utilise COPY pour copier des fichiers de type config, etc.
8. l'instruction **CMD** spécifie la **commande par défaut qui va être exécutée lorsque le conteneur est créé**.
	* Elle n'existe qu'une seule fois dans un Dockerfile et elle peut être remplacée si au moment de l’exécution du conteneur, on spécifie la commande à la suite du nom du conteneur.
9. l'instruction **ENTRYPOINT** spécifie le **point d’entrée du conteneur** qui devrait être soit le binaire de votre application soit un script de démarrage de votre application.
	* ENTRYPOINT va remplacer la commande CMD.
	* La bonne pratique est d'avoir toujours un ENTRYPOINT dans un fichier Dockerfile.
	* si ENTRYPOINT existe et si CMD existe, alors CMD passe en tant qu’argument à ENTRYPOINT.
	* si ENTRYPOINT n'existe pas, c'est l’instruction CMD qui spécifie donc le premier processus qui est lancé par Docker lorsqu'on va créer le conteneur.
	* On a un script (monappli-start.sh) qui sera toujours le premier processus qui sera créé lorsqu'on va lancer un conteneur à partir de cette image.

#### Bonnes pratiques
* Chaque ligne qui modifie le conteneur pendant la construction de l'image crée une nouvelle couche.
* Il faut donc essayer de réduire le nombre de lignes dans le Dockerfile pour réduire le nombre de couches.
* Un nombre de couches important peut entraîner un problème de performance.
* L'utilisation de ENTRYPOINT dans un Dockerfile permet de garantir que votre application sera toujours lancée avec le même script, même si bien sûr on pourrait avoir des arguments pour paramétrer l’exécution de votre application.
* Pour réduire le nombre de couches, il faut utiliser le caractère `&&` et pour une meilleure lisibilité, on utilise le caractère `\` pour écrire une seule commande sur plusieurs lignes.
exemple:

```dockerfile
# --------------- DÉBUT COUCHE APACHE ---------------
RUN apt-get update -y && \
    apt-get install ${APT_FLAGS} apache2
# --------------- FIN COUCHE APACHE -----------------
```


=============================================


#### Liste des instructions dockerfile

-   **FROM**  : Définit l'image de base qui sera utilisée par les instructions suivantes.
**Syntaxe**:  `FROM <image>:<version> [AS <name>]`
ex:
```dockerfile
FROM debian:stable-slim
```
-   **LABEL**  : Ajoute des métadonnées à l'image avec un système de clés-valeurs.  
**Syntaxe**: `LABEL <key>=<value> <key>=<value> <key>=<value> ...`
```dockerfile
# MÉTADONNÉES DE L'IMAGE
LABEL version="1.0" maintainer="DALIFOL Michel <micheldalifol@gmail.com>"
```
-   **ARG**  : Variables temporaires qu'on peut utiliser dans un Dockerfile.
**Syntaxe**:  `ARG <name>[=<default value>]`
>_Remarque_: `ARG` est la seule variable qui peut être mentionnée avant la variable `FROM`
```dockerfile
# VARIABLES TEMPORAIRES
ARG APT_FLAGS="-q -y"
ARG DOCUMENTROOT="/var/www/html"
```
> dans l'exemple ci-dessus on peut rapeler les variables APT_FLAGS et DOCUMENTROOT de la manière suivante:
> `RUN touch "scripts.js" >$DOCUMENTROOT`

_Remarque_: [Docker a prédéfinis des variables ARG](https://docs.docker.com/engine/reference/builder/#predefined-args) comme `http_proxy`par exemple

-   **ENV**  : Variables d'environnements utilisables dans votre Dockerfile et conteneur.
**Syntaxe**: `ENV <key>=<value> ...`
```dockerfile
ENV MY_NAME="Michel Dalifol"
ENV MY_DOG=Rex\ The\ Dog
ENV MY_CAT=kitty
```
-   **RUN**  : Exécute des commandes Linux ou Windows lors de la création de l'image. Chaque instruction  RUN  va créer une couche en cache qui sera réutilisée dans le cas de modification ultérieure du Dockerfile.
**Syntaxe**: `RUN <command>`
```dockerfile
# --------------- DÉBUT COUCHE PHP ------------------
RUN apt-get install ${APT_FLAGS} \
    php-mysql \
    php && \
    rm -f ${DOCUMENTROOT}/index.html && \
    apt-get autoclean -y
```
-   **COPY**  : Permet de copier des fichiers depuis notre machine locale vers le conteneur Docker.
**Syntaxe**: 2 formes
`COPY [--chown=<user>:<group>] <src>... <dest>`
`COPY [--chown=<user>:<group>] ["<src>",... "<dest>"]`
```dockerfile
COPY app ${DOCUMENTROOT}
```
-   **ADD**  : Même chose que COPY mais prend en charge des liens ou des archives (si le format est reconnu, alors il sera décompressé à la volée).
**Syntaxe**: 2 formes
`ADD [--chown=<user>:<group>] <src>... <dest>`
`ADD [--chown=<user>:<group>] ["<src>",... "<dest>"]`
```dockerfile
ADD --chown=55:mygroup files* /somedir/
ADD --chown=bin files* /somedir/
ADD --chown=1 files* /somedir/
ADD --chown=10:11 files* /somedir/
```
-   **ENTRYPOINT**  : point d'entrée de votre conteneur: c'est la commande qui sera toujours exécutée au démarrage du conteneur. Il prend la forme de tableau JSON (ex : CMD ["cmd1","cmd1"]) ou de texte.
**Syntaxe**: `ENTRYPOINT command param1 param2`
```dockerfile
# DÉMARRAGE DES SERVICES LORS DE L'EXÉCUTION DE L'IMAGE
ENTRYPOINT service mysql start && mysql < /articles.sql && apache2ctl -D FOREGROUND
```
-   **CMD**  : Spécifie les arguments qui seront envoyés au  ENTRYPOINT. On peut aussi l'utiliser pour lancer des commandes par défaut lors du démarrage d'un conteneur. S'il est utilisé pour fournir des arguments par défaut pour l'instruction  ENTRYPOINT, alors les instructions  CMD  et  ENTRYPOINT  doivent être spécifiées au format de tableau JSON.
**Syntaxe**: `CMD command param1 param2`
```dockerfile
FROM ubuntu
CMD ["/usr/bin/wc","--help"]
```
-   **WORKDIR**  : Définit le répertoire de travail qui sera utilisé pour le lancement des commandes  CMD  et/ou  ENTRYPOINT  et ça sera aussi le dossier courant lors du démarrage du conteneur.
**Syntaxe**: `WORKDIR /path/to/workdir`
```dockerfile
# RÉPERTOIRE DE TRAVAIL
WORKDIR  ${DOCUMENTROOT}
```
-   **EXPOSE**  : Expose un port
**Syntaxe**: `EXPOSE <port> [<port>/<protocol>...]`
```dockerfile
# OUVERTURE DU PORTS HTTP en TCP et en UDP
EXPOSE 80/tcp
EXPOSE 80/udp
```
-   **VOLUMES**  : Crée un point de montage qui permettra de persister les données
**Syntaxe**:

-   **USER**  : Désigne quel est l'utilisateur qui lancera les prochaines instructions  RUN,  CMD  ou  ENTRYPOINT  (par défaut c'est l'utilisateur root).
**Syntaxe**:

- **ONBUILD** :
**Syntaxe**:

- **STOPSIGNAL** :
**Syntaxe**:

- **HEALTHCHECK** :
**Syntaxe**:

- **SHELL** :
**Syntaxe**:


### Créer une image à partir d'une archive

[références commandes docker export](https://docs.docker.com/engine/reference/commandline/export/)
[références commandes docker import](https://docs.docker.com/engine/reference/commandline/import/)

1. Nous allons créer un conteneur à partir de l'image de base centos.
`docker run -it --name=contempo centos`
```docker
dali@Mac ~ % docker run -it --name=contempo centos
Unable to find image 'centos:latest' locally
latest: Pulling from library/centos
7a0437f04f83: Pull complete
Digest: sha256:5528e8b1b1719d34604c87e11dcd1c0a20bedf46e83b5632cdeac91b8c04efc1
Status: Downloaded newer image for centos:latest
```
2. `wget` de base n'est pas installé.
```docker
[root@b947ddfae667 /]# wget
bash: wget: command not found
```
3. On l'installe: `yum install -y wget`
```docker
[root@b947ddfae667 /]# yum install -y wget
Failed to set locale, defaulting to C.UTF-8
CentOS Linux 8 - AppStream 995 kB/s | 6.3 MB 00:06
CentOS Linux 8 - BaseOS  958 kB/s | 2.3 MB 00:02
CentOS Linux 8 - Extras 33 kB/s | 8.6 kB 00:00
Dependencies resolved.
=====================================================================================================================================================
Package  Architecture  Version Repository  Size
=====================================================================================================================================================
Installing:
wget x86_64  1.19.5-10.el8 appstream  734 k
Installing dependencies:
libpsl x86_64  0.20.2-6.el8  baseos  61 k
publicsuffix-list-dafsa noarch  20180723-1.el8  baseos  56 k
Transaction Summary
=====================================================================================================================================================
Install  3 Packages
Total download size: 851 k
Installed size: 2.9 M
Downloading Packages:
(1/3): publicsuffix-list-dafsa-20180723-1.el8.noarch.rpm 229 kB/s |  56 kB 00:00
(2/3): libpsl-0.20.2-6.el8.x86_64.rpm  195 kB/s |  61 kB 00:00
(3/3): wget-1.19.5-10.el8.x86_64.rpm 844 kB/s | 734 kB 00:00
-----------------------------------------------------------------------------------------------------------------------------------------------------
Total  863 kB/s | 851 kB 00:00
warning: /var/cache/dnf/appstream-02e86d1c976ab532/packages/wget-1.19.5-10.el8.x86_64.rpm: Header V3 RSA/SHA256 Signature, key ID 8483c65d: NOKEY
CentOS Linux 8 - AppStream 1.6 MB/s | 1.6 kB 00:00
Importing GPG key 0x8483C65D:
Userid : "CentOS (CentOS Official Signing Key) <security@centos.org>"
Fingerprint: 99DB 70FA E1D7 CE22 7FB6 4882 05B5 55B3 8483 C65D
From : /etc/pki/rpm-gpg/RPM-GPG-KEY-centosofficial
Key imported successfully
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
Preparing  : 1/1
Installing : publicsuffix-list-dafsa-20180723-1.el8.noarch 1/3
Installing : libpsl-0.20.2-6.el8.x86_64  2/3
Installing : wget-1.19.5-10.el8.x86_64 3/3
Running scriptlet: wget-1.19.5-10.el8.x86_64 3/3
Verifying  : wget-1.19.5-10.el8.x86_64 1/3
Verifying  : libpsl-0.20.2-6.el8.x86_64  2/3
Verifying  : publicsuffix-list-dafsa-20180723-1.el8.noarch 3/3
Installed:
libpsl-0.20.2-6.el8.x86_64 publicsuffix-list-dafsa-20180723-1.el8.noarch wget-1.19.5-10.el8.x86_64
Complete!
```
4. On vérifie que wget s'est bien installé puis on arrête le conteneur. `wget`; `
exit``
```docker
[root@b947ddfae667 /]# wget
wget: missing URL
Usage: wget [OPTION]... [URL]...
[root@b947ddfae667 /]# exit
exit
```
5. On vérifie que le conteneur est bien arrêté. `docker ps -a`
```docker
dali@Mac ~ % docker ps -a
CONTAINER ID 		IMAGE  		COMMAND  		CREATED  			STATUS 					PORTS 		NAMES
b947ddfae667 		centos 		"/bin/bash"  	19 minutes ago 		Exited (127) 6 seconds ago 			contempo
3f84592783a1 		mdalifol/monalpine:1.0 		"/bin/sh"  			2 days ago Exited (0) 2 days ago  	laughing_stonebraker
9dfbaee47d70 		alpine 		"/bin/sh"  		2 days ago Exited (0) 2 days ago  	monAlpine1
fb09d3bb4c69 		ae2feff98a0c 		"/docker-entrypoint.…" 		4 weeks ago  Exited (255) 4 weeks ago 	0.0.0.0:55000->80/tcp 		kind_agnesi
616f5f4121a1 		ubuntu 		"ps" 	5 weeks ago  Exited (0) 5 weeks ago 	charming_kalam
97372c0e8f7a 		alpine 		"/bin/sh"  5	 weeks ago  Exited (0) 5 weeks ago 	infallible_lewin
3d027cac3c6f 		alpine/git 		"git clone https://g…" 	5 weeks ago  Exited (0) 5 weeks ago 		repo
```
6. On crée une archive à partir de ce conteneur `docker export -o moncentos.tar contempo`
	* `o` comme output
	* `moncentos.tar`, c'est une archive de type tar
	* on donne le nom du conteneur `contempo`
7. On vérifie qu'on a bien un fichier tar qui a été créé. `ls`
```docker

```
8. On peut même vérifier et voir son contenu, par la commande `tar tvf moncentos.tar` .
	* c'est l'ensemble des fichiers du conteneur qui a été archivé dans ce fichier.
	* Pour une question pratique, on va pouvoir compresser le fichier en question.
```docker

```
9. On va pouvoir compresser ce fichier par la commande : `gzip moncentos.tar`
```docker

```
10. On vérifie `ls`, Il est compressé
```docker

```
11. On va pouvoir l'envoyer par internet, le transférer... sur une autre machine. `scp moncentos.tar.gz centos2:`
```docker

```
12. On va basculer sur l'autre machine.
```docker

```
13. On vérifie sur cette nouvelle machine qu'on a bien récupéré le fichier `ls`
```docker

```
14. On vérifie qu'on n'a pour l'instant, aucune image (`docker images`), et on va extraire à partir de l'archive, et générer une nouvelle image.
```docker

```
15. On va utiliser la commande inverse qui est la `commande import`. `docker import moncentos.tar.gz masociete/moncentos:1.0`
	* la syntaxe étant `docker import <nomArchive> <nomImageAGenerer>`
```docker

```
16. On vérifie si l'image a été créée. `docker images`
```docker

```
17. Notre image est disponible.
18. On va tester cette nouvelle image par l'exécution d'un conteneur, par la commande `docker run -it <nomImageGeneree>`, soit `docker run -it masociete/moncentos:1.0 bash`
```docker

```
19. on vérifie si wget est bien présent. `wget`
```docker

```
20. On sort. `exit`
```docker

```

21. On peut vérifier que le conteneur s'est bien lancé, par la commande `docker ps -a`
```docker

```
22. On va utiliser la commande `history` pour voir le contenu de l'image. `docker history masociete/moncentos:1.0`

```docker

```
24. Il n'y a plus de notion de couches.
	* Rappelez-vous, avec le Dockerfile, on avait la couche de base qui était centos, et ensuite, les couches lorsqu'on a rajouté des composants, lorsqu'on a fait par exemple un `yum install`.
	* Lorsqu'on extrait une archive et on crée une image, l'image en question ne contient qu'une seule couche, car la création de l'archive s'est effectuée au niveau fichier, et donc il n'y a pas de notion de couches.

## Distribuer des images à partir du hub
[docker hub](https://hub.docker.com/)

1. Créer un compte gratuit donne le droit à plusieurs images gratuites ou des repository publics et un seul repository privé.
Pour avoir plusieurs repository privés, il faudra passer par des formules payantes.
2. Une fois le compte créé, choisir un « _Docker ID_ , avec un email, et un mot de passe.
Il sera le dépôt principal de vos images, vous pouvez créer d'autres dépôts en créant des organisations.
3. L'onglet _Explore_, est pour explorer les images des organisations et utilisateurs.
On y trouve aussi les organisations associées à son compte, ou la liste des organisations, un client avec qui vous collaborez pour pouvoir développer vos applications.
4. Sur le dashboard afin de voir comment _pusher_ une image.

5. Sur le terminal
	* Sur le serveur centos1, un Dockerfile  est prêt dans un dossier _alpine_.
	* Ce _Dockerfile_ permet de construire une nouvelle image à partir d'une image _alpine_, qui est une image de type Linux réduite.
	* Comme l'image alpine ne contient pas de curl, rajoutez une commande pour pouvoir installer le binaire curl.
	* Créons une image à partir de ce fichier Dockerfile: `docker build`, à partir du répertoire alpine.
	* On récupère l'image alpine
	* On rajoute les binaires curl et l'image est créée.
		*  la commande `docker build` n'a été pas utilisé avec l'option -t (comme tag): On a une image qui s'est créée, mais qui n'a pas de tag.
		* Pour donner un tag à une image:
			* Faisons un docker tag soit l'ID de l'image, soit le nom de l'image.
			* Appelons la _test_toto_, et on va l'appeler _alpine 1.0_.
			* L'image a bien un tag.
		* Utilisons la commande `docker tag` pour donner à mon image _alpine version 1.0_ un autre tag qui identifie la dernière version de mon image.
Rappelez-vous, c'est le tag latest.
			* Sont présent 2 tags, 1.0 et latest qui font référence à la même image.

6. Se connecter et envoyer l'image sur le site Docker Hub.
[références commandes docker volume push](https://docs.docker.com/engine/reference/commandline/push/)
	* Une fois connecté, _pusher_ l'image Docker.
`docker push test_toto/alpine` (avec "test_toto/alpine" le nom de l'image à publier sur docker hub)
	* Transférer l'image du serveur 1 vers Docker Hub.
7. Sur l'interface graphique faire un refresh de la page pour s'apercevoir que l'image _alpine_ est téléchargée.
8. Sur « DETAILS », la `commande pull` permet de la télécharger à partir du Docker Hub, et les deux tags 1.0 et latest sont présents.
9. Basculons sur le 2e serveur, pour récupérer l'image, et voir comment est distribué l'image
	* Sur centos2, vérifier quelles sont les images stockées en local, récupérer l'image par la commande `docker pull test_toto/alpine`.
	* `docker images`: l'image est présente sur centos2, on a récupéré l'image.
10. On récupère l'image de version 1.0 et il n'y a pas de téléchargement, puisqu'en réalité, c'est juste un tag supplémentaire.
	*	`docker images`: on a les 2 tags.
11. `docker rmi test_toto/alpine`: il y a un Untag sur le tag "latest": le tag a été supprimé, mais pas l'image.
12. On fait cette fois-ci un `docker rmi test_toto/alpine:1.0`: il y a Untagged et  on supprime physiquement l'image.
13. On a fait le Delete de l'image « alpine » basée sur 3 couches.

```docker

```

## Générer des volumes

[références commandes docker volume create](https://docs.docker.com/engine/reference/commandline/volume_create//)

> Un **volume est un dossier externe au conteneur, qui est monté dans l'arborescence du conteneur**.

* Ce mécanisme est conçu pour la **pertinence des données**, indépendamment du cycle de vie du conteneur, mais il est aussi parfois utilisé pour fournir aux conteneurs des fichiers de configuration à l'application qui s'exécute dans le conteneur.

* On l'utilise aussi dans un **but de performance**, si on veut éviter de passer à travers toutes les couches de l'image pour réaliser des entrées/sorties.

#### Créer un premier type de volume par la commande `docker volume create`.

1. `docker volume create --name vol1`: on vient de créer le volume "vol1".

2. `docker volume ls`:
	*  Nous avons un volume de type local où le driver est local, ce qui fait référence au filesystem local du centos et qui exécute Docker, et le volume s'appelle « vol1 ».

3. Pour utiliser le volume, il va falloir le monter au lancement du conteneur.
	* `docker run -it -v vol1://www/html centos bash`
		* `docker run -it` pour la partie interactive
		* `-v ` pour spécifier le volume, ainsi que son point de montage.
		* Si on est dans le cas d'un serveur Web `/www/html`
		* Dans notre cas, pour mieux voir le résultat, passons par une image `centos`.

4. `df -h`:
	* le point de montage `/www/http` est disponible
	* il est monté à partir d'un volume `/dev/mapper/centos-root` qui est le Filesystem `/` de l'OS.

5. Si nous faisons `exit`, puis `df -h`:
	*	`/dev/mapper/centos` est `/`

6. Pourquoi ? Vérifions les propriétés du volume.
	* Utilisons la commande inspect
	[références commandes docker volume inspect](https://docs.docker.com/engine/reference/commandline/inspect/)
	`docker volume inspect vol1`
		* Regardons les propriétés, la date de création, le driver est en local, le point de montage provient de `var/lib/docker/vol1/_data`.
		* C'est ce répertoire-là qui va être monté sur le point de montage spécifié dans la commande avec l'option `-v`
		* C'est la **configuration par défaut**: nous allons stocker des données dans le Filesystem `/` de l'OS.

> En production, la bonne pratique est de **créer un système de fichiers sur un disque externe, différent du disque système et le monter sur `var/lib/docker`**, qui est le dossier racine de Docker.

> Ça veut dire que toutes les informations de Docker se trouvent sous ce dossier.

```docker

```

#### Supprimer un volume
[références commandes docker volume rm](https://docs.docker.com/engine/reference/commandline/volume_rm/)

> **La suppression d'un volume ne peut se faire que si le volume n'est plus utilisé, ou passer l'option force**.

1.  `docker volume rm vol1`
	* Le volume est toujours utilisé par le conteneur.

2.  `docker ps -a`: notre conteneur est **_a remplir avec exemple_**: c'est le même conteneur.

3.  `docker rm <conteneur>`, le volume pourra être supprimer: `docker volume rm vol1`

4. Il est possible d'**utiliser des volumes anonymes** qui sont créés au moment de la création du conteneur si nous spécifions uniquement le point de montage.

5.  `docker run -it -v`: nous ne donnons que le point de montage.

6. `df -h`, nous voyons toujours le point de montage.

7. Faisons `exit` puis `docker volume ls`: un nouveau volume s'est créé de manière automatique au lancement du conteneur.

11. Pour supprimer les volumes anonymes, au moment où on supprime le conteneur, rajouter l'option `-v`

12. `docker rm -v <IDconteneur>`, on va supprimer le conteneur, **ET** en même temps, le volume.


```docker

```
