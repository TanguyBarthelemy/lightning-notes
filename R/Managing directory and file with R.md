En R il est possible de manipuler des fichiers et des dossiers avec certaines fonctions :

## Lister les fichiers et objets

La fonction `list.files()` permet de lister l'ensemble des fichiers et dossiers présents à un chemin. 

Exemple :

``` r
list.files("N:/L120/SECTION MTS/Admin_Section/")
#> [1] "Concours_Admin_2024.txt"              
#> [2] "Configuration_environement_travail.md"
#> [3] "Demande ordi blanc.pdf"               
#> [4] "Missions.txt"                         
#> [5] "Working-Session-NBB- Agenda.docx"     
#> [6] "youtube.txt"
```

La fonction `dir()` fait la même chose.

La fonction `list.dirs()` fait la même chose mais uniquement pour les dossier. mais retourne le chemin absolu des objets :

``` r
list.dirs("N:/L120/SECTION MTS/Formations/ESTP/", recursive = TRUE)
#> [1] "N:/L120/SECTION MTS/Formations/ESTP/"                                  
#> [2] "N:/L120/SECTION MTS/Formations/ESTP/Beginner_Session Janvier_2022"     
#> [3] "N:/L120/SECTION MTS/Formations/ESTP/Beginner_Session Janvier_2022/Day1"
#> [4] "N:/L120/SECTION MTS/Formations/ESTP/Beginner_Session Janvier_2022/Day2"
#> [5] "N:/L120/SECTION MTS/Formations/ESTP/Beginner_Session Janvier_2022/Day3"
```

## Création

### Créer un fichier ou dossier vide

Les fonctions `file.create()` et `dir.create()` permettent de créer des fichiers et des dossiers.
Il suffit de renseigner le nom du fichier/dossier à créer et R s'en charge.

> [!INFO]  
> Pour créer un **fichier** quelque part, il faut s'assurer que le chemin menant au fichier (arborescence des dossiers) existe.

Par exemple, 

> [!EXAMPLE]  
> La commande `file.create("mydir/file.csv")` échouera si le dossier `mydir` n'existe pas.

De la même manière, `dir.create("mydir/otherdir")` échouera si le dossier `mydir` n'existe pas. Mais il est quand même possible de créer un chemin de dossier en une seule commande en utilisant l'argument `recursive = TRUE`.

Exemple :

``` r
file.create("file.csv")
#> [1] TRUE
dir.create("otherdir")
file.create("mydir/file.csv")
#> Warning in file.create("mydir/file.csv"): cannot create file 'mydir/file.csv',
#> reason 'No such file or directory'
#> [1] FALSE
dir.create("mydir/otherdir")
#> Warning in dir.create("mydir/otherdir"): cannot create dir 'mydir\otherdir',
#> reason 'No such file or directory'
dir.create("mydir/otherdir", recursive = TRUE)
file.create("mydir/file.csv")
#> [1] TRUE
```

### Copie

La fonction `file.copy()` permet de copier **un fichier** d'un emplacement à un autre. 

> [!INFO]  
> Comme pour `file.create()`, pour déplacer un **fichier** quelque part, il faut s'assurer que le chemin menant à la nouvelle destination (arborescence des dossiers) existe.

Avec l'argument `recursive = TRUE`, il est possible de **copier des dossiers**.

## Récupérer des infos

### Tester l'existence de fichiers ou dossiers

Les fonctions `file.exists()` et `dir.exists()` permettent de tester si un chemin menant à un fichier ou un dossier existe. D'après l'exemple précédent, 

Exemple

``` r
file.exists("file.csv")
#> [1] TRUE
file.exists("file2.csv")
#> [1] FALSE
file.exists("mydir/file.csv")
#> [1] TRUE

dir.exists("mydir/")
#> [1] TRUE
dir.exists("mydir/otherdir")
#> [1] TRUE
dir.exists("mydir2")
#> [1] FALSE
```

### Informations générales sur les fichiers

La fonction `file.info()` et `file.size()` permettent de récupérer des meta-informations sur le fichier (date de création, date de modification, taille...).

Exemple : 

``` r
file.info("~/.Renviron")
#>             size isdir mode               mtime               ctime
#> ~/.Renviron  593 FALSE  666 2024-07-24 14:48:55 2021-11-29 13:31:35
#>                           atime exe
#> ~/.Renviron 2024-10-24 17:42:03  no
file.size("~/.Rprofile")
#> [1] 1567
```

## Modification
### Renommage
La fonction `file.rename()` permet de renommer un fichier ou un dossier.

Exemple : 

``` r
file.rename("~/mydir/", "~/mydir2/")
#> [1] TRUE
file.rename("~/file.csv", "~/file2.csv")
#> [1] TRUE
```

### Suppression

La fonction `file.remove()` permet de supprimer un fichier.

Exemple : 

``` r
file.remove("~/file2.csv")
#> [1] TRUE
file.remove("~/mydir2/file.csv")
#> [1] TRUE
```

> [!warning]  
> La fonction `file.remove()` ne permet pas de supprimer **les dossiers**. 

Pour supprimer un dossier, il est possible d'utiliser la fonction `unlink()` avec l'option `recursive = TRUE` :

Exemple : 

``` r
path_dir <- normalizePath("~/mydir/")
dir.exists(path_dir)
#> [1] TRUE
unlink(path_dir, recursive = TRUE)
dir.exists(path_dir)
#> [1] FALSE
```

