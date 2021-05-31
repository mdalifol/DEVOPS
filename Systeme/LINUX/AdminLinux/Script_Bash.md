# SCRIPT bash

## Connection


## Présentation du shell

La commande `ps -l`permet de connaitre dans quel shell on se trouve.

La commande `set -o` permet d'afficher les parametres du shell présent sur la machine.
Pour revenir en arrière, il suffit de faire `set+o`

Pour modifier un parametre on écrit `set -o <nomParametre>`

Par exemple, le parametre `noclobber` permet de "NE PAS" écraser le contenu d'un fichier avec un `>`.
On peut faire de même si on ne veut pas garder d'historique, on met le parametre `history`à "off".

_Remarque_: lorsque le parametre noclobber est "on", le seul moyen de modifier le fichier (avec un ">") est de forcer l'action avec `>|`.


### Alias

Pour lister les alias on a la commande `alias`
Pour créer un alias on fait
  `alias <raccourcis'>='<commande à faire>`
Pour supprimer un alias:
  `unalias <nomAlias>`

_ATTENTION_: tous ces alias sont dans le shell (dans lequel on se trouve), les informations ne sont pas transmises.
_Remarque_: si un alias a le même nom qu'un commande, par defaut l'alias sera exécuté. Pour exécuter la commande il faut la précéder d'un "\". `\<commande>`.

Comment savoir si une commande est un alias ou non:
`type <nomCommande>` renvoie le type de la commande.



## Execution du script

Il faut noter `./<nomScript>` ou `<nomScript>`, dans le 2e cas cela veut dire que la variable PATH a été mise à jour.
Pour mettre à jour cette variable il faut faire:
  `PATH=$PATH: <nomRepertoireAAjouter>` (ex: "." est le répertoire courant)

Pour exécuter un script dans le shell courant il faut noter `. ./<nomScipt>`, cela permet de conserver les variables créées pour le script.
On peut de la même manière faire appel à un autre script dans mon script.
ex: dans mon script on appelle le script "toto" on le note `. ./<chemindetoto>`


### SheBang
 `#!/bin/bash` se met au tout début du script.
 Si cette ligne n'apparait pas, le script sera lancé dans le shell "courant".


## Structure d'un script

