# TAR

La commande **tar** permet de créer une archive compresser ou non. Pour cela il suffit de modifier ses paramètres.
Elle permet aussi de faire le chemin inverse: extraire des archives.


## COMPRESSER UN FICHIER

`tar -czvf nom-de-l-archive.tar.gz </chemin/vers/répertoire-ou-fichier>`

* **c**: crée une archive
* **z**: compresse l'archive avec gzip
* **v**: mode verbeux, affiche la progression
* **f**: permet de specifier le nom de l'archive

Une compression se fait de manière récursive: elle compresse tous les fichiers et sous-dossiers du répertoire spécifié.


## COMPRESSER PLUSIEURS FICHIERS A LA FOIS
`tar -czvf nomArchive.tar.gz </cheminFicher1aArchiver> </cheminFichier2aArchiver> </cheminFichierNaArchiver>`

Tous ces fichiers (ou répertoires) seront dans la même archive nomArchive.tar.gz


##COMPRESSION BZIP2
`tar -cjvf archive.tar.bz2 </cheminFicher1aArchiver>`


## EXCLURE DES FICHIERS

Pour exclure des fichiers (dossiers) lors de la création d'une archive il faut utiliser l'option `--exclude`.
`tar -czvf nomArchive.tar.gz </cheminFicher1aArchiver> --exclude=</Fichier1aExclure> --excluse=</fichier2aExclure> `

_Remarque_: On peut aussi utiliser des modèles pour exclure des types de fichiers

`tar -czvf nomArchive.tar.gz </cheminFicher1aArchiver> --exclude=</*.jpg>`

Ici on a exclut de l'archive toutes les images jpg.


## DECOMPRESSER UNE ARCHIVE

Il faut utiliser l'option `-x` pour décompresser.
Pour extraire dans un répertoire spécifique, il faut utiliser l'option `-C`.

`tar -czvf nomArchive.tar.gz -C </cheminDestinationDearchivage>`
