# GIT

## GIT DEFINITION
> Git est un logiciel de gestion de versions décentralisé.

[Site officiel de git](https://git-scm.com/)
[Documentation Git](https://git-scm.com/docs)
[Procédure Bitbucket](https://www.atlassian.com/fr/git/tutorials/learn-git-with-bitbucket-cloud)


## INSTALLATION GIT

* On télécharge [Git sur son site officiel](https://git-scm.com/download) en fonction de l'OS où il sera à installer.
  * MacOS:
```bash
 brew install git
```

* On crée un **nouveau dépôt**:
    * On crée un nouveau dossier, on l'ouvre et on exécute la commande
```bash
 git init
```

* On clone un **dépôt existant**:
```bash
# On crée une copie du _dépôt local_ avec
 git clone /path/to/repository
# On crée une copie du _dépôt distant_ avec
 git clone username@host:/path/to/repository
```



## CONNEXION A GIT VIA TERMINAL

```bash
# Identity Name:  	
 git config --global user.name "<nomUserGit>"
# Identity Email:
 git config --global user.email "<emailUserGit>"
# Editor Tool:     	
 git config --global core.editor subl
# Diff Tool:       	
 git config --global merge.tool filemerge
```



## UTILISATION DE GIT

### Aide Git

> Permet d'obtenir les aides des commandes GIT

```bash
git help config
git help push
git help pull
git help branch
```


### Principales commandes git

```bash
# Status des fichiers:      
 git status
# Liste des branches**:       
 git branch -a
# Créer une branche**:
  # Deux lignes: créer et basculer sur la nouvelle branch
    git branch nom_de_ma_branch_nouvelle
    git checkout nom_de_ma_branch_nouvelle
  # Une seule ligne: créer et basculer
     git checkout -b nom_de_ma_branch_nouvelle
# Supprimer une branche:
  # Si la branch est local et n'est pas créée sur le repo distant
      git branch -d nom_de_ma_branch_local
  # Si la branch est présente sur le repo distant
      git push origin --delete nom_de_ma_branch_distante
# Changer de branche:       
 git checkout nom_de_ma_branch

# Premier commit:           
 git add .
 git commit -m "initial commit"
# Commit suivant:           
 git add chemin_vers_mon_fichier
 git commit -m "message du commit"
# Annulé dernier commit:    
 git reset --hard md5_commit
 git push --force
# Antidaté un commit:       
 git add .
 GIT_AUTHOR_DATE="2015-12-12 08:32 +100" git commit -m "Commit antidaté"

# Mettre à jour un dépot local:
 git pull
# Mettre à jour une branche sécifique d'un dépot local:
 git pull origin MA_BRANCH
# Envoyer les commits vers le dépot distant:
 git push
# Envoyer les commits d'une branche spécifique vers le dépot distant:
 git push origin MA_BRANCH

# Supprimer un fichier du répertoire de travail et de l'index:
 git rm nom_du_fichier
# Supprimer un fichier de l'index:
 git rmg --cached nom_du_fichier

# Annuler des commits:
> Seul le commit est retiré de Git ; vos fichiers, eux, restent modifiés. Vous pouvez alors à nouveau changer vos fichiers si besoin est et refaire un commit.

  # Annuler le dernier commit:            
    git reset HEAD
  # Annuler l'avant dernier commit:       
    git reset HEAD^
  # Annuler l'avant avant dernier commit:
    git reset HEAD~2
  # Annule un commit precis:              
    git reset d6d9892

> Si vous voulez annuler votre dernier commit et les changements effectués dans les fichiers, il faut faire un reset hard.

  # Annuler les commits et perdre tous les changements:   
    git reset --hard HEAD^
  # Annuler les modifications d’un fichier avant un commit: Si vous avez modifié plusieurs fichiers mais que vous n’avez pas encore envoyé le commit et que vous voulez restaurer un fichier tel qu’il était au dernier commit
    git checkout nom_du_fichier
    git restore nom_de_ma_branch
  # Annuler/Supprimer un fichier avant un commit: Supposer que vous venez d’ajouter un fichier à Git avec git add et que vous vous apprêtez à le « commiter ». Cependant, vous vous rendez compte que ce fichier est une mauvaise idée et vous voulez annuler votre git add
    git reset HEAD -- nom_du_fichier_a_supprimer

# Affiche la différence entre le contenu du dernier commit et celui du répertoire de travail:
 git diff HEAD
# Affiche la différence entre le contenu pointé par A et celui pointé par B:
 git diff A B
# Diff entre un dossier présent sur deux branches:                                  
 git diff master..MA_BRANCH chemin/vers/mon_dossier

# Modifier le message du dernier commit:
 git commit --amend

> La commande charge le dernier message dans l'éditeur; Modifier message; Enregistrer et quitter l'éditeur

# Ajouter des fichiers au dernier commit:
 git add mon_fichier
 git commit --amend

# Éditer l'historique avec rebase:
 git rebase -i HEAD~3
  > Historique des trois derniers commits
  Chaque commit est précédé du mot "pick", utiliser tous les mots-clés possibles.
  # Pour éditer, un commit changer "pick" par "edit" ;
  # Enregistrer et quitter l'éditeur afin de prendre les modifs en compte ;
  # Et suivre les instructions.

# Pour valider, les changements utiliser la commande:
 git rebase NOM_DE_MA_BRANCH
  # Dans le cas, ou les commits modifier sont déjà présent sur la branch distante, il faura "forcer"
    git push --force origin NOM_DE_MA_BRANCH

# Affiche les logs:           
 git log
# Affiche X derniers commits:
 git log -n X
# Affiche les commits concernant un dossier:
 git log --oneline -- chemin/vers/mon_dossier
# Affiche un ensemble de commits par date:
 git log --since=date --until=date

# Représentation de l’historique à partir de HEAD (commit / branch):
 git log --oneline --graph --decorate
# Représentation de l’historique à partir d'un fichier (commit / branch):
 git log --oneline --graph --decorate nom_du_fichier
# Créer une release:          
 git checkout -b release/aaaammyy_xx dev
```

## ALLER PLUS LOIN

### **Arbres**:
> Un dépôt local est composé de _3 arbres_ gérés par git.
        * _espace de travail_ qui contient **réellement** les fichiers
        * _index_ qui joue le rôle d'espace de transist pour les fichiers
        * _head_ qui pointe vers la dernière validation faite  

_REMARQUE_: Pour passer du _espace travail_ à _index_ on fait un `add`

_REMARQUE_: Pour passer de _index_ à _head_ on fait un `commit`

* Ajouter et valider:
     * on propose un changement (ajoute à l'index) avec
```bash
 git add <filename>
# ou  
 git add *
```
_REMARQUE_: C'est la 1ere étape du workflow git

	  * Pour valider ces changements, on utilise
```bash
 git commit -m "Message de validation"
```
_REMARQUE_: Le fichier est ajouté dans la partie _head_ MAIS pas encore dans le dépôt distant.

* Envoyer des changements sur le dépôt distant:
```bash
 git push origin master
```
_REMARQUE_: on remplace _master_ par le nom de la branche souhaitée

* Si le dépôt distant n'est pas cloné, alors il faut se connecter:
```bash
 git remote add origin <server>
```

* Ensuite on peut envoyer les changements

### **Branches**

> Les **branches** sont utilisées pour développer des fonctionnalités isolées des autres.
> La branche **master** est la branche par défaut.
> On utilise les autres branches pour le développement et on fusionne (avec un `merge`) sur la _master_ une fois le développement validé.

```bash
# Créer une nouvelle branche et l'utiliser:
	git checkout -b <nomNouvelleBranche>

# Retourner sur la branche master:
	git checkout master

# Supprimer la branche créée:
	git branch -d <nomNouvelleBranche>
```

_REMARQUE_: Une branche **n'est PAS disponible** pour les autres **tant qu'elle n'est pas envoyée sur le dépôt distant**: `git push origin <nomBrancheCréée>`


### Mettre à jour et Fusionner

* Mettre à jour le dépôt local :
```bash
 git pull
```

* Fusionner 2 branches entre elles (**_branche1 vers branche2_**):
```bash
 git merge <branche1> <branche2>
```
_REMARQUE_: il arrive (souvent) qu'il y ait des conflits. Il faut donc les résoudre avant de pourvoir faire le merge.
_REMARQUE_: par défaut, si on n'écrit que "branche1", la "branche2" sera la master.
```bash
 git add <elementAAjouter>
```
* Voir les changements effectués:
```bash
 git diff <source_branch> <target_branch>
```

## TAGS

Il est recommandé de créer des tags pour les releases:
```bash
 git tag <numVersionTag> <IDduTag>
```
_REMARQUE_: "IDduTag" les 10 premiers caractères de l'identifiant du changement que vous voulez référencer avec ce tag

Pour connaitre cet identifiant:
```bash
 git log
```

## REMPLACER LES CHANGEMENTS LOCAUX

Lorsqu'une erreur se produit, pour annuler les changements locaux on utilise la commande:
```bash
 git checkout --<filename>
```
_REMARQUE_: Cela remet dans l'_espace de travail_ le dernier contenu du _head_. Les changements déjà ajoutés à l'index, aussi bien les nouveaux fichiers, seront gardés.

Pour supprimer tous les changements et validations locaux:
```bash
 git fetch origin
 git reset --hard origin/master
```


## CONSEILS

Utiliser des couleurs pour les réponses de git (facilite la lecture):
```bash
 git config color.ui true
```

Utilise l'ajout interactif:
```bash
 git add -i
```

----------------------------------------------------------------------------------------------------------

## ORDRE DES COMMMANDES A UTILISER


1. se connecter à git
  entrer ses identifiants
```bash
git config --global user.name "mdalifol"
git config --global user.email "micheldalifol@gmail.com"
```

  vérifier les connexions
```bash
 git config --global --list
```

2. créer un repository dans GitHub: "Test1"

3. sur l ordinateur (en local)

```bash
mkdir ~/Test1
cd ~/Test1

echo "Test1" >> README.md
git init
git config --global init.defaultBranch main
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/mdalifol/Test1.git
```
4. le username (renseigné au début) et le mot de passe sont demandés pour la connexion

5. on pousse sur le dépot git distant (ici github), les fichiers qui ont été ajoutés avec git add
```bash
 git push -u origin main
```

6. voir ses répositories distant
```bash
 git remote -v
```

7. opérations suivantes: opérations de code et de versionning "classique"