### Autres fonctions de manipulation des fichiers et dossiers

Il existe d'autres fonctions de manipulations de ces objets mais moins utilisés et pour des cas plus particuliers.

#### Ouvrir un fichier ou un dossier
Il est aussi possible d'ouvrir un fichier avec l'application par défault (et donc pas forcément RStudio). On peut utiliser les fonctions `utils::browseURL()` ou `file.show()`.

> [!warning]  
> La fonction `file.show()` **ne permet pas** d'ouvrir de dossiers alors que la fonction  `utils::browseURL()` le **permet**.

#### Ouvrir et éditer un fichier dans RStudio
La fonction `file.edit()` permet d'ouvrir et éditer un fichier dans RStudio.

## Manipulation des chemins

Un étape importante dans la manipulation des fichiers et des dossiers est là manipulation des chemins (`PATH` in english).

### Choisir un fichier
La fonction `file.choose()`  ouvre une fenêtre windows pour choisir **un fichier** dont on veut récupérer le chemin.

```r
path <- file.choose()
```

### Construire un chemin

> [!question]  
> Pour créer un chemin sous R, il est possible de le faire à la main. Il ne faut pas se tromper car sous Windows, les chemins s'écrivent `chemin\vers\le\fichier` donc sous R il faut écrire `chemin/vers/le/fichier` ou `chemin\\vers\\le\\fichier`.  Aussi on peut se servir de `paste0()` si l'un de nos dossiers est sous la forme d'une variable. Mais est-ce valable sous d'autres environnements...

Le moyen le plus sûr est d'utiliser la fonction `file.path()`. Cette fonction permet aussi d'utiliser des variables comme nom de fichier ou dossier.

Exemple : 

```r
path_fichier <- file.path("chemin", "vers", "le", "fichier")
nom_WS <- "WS_industrie"
nom_SAP <- "Industrie_covid"
path_demetra_m <- file.path(nom_WS, "Output", nom_SAP, "demetra_m.csv")
```

> [!info]  
> Il existe d'autres packages pour la création de chemin. le package [**{fs}**](https://github.com/r-lib/fs) semble être une trés bonne alternative (même si je n'ai pas tout testé).
### Normaliser les chemins

Lorsqu'on utilise un chemin dans un code, une fonction, il est important de s'assurer qu'il est valide mais aussi qu'il soit normalisé. Pour cela, on utilise la fonction `normalizePath()`. L'argument `mustWork = TRUE` indique que la fonction retourne une erreur si le dossier ou le fichier n'existe pas.

Exemple : 

``` r
normalizePath("N:\\L120\\SECTION MTS/New_dir/New_file")
#> Warning in normalizePath(path.expand(path), winslash, mustWork):
#> path[1]="N:\L120\SECTION MTS/New_dir/New_file": Le chemin d’accès spécifié est
#> introuvable
#> [1] "N:\\L120\\SECTION MTS\\New_dir\\New_file"
normalizePath("N:\\L120\\SECTION MTS/New_dir/New_file", mustWork = TRUE)
#> Error in normalizePath(path.expand(path), winslash, mustWork): path[1]="N:\L120\SECTION MTS/New_dir/New_file": Le chemin d’accès spécifié est introuvable
```

La fonction `tools::file_path_as_absolute()` propose aussi une normalisation (avec séparateur winslash `/`) mais le fichier ou dossier **doit exister** sinon la fonction retourne une erreur.

### Générer chemin de dossiers et fichiers temporaires

Il est possible avec les fonctions `tempfile()` et `tempdir()` de générer des chemins (disponibles) pour des fichiers et dossiers temporaires (c'est-à-dire qui seront supprimés automatiquement par Windows régulièrement).

La fonction `tempfile()` retourne le chemin d'un fichier dont le nom commence par une chaine de caractère (fournie par l'utilisateur) et le reste est un identifiant unique.

Exemple :

``` r
tempfile("file_temp")
#> [1] "C:\\Users\\UTZK0M\\AppData\\Local\\Temp\\RtmpU79Iqu\\file_temp2ddc347f1547"
```

> [!warning]  
> Les fonctions `tempfile()` et `tempdir()` ne crééent pas de fichiers ni de dossiers.

> [!note] 
> La fonction `tempfile()` (contrairement à la fonction `file.create()`) autorise une architecture (temporaire) avec des dossiers.

Exemple : 

``` r
tempfile("dir1/dir2/file_temp")
#> [1] "C:\\Users\\UTZK0M\\AppData\\Local\\Temp\\Rtmp4kkuBn\\dir1/dir2/file_temp303c75354ba4"
```

La fonction `tempdir()` retourne le chemin vers le dossier temporaire (relatif à la session de R).

### Manipuler les noms

Une fois qu'on a un chemin menant à un fichier, il peut être intéressant de récupérer :

- le nom du fichier avec `basename()`
- le nom du dossier parent `dirname()`
- l'extension du fichier `tools::file_ext()`
- le chemin du fichier mais sans l'extension du fichier `tools::file_path_sans_ext()`

``` r
path_csv <- "./mydir/file.csv"
basename(path_csv)
#> [1] "file.csv"
dirname(path_csv)
#> [1] "./mydir"
tools::file_ext(path_csv)
#> [1] "csv"
tools::file_path_sans_ext(path_csv)
#> [1] "./mydir/file"
```
