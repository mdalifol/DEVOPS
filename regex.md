# REGEX

Les **expressions régulières** sont partout dès lors qu'on veuille se faciliter la vie. C'es très puissant.

[Site d'entrainement au regex](https://regex101.com/)
[Site pour les experts regex](https://regexper.com/)

## CONSTRUCTION D'UNE REGEX

Une **regex** est **faite pour effectuer des recherches dans les chaînes de caractères**… et une regex est elle-même une chaîne de caractère.
Elle possède un **délimiteur** qui en indique le début et la fin ainsi que des **caractères spéciaux**. Les caractères spéciaux permettent d’indiquer des comportements. prédéfinis.
Ces comportements sont par exemple la longueur d'un mot, un mot optionnel...

_REMARQUE_: Il existe des variantes aux RegEx parce qu'elles ne fonctionnent pas pour tous les langages et toutes les plateformes de la même manière.
**PCRE** est la syntaxe la plus largement utilisée aujourd'hui.

## DELIMITEURS

Ils servent à délimiter ce qui fait parti de votre expression, de ce qui n’en fait pas parti.

	#, %, /, ...
C'est en dehors de ceux-ci que les options seront définies pour la recherche.

### Les **métas-caractères**

|Signe|Signification|Exemple|
|:---|:---:|---:|
|^|début de chaîne|/^music/ -> commence par "music"|
|$|fin de chaîne|/^music$/ -> commence ET termine par "music"|
|\||OU| /music \| musique/ -> "music" OU "musique"|
|.| TOUS les caractères SAUF le retour chariot \n; dans ce cas utiliser l’option s| /./|
|\\|échappement| /\\?/ -> ici c'est bien "?" qu'on cherche|

_REMARQUE_: tous les métas-caractères doivent être échappés


### **Quantificateurs**

Ils permettent de préciser le nombre de fois que l’on autorise un caractère ou une suite de caractères à se répéter.

|Signe|Signification|Exemple|
|:---|:---:|---:|
|?|0 ou 1 fois| /bue?no/ -> buno, bueno|
|+|1 ou plus| /bue+no/ -> buno, bueno, bueeeeno, ...|
|()|permet d'appliquer la répétition sur plusieurs signes|/Ay(AY)*/ -> Ay, AyAy, AyAyAyAy...|
|{}|précise le nombre de fois qu'il y a répétition| /Ay(Ay){3}/ -> AyAyAyAy|

_REMARQUE_:
* `{0,1}` revient à utiliser `?`
* `{1,}` revient à utiliser `+`
* `{0,}` revient à utiliser `*`


### **Classes** et **intervalles**

> Les **classes** permettent de recherche entre plusieurs caractères différents.
> Les **intervales** sont des classes un peu spéciales puisqu’elles permettent d’énumérer une certaine palette de chiffre ou de lettres.

|Signe|Signification|Exemple|
|:---|:---:|---:|
|[]| classes de caractères | /gr[oai]s/ -> gros, gras, gris |
|[-]| intervalle de classe | /n°[ 0-9]/ -> n°1, n°2, ..., n°9|
|[^ ]| classe à exclure | /h[ ^3-9]/ -> h1, h2 seulement|

_REMARQUE_: `[0-9a-z?+*{}.]` correspond à 1 chiffre, 1 lettre minuscule, 1 point d'interrogation, 1 point, 1 plus.....
Dans ce cas les métas-caractères ne comptent pas dans les classes. On ne les échappe pas.

### Classes **Abrégées**

Elles n’apportent rien de plus en fonctionnalité que les classes normales, mais elles permettent d’écrire tout ça bien plus vite, ce sont des raccourcis.

|Signe|Signification|
|:---|---:|
|\\d| Indique un chiffre. <=> [0-9] |
|\\D| Indique PAS un chiffre <=> [^0-9] |
|\\w| indique 1 caractère alphanumérique OU un underscore <=> [a-zA-Z0-9_] |
|\\W| indique PAS alaphanumérique ou underscore <=> [^a-zA-Z0-9_] |
|\\t| tabulation |
|\\n| nouvelle ligne |
|\\r| retour chariot |
|\\s| espace blanc <=> \\t, \\n, \\r |
|\\S| PAS espace blanc |
|.| n'importe quel caractère |


### Lookahead et lookbehind

Sont des types de références un peu spéciales. Elles permettent de matcher un élément en fonction de son contexte.

> **lookahead** veut dire que l’on regarde en avant, donc on pourra sélectionner un élément en fonction de ce qu’il y a, ou n’y a pas, après lui.
> **lookbehind** fera de même à l'inverse.

_exemple lookahead_:
```javascript
	const birthQuent = 'Je suis Quentin et je suis né en 1990 à Lyon';
	const birdtRoger = 'Je suis Roger et je suis né en 1978 à Paris';

	const regex = /en ([0-9]{4}) (?=à Lyon)/;

	console.log(regex.exec(birthQuent)); // ["en 1990 ", "1990"]
	console.log(regex.exec(birdtRoger)); // null
```

> Ici on recherche donc une année qui a 4 chiffres (année complète) seulement si elle est suivie de "à Lyon". Il n'y a donc que "Quentin" qui ressort.

_exemple lookbehind_:
```javascript
	const alex = 'Codename 006 – Alec Trevelyan';
	const james = 'Codename 007 – James Bond';

	const regex = /(?<=007 – )([A-Z][a-z]+ [A-Z][a-z]+)/;
```

> Ici on recherche tous les caractères alphabétiques si "007 -" est présent avant



## OPTIONS

Suivant les langages, d'autres options pour ces regex existent. Pour cela voir directement les docs de ces langages.
