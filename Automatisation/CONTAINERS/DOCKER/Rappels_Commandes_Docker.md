# RAPPELS COMMANDES DOCKER

Petit résumé des commandes docker les plus souvent utilisées.


## INSTALLATION DE DOCKER

#### Installation Linux
```terminal
yum-utils
yum-config-manager
yum install -y
systemctl start docker
systemctl enable docker
docker version
docker ps -a
```

#### Installation MacOSX
```terminal
brew install docker
brew install --cask docker
brew install docker-machine
brew install docker-compose
docker version
docker ps -a
```


## UTILISATION DE DOCKER


| Action       |     Syntaxe    |        Exemple |    Remarques  |
| :------------ | :-------------: | :-------------: |-------------: |
| Chercher une image sur dockerHub      |     `docker search <nomImage>`     |        `docker search centos` ||
| Récupérer une image à partir du dockerHub    |   `docker pull <nomImage>`    |      `docker pull centos`||
| Lancer une image docker en la renommant ainsi que le Hostname        |     `docker run --name=<nomMachine> --hostname=<nomHostanme -it <image>`      |         `docker run --name=centos_test --hostname=host_centosTest -it centos` ||
| Lister les images existantes en local |  `docker images` | `docker images` | affiche la liste des images|
| Lister les containers running en local |  `docker ps` | `docker ps` | affiche la liste des containers running|
| Lister les containers existants en local |  `docker ps -a` | `docker ps -a` | affiche la liste des containers|
| Rédémarrer un container existant |  `docker run -ti <imageID>` | `docker run -ti 300e315adb2f` ||
| Supprrimer les containers locaux |  `docker rm --force <containerID>` | `docker rm --force c15dde3a4319` |la valeur de <containerID> provient du résultat de `docker pas -a`|
| Supprrimer tous les containers au status "exited" |  `docker rm $(docker ps -aq --filter status=exited)` | `docker rm $(docker ps -aq --filter status=exited)` |l'option "q" est pour récupérer automatiquement tous les ID des containers pour pouvoir supprimer tous ceux qui sont dans le status "exited"|
| Supprimer un container automatiquement  lorsqu'il se termine     |     `docker run -it --rm <nomImage>:<tag>`     |        `docker run -it --rm centos:latest` ||
| Démarrer un container existant     |     `docker start <IDContainer>`     |        `docker start 99c28791e53a` ||
| Arrêter proprement un container existant     |     `docker stop <IDContainer>`     |        `docker stop 99c28791e53a` ||
| Arrêter un container existant     |     `docker kill <IDContainer>`     |        `docker kill 99c28791e53a` | permet de débloquer un container lorsqu'il est buggué |



## CREER IMAGE

### MODIFICATION DE CONTAINER

| Action       |     Syntaxe    |        Exemple |    Remarques  |
| :------------ | :-------------: | :-------------: |-------------: |
| lancer un container à partir d'une image | `docker run -it --name=<nomImage> <imageOfficielle>` | `docker run -it --name=monApline1 alpine` |  |
| faire une modification sur l'image | `apk update` `apk add` | `apk update` `apk add` | "apk" correspond à "apt", permet de faire une mise à jour |
| sortir du container l'image | `exit` | `exit` | permet de sortir proprement du container |
| constater les modifications | `docker diff <nomImage>` | `docker diff monAlpine1` | des différences s'affichent avec "A", "C", "D" |
| sauver les modifs | `docker commit <nomImage> <notreNameSpace:version>`| `docker commit monAlpine1 mdalifol/monalpine:1.0` | permet de sauver ma modifications |
| utiliser les modifs | `docker run -it <notreNameSpace:version>`| `docker run -it mdalifol/monalpine:1.0` |  |


### UTILISATION DE DOCKERFILE

1. Créer un fichier dockerfile dans le répertoire correspondant au projet: **1 fichier dockerfle par projet**
2.

| Action       |     Syntaxe    |        Exemple |    Remarques  |
| :------------ | :-------------: | :-------------: |-------------: |
|  |  |  |  |
|  |  |  |  |
|  |  |  |  |
|  |  |  |  |
|  |  |  |  |
