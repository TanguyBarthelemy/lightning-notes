Lorsqu'on charge un package, généralement, on utilise la fonction `library()`. Mais pour des packages utilisant des dépendances ou des initialisations particulières, il faut rentrer dans les détails de l'initialisation.

## "Attaching" and "Loading"

La différence entre "Attaching" (attaché ou indexé) et "Loading" (chargé) est décrite ici :https://r-pkgs.org/dependencies-mindset-background.html#sec-dependencies-attach-vs-load.

Ce qu'il faut retenir, c'est que :

- "Loading" prépare le package en mémoire (toutes les classes, les fonctions, les data...)
- "Attaching" indexe le package et ses fonctionnalités à l'environnement R

## Trigger Event

Quand est ce qu'un package est "Attached" ou "Loaded" ?

### "Loading"

Voilà différents cas de figure qui charge le package en mémoire :

- dès que le package est "attached"
- `devtools::load_all()`
- `mon_package::ma_fonction()`
- `loadNamespace("mon_package")`
- `requireNamespace("mon_package")`

### "Attaching"

Voilà différents cas de figure qui indexe le package dans l'environnement :

- `library("mon_package")`
- `require("mon_package")`

## Utilisation

Mais quand faut attacher un package ou juste le charger ?

Du point de vue développeur, un package dépendance ne doit jamais être attaché mais uniquement chargé.

## Que mettre dans le `.onAttach` et le `.onLoad` ?

[Ref R Packages](https://r-pkgs.org/code.html#sec-code-onLoad-onAttach)
Étant donné que l'utilisateur peut utiliser notre package à partir de `library("mon_package")` mais aussi à partir de `mon_package::ma_fonction()`, il faut que les informations minimales à l'utilisation du package soient dans le `.onLoad()`.

### `.onLoad`

Le `.onLoad`  doit contenir :
- La préparation des options (si il y en a)
- le chargement des jars (si dépendance Java)
- le chargement des proto (si il y en a)

### `.onAttach`

Dans le .onAttach, on va plutôt mettre des messages informatifs à l'utilisateur par rapport à son environnement.

### Et Java

Sachant qu'un utilisateur de R pourrait appeler un package sans l'attacher avec `::` (exemple : `rjd3x13::x13(AirPassengers)`), ne faudrait-il pas mettre la condition sur la version de Java (`Java >= 21`) dans le `.onLoad()` et non le `.onAttach()` et de mettre une erreur ?

1) Non pour l'erreur car sinon, un utilisateur ne pourrait pas installer le package sans avoir `Java >= 21` et ce n'est pas ce que l'on veut. On veut que l'utilisateur puisse quand même installer le package et si besoin mettre à jour son Java a posteriori. Aussi cela empêcherai les checks du CRAN.
2) Non pour le `.onLoad()` car (comme mentionné [ici](https://r-pkgs.org/code.html#sec-code-onLoad-onAttach)), le but du `.onLoad()` n'est pas d'être verbeux. Si qqch en fonctionne pas, l'utilisateur est prévenu dans le `.onAttach()` avec le `packageStartupMessage()` et se rappeler que `::` est une forme avancé et pas forcément pour les débutants.

## Dépendances

La page du livre R packages est bien développée à ce sujet : https://r-pkgs.org/dependencies-mindset-background.html

### Imports vs Depends vs Suggests vs LinkingTo vs Enhances

Il existe 4 moyen de déclarer des dépendances en R. 

- **Depends** : liste les dépendances qui **seront attachées**. A priori, il n'y a **aucune raison** de mettre des packages dans cette section (on privilégiera la section Imports).
- **Imports** : liste les dépendances dont l'utilisateur a besoin pour faire fonctionner le package. Ces packages **sont installés et chargés** ("Loading") en même temps que le package principal.
- **Suggests** : liste les dépendances dont le développeur a besoin pour développer ou utiliser des fonctionnalités avancées du packages (tests, vignettes). Ces packages **ne sont pas** automatiquement installé en même temps que le package principal.
- **LinkingTo** : Pour le code C (inutile ici) 
- **Enhances** : Concept compliqué. Pas utilisé ici.

### Dans le zzz.R

Par défault, les packages dépendances (dans la section **Imports** du fichier DESCRIPTION) sont chargés ("Loading") lors du chargement du package principal. Il n'y a donc pas besoin de recharger à la main un package dépendance. Si une dépendance est manquante R le signifiera à l'utilisateur avec  le message :

```
Error in loadNamespace(i, c(lib.loc, .libPaths()), versionCheck = vI[[i]]) : 
  there is no package called 'rjd3toolkit'
```

## Schéma rjdverse

Pour le package {rjd3x13}, voilà un extrait du fichier DESCRIPTION :

```
Depends:
    R (>= 4.1.0)
Imports:
    rJava (>= 1.0-6),
    RProtoBuf (>= 0.4.20),
    rjd3toolkit (>= 3.7.1)
```

On liste ici les packages dont on aura besoin (et qui seront installés) et la version de R minimale. 

Dans le fichier zzz.R, on retrouve :

- Un `.onAttach()`avec le `packageStartupMessage()` sur la version minimale de Java:

```r
#' @importFrom rjd3toolkit get_java_version minimal_java_version
.onAttach <- function(libname, pkgname) {
    current_java_version <- rjd3toolkit::get_java_version()
    if (current_java_version < rjd3toolkit::minimal_java_version) {
        packageStartupMessage(sprintf("Your java version is %s. %s or higher is needed.",
                                      current_java_version, rjd3toolkit::minimal_java_version))
    }
}
```

- un `.onLoad()` avec le chargement des Jars, un chargement de {rjd3toolkit} si la version de Java le permet, le chargement du ProtoBuf et quelques options : 

```r
#' @importFrom stats is.ts start
#' @importFrom RProtoBuf read readProtoFiles2
#' @importFrom rJava .jpackage .jcall .jnull is.jnull .jfield .jaddClassPath
#' @importFrom rjd3toolkit get_java_version minimal_java_version
.onLoad <- function(libname, pkgname) {
    jar_dir <- file.path(libname, pkgname, "inst", "java")
    jars <- list.files(jar_dir, pattern = "\\.jar$", full.names = TRUE, all.files = TRUE)
    rJava::.jaddClassPath(jars)
    result <- rJava::.jpackage(pkgname, lib.loc = libname)
    if (!result) stop("Loading java packages failed", call. = FALSE)

    current_java_version <- rjd3toolkit::get_java_version()
    if (current_java_version >= rjd3toolkit::minimal_java_version) {
        rjd3toolkit::reload_dictionaries()
    }

    proto.dir <- system.file("proto", package = pkgname)
    RProtoBuf::readProtoFiles2(protoPath = proto.dir)

    if (is.null(getOption("summary_info"))) {
        options(summary_info = TRUE)
    }
    if (is.null(getOption("thresholds_pval"))) {
        options(thresholds_pval = c(Severe = 0.001, Bad = 0.01, Uncertain = 0.05, Good = Inf))
    }
}
```
