# SED

**Sed** peut faire des choses qui prendraient des heures à faire avec une interface graphique.
La puissance de **sed** provient des expressions régulière.
**Sed** fonctionne en mode "flux". Cela veut dire qu'un flux en entrée (fichier ou autre) est traité ligne par ligne. Ceci garantit de bonnes performances ainsi qu'une utilisation réduite de mémoire, mais empêche certains types d'utilisation.

> Il existe 2 manières d'utiliser `sed`
>* "classique": on applique la commande sur le flux d'entrée et on récupère le flux de sortie
>* "directe": `sed -i` , on applique la commande directement sur le fichier en entrée .

> Il existe 2 manière de passer un script à `sed`
> * "directe": `sed -e` on sépare les commandes avec des ";". C'est la façon _uniligne_.
> * "via fichier externe": `sed -f <script_file>` Cela permet i,e meilleure lisibilité et la possibilité de réutiliser ces scripts.

## LECTURE SIMPLE
Il est possible de lire le contenu d'un fichier avec **sed**

	`sed '' <nomFichier>`
> Cela revoit le contenu du fichier "nomFichier".

_REMARQUE_: L'option `-e` permet de passer plusieurs commandes à la suite.

	`sed -e '4d; 7d' <nomFichier>`
> Dans ce cas on va supprimer (voir chapitre suivant pour m'option "d") les lignes 4 et 7 d'un fichier.

## SUPPRESSION

### Supprimer 1 ligne selon son numéro
> Il faut utiliser l'option `d` de delete.

_exemple_:

	`sed '1d;4d' <nomFichier>`
> Supprimera les lignes 1 **et** 4 dans le fichier "nomFichier".
> Le ";" fait office de séparateur.

	`sed '1,4d' <nomFichier>`
> Supprimera les lignes 1 **à** 4 du fichier "nomFichier"

###  Supprimer une ligne selon une REGEX
> On utilise aussi l'option `d` .

_exemple_:

	`sed '/^#/d' <nomFichier>`
> le "/" sert de délimiteurs.
> Ici on supprime TOUTES les lignes commençants par "#"

En utilisant des intervalles:

	`sed '/^#/d;/_/d' <nomFichier>`
> On supprime TOUTES les lignes entre celle qui commence par 1 "#" et celle qui contient 1 "_" .

	`sed '1d;/^#/d' <nomFichier>`
> On supprime la 1ere ligne ET TOUTES les lignes commançant par 1 "#"


## FILTRAGE

Il s'agit d'exactement l'inverse que précédemment. On choisit de ne rien afficher par défaut.
Pour cela on utilise l'option `-n` avec la commande `p` de **print**.

	`sed -n '/^#/p' <nomFichier>`
> Ici on n'affiche que les lignes commençant par un "#".

On peut choisir d'afficher 1 ligne OU 1 intervalle.

	`sed -n 5p hexdump`
> juste la ligne 5

	`sed -n 5,10p`
> des lignes 5 à 10

## SUBSTITUTION

Cela permet d'apporter des modifications.
On utilise l'option `-r` pour permettre une syntaxe étendue et la commande `s` de substitution.

	`sed 's/George Dupond/Georges Dupont/' <nomFichier>`
> Remplace "George Dupond" par "Georges Dupont" dans tout le fichier.

	`sed -re 's/^# *//' <nomFichier>`
> Dé-commente les lignes commentées (commençant par une dièse), et supprime les espaces en début de ligne (le * est un métacaractère signifiant 0 ou plus)

## TRANSLITERATION

Il s'agit d'un changement de lettre.
On utilise la commande `y` .

_exemple_: on a un problème avec des accents, on veut tous les enlever

	`sed 'y/éèêë/eeee/' <nomFichier>`
> Remplace tous les accents avec la lettre "e" par des "e" sans accents.

## COMMANDES GROUPEES

On peut appliquer plusieurs commandes à la fois en ajoutant les "**{}**".

	# remplacer tous les "e" accentués par des "e"  
	# et réécrire "Dupond" avec un "t" aux lignes 3 et 4
	`sed '2,6 {y/éèê/eee/;s/Dupond/Dupont/g}' fichiersource.txt`

	# même chose mais sur les lignes qui commencent par "#"
	`sed '/^#/ {y/éèê/eee/;s/Dupond/Dupont/g}' fichiersource.txt`


## RENOMMER DES FICHIERS

Vous voulez changer des caractères pas d'autres dans toute une série de fichiers.

	`$ for f in *.avi; do fn=`echo $f|sed 's/-/_/g'`; mv "$f" "$fn"; done`
> -   `for f in *.avi;`  on créé une variable  `f`, qui prend en paramètre le nom de tous les fichiers qui se terminent par _.avi_
 >   -   ``do fn=`echo $f|sed 's/-/_/g'`;``  on créer une nouvelle variable  `fn`, qui prendra comme valeur le résultat de la sous-commande  `echo $f|sed ‘s/-/_/g’`. Les expressions entre  _backquotes_  (`) sont substituées par leur résultat en bash.
>-   `echo $f|sed ‘s/-/_/g’`  on envoie à sed le contenu de  `$f`  en combinant  `echo`  et le pipe (`|`) afin qu’il le mette au bon format.
>-   `mv $f $fn;`  enfin, on modifie le titre avec la commande  `mv`  : change  `$f`  par  `$fn`.
>-   `done`, on indique que la commande est terminée.

## MODIFIER DU TEXTE DANS PLUSIEURS FICHIERS A LA FOIS

	# exemple avec des fichiers php, on admet être dans le bon répertoire
	`$ for f in *.php; do sed -i -e 's/ancienne-adresse/nouvelle-adresse/' "$f"; done`

> option `-i` modifie directement le fichier. Ce qui est intéressant avec cette option est qu'il est possible de conserver une sauvegarde du fichier avant modification. Pour cela il suffit de mettre un "suffixe" à l'option `-i` .

_exemple_:

	`for f in *.php; do sed -i.bak -e 's/ancienne-adresse/nouvelle-adresse/'  "$f"; done`
> Ici, le suffixe est ".bak". Les fichiers modifiés seront ".php" et les "initiaux" seront ".php.bak" par exemple.
> On peut aussi faire une redirection avec `>` .

	`$ for f in *.php; do sed 's/ancienne-adresse/nouvelle-adresse/' "$f" > new/"$f"; done`
> Tous les nouveaux fichiers modifiés seront dans le dossier "new".