1. SheBang
2. Commentaires de présentation
3. Les variables modifiables (en fonction du serveur ou service ou société)
4. Les variables non modifiables (utilisées dans l'usage interne du script)
5. La déclaration des fonctions
6. Les tests de cohérence (vérifier que "rien" n'empêche l'exécution du script, par exemple que toutes variables existent)
7. Le programme pincipal


Comment nommer des variables utilisables: connaitre les commandes linux:
`basename /rep/rep2/toto` affiche "toto"
Dans le script on pourra appeler cette variable avec `$(basename $0)`


## Debug d'un script

On peut débugger de différentes manières:
- on peut même le flag (avec le set -o xtrace)
- on peut utiliser `bash -x <nomScript>` ou `bash -v <nomScript>` mais "-v" est beaucoup plus verbeux (même les commentaires s'affichent).
Avec le "-x" les lignes qui commencent par "+" sont celles où des commandes sont appliquées

Mais avec ces commandes, le debug se fait sur le script en entier.
Pour debugger seulement une partie de script on utilse `set +x` dans le script directement. On met `set -x` juste avant la ligne où on veut commencer le debug et `set +x` après la dernière ligne à tester.
## Gestion des logs

Il faut utiliser la commande `logger`, et le gestionnaire des logs va gérer tout ça.

## Rappel sur les commandes

* `echo`                    affiche à l'écran ce qui est écrit
      `echo -e <xxx>`       l'option "-e" est utile pour les \n, \t, .... comme cela ces options sont interprétées, sans "-e" c'est affiché
* `env`                     liste toutes les variables d'environnements
* `head -<numLignes>`       afiiche les n 1eres lignes
* `tail -<numLignes>`       affiche les n dernières lignes
* `wc <option> <fic1>`      afiiche les infos de fichier 1
      option:   -l          nb de lignes
                -w          nb de mots
                -c          nb d'octets
* `file`                    affiche le type du contenu du fichier
* `string <nomFichier>`     affiche les caractères imprimables d'un fichier
* `cmp <fichier1> <fichier2>` compare 2 fichiers (dit s'ils sont identiques ou non)
* `diff <fic1> <fic2>`        indique les différences entre les fichiers
* `paste <fic1> <fic2>`       fusionne les 2 fichiers ligne par ligne (ligne 1 du fic1, ligne2 du fic2....)
* `cut <options>`             coupe les parties de fichiers
  * `cut -c 2-5 <fic1>`       coupe les caractères 2 à 5 de chaque ligne du fichier 1
  * `cut -c -3 <fic1>`        coupe les 3 1er caractères
  * `cut -c 3- <fic1>`        coupe à partir du 3e caractère
  * `cut -d<caractere> -f <numChamp> <nomFichier>`
                -d <caractere> coupe à partir de ce caractère (ex: -d:  -> coupe à partir des ":")
                -f <numChamp>
                      `-f 1`    récupère le champ 1
                      `-f 1,4`  récupère les champs 1 et 4
                      `-f 1-4`  récupère les champs 1 à 4


* `awk`
        `awk -F: '{print $1 " est " $5}' <fic1>`  affiche le champ 1 et 5 de fichier 1 où le délimiteur de champ est ":"
            on réfléchit en colonne
        ex: je veux récupérer la date sur la commande who
            `who | awk {print $3}`


* `sort`

        `sort -n`
        `sort -r`         affiche dans l'ordre décroissant
        `sort -t: -k3nr fic1` affiche avec le délimiteur ":" (option "t") le champ (potion "k") 3 dans l'ordre décroissant du fichier fic1



* `uniq <fic1>`                    affiche les lignes sans doublons "CONSECUTIFS" dans le fichier

* `tr '<caracteresARemplacer>' '<caracteresRemplaçants>' `
        ex: `tr 'a-z' 'A-Z' < fic1`     remplace toutes les minuscules en majuscules du fichier fic1
        la commande tr ne se met pas en argument, il faut utiliser un pipe
        ex: `cat <nomFichier> | tr 'a-z' 'A-Z'`

        `cat /etc/passwd | sort | grep 'bash$' | nl | tee /tmp/bash_users | wc -l`
          fait:
          - cat /etc/passwd           affiche le contenu du fichier /etc/passwd
          - sort                      le range par ordre aplphabétique
          - grep 'bash$'              n'affiche que les lignes qui ne se termine par "bash"
          - nl                        affiche le numéro de la ligne du fichier
          - tee /tmp/bash_users       envoie le résultat de ces commandes dans /tmp/bash_users
          - wc -l                     affiche le nombre de lignes
          -> résultat final: le nb de lignes et dans le fichier /tmp/bash_users toutes ces lignes               


## LES METACARACTERES

Ce sont les caractères spéciaux au niveau du shell (pas dans les documents).



## ENTREES ET SORTIES STANDARD ET D'ERREURS

* Entrée standard: **stdin** représenté par **0**.
* Sortie standard: **stdout** représenté par **1**.
* Sortie erreur: **stderr** représenté par **2**.
* Sortie "poubelle": **/dev/null**


Pour une redirection de ces messages dans 1 fichier il faut:
  `<commande> >> fic1`  que la commande soit correcte ou non (si correcte, ce qui sera dans le fichier correspondra à "stdin", si la commande est fausse, cela correspondra ) "stderr")

* commande `tee`    le résultat qu'elle reçoit va à la commande suivante ET est stockée dans 1 fichier
    `<commande> | tee <nomFichierdeStockge> | <commande2>`



## LES VARIABLES


### Les codes retours

- `$?`      affiche le code retour (0:ok, !0=KO)
- `$$`      affiche le PID du programme lancé

_Remarque_: dans un script ce code retour "$?" peut être rensigné par le "exit <num>" que l'on définit.
Dans ce cas, il faudra donner une documentation avec la définition de ces codes retours.

ex: script bash qui se termine par "exit 4"
si on fait `echo $?` on a comme réponse "4"

### La concatenation

Pour concaténer 2 variables, il faut mettre les blocs ("{}") pour quelles soit prises en compte.
Si ces blocs (avec les accolades) ne sont pas notées, nous n'aurons pas le résultat attendu.
    ex: on veut afficher "AIME" à partir d'une variable HO="AI"
    -> `echo ${HO}ME`
    => affiche "AIME"


### Interactivté avec un SCRIPT

> La commande `read` intègre de l'interactivé.
Tout ce qui va être tapé au clavier va initialiser une variable "variable" qu'on peut définir.

_ATTENTION_: on peut générer 1 seul read pour plusieurs variables, mais dans ce cas, chque "espace" fera un changement de variable, et la dernière prendra "tout" ce qu'il reste de renseigné

**read** a aussi des options.
- `-p`      permet de récupérer l'info à la volée
- `-n`      permet de de limiter pour la récupération le nombre de caractères
      ex: `read -p "Entrez votre prenom: " -n 5 prenom`
          -> l'utilisateur rentre "Michel", seul sera conservé "Miche"
- `-t`
- `-s`      permet de "cacher" ce qui est renseigné (bien pour les mots de passe)



#### Passage d'arguments
Il faut être capable de récupérer les arguments. Pour cela des variables sont créées.

ex: `./script toto titi tata tutu`
  $0: ./script
  $1: toto
  $2: titi
  $3: tata
  $4: tutu
  $#: nb d'argument passés (ici 4)
  $*: liste des arguments du script (toto titi tata tutu)

Dans le script pour prendre en compte ces arguments, il faut les renseigner dans le code avec $1, $2, $n lorsqu'on a besoin de ces valeurs.

ex: `.recherche fic1 rep1`
avec comme script:
    `#!/bin/bash
    echo -e "\nResultat: "
    find "$2" -name "$1"
    `
    Affiche le resultat de la recherche puisque dans ce script nous faisons un "find"


## LES OPERATEURS ET LE CONDITIONS

Chaque commande renvoie un code retour (O: ok, !O: ko). Cette valeur est contenue dans la variable `$?`.

#### Opérateurs && et ||

  `<commande1> && <commande2>`    la commande2 est traitée SI la commande1 est exécutée ET qu'elle renvoie "0".
  `<commande1> || <commande2>`    la commande2 est traitée SI la commande1 est exécutée ET qu'elle renvoie un code différent de"0".


#### Commande 'test'

Cette commande permet de faire des tests, elle renvoie (0)"vrai" ou (!O)"faux".

>Pour les fichiers:

  `test -d /home/tux`   -> si ce fichier tux dans /home existe, cette commande renvoie 0
  `[ -d /home/tux ]`    -> est équivalent
  `[ ! -d /home/tux ]`  -> il répond "0" si le répertoire n'existe pas

Lorsqu'on utilise les "()" on peut regrouper des actions en les séparants par des ";"

>Pour les nombres:

On peut utiliser les [ ] ou (( ))

  `[ nb1 -eq nb2 ]`         `(( nb1 == nb2 ))`
  `[ nb1 -ne nb2 ]`         `(( nb1 != nb2 ))`
  `[ nb1 -lt nb2 ]`         `(( nb1 < nb2 ))`
  `[ nb1 -gt nb2 ]`         `(( nb1 > nb2 ))`
  `[ nb1 -le nb2 ]`         `(( nb1 <= nb2 ))`
  `[ nb1 -ge nb2 ]`         `(( nb1 >= nb2 ))`

>Pour les chaines de caractères

  `[ ch1 = ch2 ]`           ch1 est identique à ch2


>Pour les expressions

  `[ !exp ]`                l'expression "exp" est fausse
  `[ exp1 -a exp2 ]`        exp1 ET exp2 sont vraies
  `[ exp1 -o exp2 ]`        exp1 OU exp2 est vraie
_Remarque_: on peut aussi remplacer le "-a" ou le "-o" par des "&&" ou des "||"


#### Condition if, else, elif, case

##### IF


  `if ((  <condition1>  ))
  then
    <commande1 à faire si la condition1 est remplie>
  elif (( <condition2 ))
    <commande2 à faire si la condition2 est remplie ET que la condition1 n'est pas remplie>
  else
    <commande3 à faire lorsque les conditions précédentes ne sont pas remplies
  fi`

on peut aussi remplacer cette condition en 1 seule ligne:
    `if <condition1>; then <commande1>; elif <condition2>; then <commande2>; else <commande3>`

_Remarque_: dans le **if** on peut mettre une commande parce que nous allos dans ce cas exploiter la valeur du code retour **$?**.
  `if grep $user /etc/passwd; then echo $user existe; else echo $user n existe pas`
  -> on attend le code retour de la commande "grep $user /etc/passwd"

###### CASE

En fonction de la valeur d'une varaible on veut faire un traitement spécifique.

  `case variable in
    <valeur1>) <commande1> ;;
    <valeur2>) <commande2> ;;
    <valeur3>) <commande3> ;;
    <valeur4>) <commande4> ;;
    *) <commandeN> ;;
  esac`



## LES BOUCLES

#### Boucle for

`for <variable> in <listeDeVariables>
do
  <commande>
done  
`


commande `seq`: $(seq 10 30)      affiche une liste de 10 à 30
                $(seq 10 2 30)    affiche une liste tous les "2" de 10 à 30
equivalent:     {{ 10..30 }}      affiche une liste de 10 à 30
                {{ 10..20..2 }}   affiche une liste tous les "2" de 10 à 30
ex:
  `for i in $(seq 10 2 20); do echo "$i";done`
  -> 10 12 14 16 18 20

  `for var in {a..d}; do echo $var; done `
  -> a b c d

------------------------------------------------------------------------------------
ex: boucle for
on veut compter le nb de fichers et de repertoire dans le repertoire courant
[ -f $file ]    : indique le nb de fichers
[ -d $file ]    : indique le nb de repertoire (directory)
[ ! -d $file ]  : indique si le repertoire existe

`# VARIABLES
fic=0     # nb de fichiers
rep=0     # nb de repertoires

# boucle for
for el in $(ls)   # $(ls) est pour retourner le resultat de la commande "ls"
do
  if [ -f $el ]
    then (( fic++ ))
  elif [ -d $el ]
    then (( rep+ ))
  fi
done

echo "Il y a $fic fichiers et $rep repertoires dans le repertoire courant"
`



------------------------------------------------------------------------------------


#### boucle while
"tant que le test est vrai je continue": __PENSER__ à ajouter une incrementation pour ne pas passer en boucle infinie
`i=0
while (( i<3 ))
do
  echo $i
  i++
done`

_Remarque_: la commande `shift` permet de "supprimer" dans la liste des variables sur lesquelles agit la boucle supprime la 1ere valeur (décrémentation)



#### boucle until
"jusqu'à ce que le test soit vrai, je continue"



## OPERATIONS ARITHMETIQUES

Utiliser les " ((  )) " et mettre son calcul à l'interieur.

ex: 3*5=15
a=(( 3 * 5 ))
echo $a   -> 15


## LES CHAINES DE CARACTERES

Commande `expr`
Cette commande permets grace à des mots clés de récuperer des infos sur les string.

`var=formidable
expr lenght $var`   -> affiche 10 (10 caracteres dans "formidable")

`expr substr 5 3`   -> affiche "ida" car "i" est le 5e caractere et "ida" car pendant 3 caracteres

`expr `


## LES FONCTIONS

Pour appeler dans le script des fichiers qui contiennent des fonctions, il faut appeler ce fichier en début de script
`
. ./ <cheminDuFichierFonctions>

`


##
