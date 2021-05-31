# APT-GET

Documentation [apt-get](https://doc.ubuntu-fr.org/apt-get)

## INSTALLATION DE PAQUETS

* connaitre la version et les dépendances d'un paquet:
  apt-get show <nomPaquet>

* installation de paquet:
  apt-get install <nomPaquet> <nomPaquet2>

* installaation d'une version présente dans le dépot
  apt-get install <nomPaquet>=<version> -V

* réparer une installation
  apt-get install -f <nomPaquet>

### INSTALLATION A PARTIR DE PAQUETS EN DEHORS DES DEPOTS [dpkg](https://doc.ubuntu-fr.org/dpkg)

Il arrive de devoir télécharger des fichier _.deb_ (pour debian) directement sans passer par les dépots car non présents.

* installer un paquet:
  dpkg -i <paquet.deb>

_Remarque: **dpkg** ne gère pas les dépendances, il faut souvent faire une réparation à sa suite (`apt-get install -f <nomPaquet>`)_

* installer paquet dans un dossier spécifique:
  for i in <cheminVersDossier/DEBS/*.deb>; do dpkg-deb -x $i . ; done       **


## RECEHRCHE DE PAQUETS

* recherche de paquet:
  apt-cache search <mot1 mot2>

* liste les paquets installés via dpkg:
  dpkg -l <nomPaquet> <nomPaquet2>

## SUPPRESSION DE PAQUETS

* suppression paquet:
  apt-get remove <nomPaquet> <nomPaquet2>

* suppression de paquet et ses dépendances:
  apt-get autoremove <nomPaquet> <nomPaquet2>

* purge de paquet avec ces fichiers de configuration:
  apt-get autoremove --purge <nomPaquet> <nomPaquet2>

* nettoie les copies des paquets installés dans _/var/cache/apt/archives_:
  apt-get clean

* supprime les "vielles" versions copies de paquets dans _/var/cache/apt/archives_:
  apt-get autoclean

* supprime paquet installé via dpkg
  dpkg -r <nomPaquet> <nomPaquet2>

* purge les paquets et les conf de ceux-ci:
  dpkg -P <nomPaquet> <nomPaquet2>

## MISE A JOUR DE PAQUETS  

* mise à jour des paquets:
  apt-get update

* mise à jour des paquets définis:
  apt-get update <nomPaquet> <nomPaquet2>

* upgrade des paquets:
  apt-get upgrade

* upgrade des paquets définis:
  apt-get upgrade <nomPaquet> <nomPaquet2>

* upgrade de tous les paquets et de la distribution:
  apt-get dist-upgrade

## BLOQUER LES MISES A JOUR D'UN PAQUET

Il faut aller dans le fichier **/etc/apt/preferences** et ajouter les lignes ci-dessous:

  `Package: nom_du_paquet
  Pin: version du paquet à conserver
  Pin-priority: 1001`
_Remarque: si ce fichier n'existe pas, il faut le créer._

Dans notre exemple, _Pin-priority_ à **1001** bloque les futures versions.
