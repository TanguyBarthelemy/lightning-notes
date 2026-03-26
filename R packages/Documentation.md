Ce document regroupe quelques de convention de documentation qui permettront d'ajouter de la cohérence.

## `@examples` ou `@examplesIf`

- Les exemples doivent suivre les règles de linting.
- Toutes les fonctions exportées qui sont du niveau de l'utilisateur (pas fonctions internes) doivent avoir des exemples illustratifs.
- Si une fonction met plus de 5s à tourner et qu'il n'y a pas de démo plus simple (qui prend moins de temps) de la fonction, on peut utiliser la balise `\donttest`
- Les exemples ne doivent jamais utiliser `\dontrun` 
- Pour mettre en place un condition sur les exemples, il est possible d'utiliser le tag. Par exemple pour vérifier la version de Java, on utilise :
```r
#' @examplesIf rjd3toolkit::get_java_version() >= rjd3toolkit::minimal_java_version
```

## `@details`
## Vignettes
## `@param`

- La documentation des arguments doit inclure :
	- le type de l'objet (Booléen, integer, data.frame, ts object, numeric vector, character vector...)
	- une description claire de ce qu'il représente et de à quoi il sert
	- Une mention si l'argument est obligatoire, optionnel ou nécessaire dans tel ou tel condition (à compléter avec la section `@details`)
	- La valeur par défault (ou no default).
- La description des arguments doit toujours commencer par une MAJUSCULE et finir par un POINT `.`

### Exemples

Voilà quelques exemples illustratifs de documentation d'arguments :

```r
#' @param calendars A named list of `JD3_CALENDAR` objects to be written to an 
#'   XMl file. Mandatory with no default.
#' @param freq An integer specifying the annual frequency. 
#'   If set to `0`, only the daily series is computed. The default value is `12`.
#' @param jws A JD+ workspace object (Java pointer), as returned by
#'   [rjd3workspace::jws_open()] or [rjd3workspace::jws_new()].
#'   Mandatory with no default.
#' @param overwrite Boolean (optional argument). 
#'   Should the function overwrite an existing workspace?
#'   The default value is `FALSE`.
#' @param file String. The path to the csv file contaning the raw data to import.
#'   Mandatory with no default.
#' @param td A data.frame with two columns:
#'   \describe{
#'     \item{series}{Name of the series (column name if `series` is multivariate).}
#'     \item{reg_selected}{Name of the selected regressor set.}
#'   }
#' @param ... Unused parameters.
#' @param ... Optional additional arguments passed to [rjd3toolkit::add_outlier()]
#'   to add new outliers. Possible arguments include:
#'   \describe{
#'     \item{type}{Character vector. The type of the outliers ("AO", "LS" or "TC")}
#'     \item{date}{Character vector. The date of the outliers (in the format
#'     `"YYYY-MM-DD"`).}
#'   }
```

> [!Longues descriptions]
Si la documentation de l'argument est trop longue pour tenir sur une ligne, il faut penser à indenter la ligne suivante avec 2 espaces (matérialisés ici avec des points) :
> ```r
> #' @param threshold_seasonnality_test Longue description de cet argument très
> #' ••important et à quel point il faut bien le specifier.
> ```

### Vocabulaire

Certains termes sont à privilégier par rapport à d'autres:

- Boolean plutôt que logical
- Character vector => length > 1
- Character string => length = 1
- Vector => length > 1
- Scalar => length = 1
- Pour parler de variable numérique, il vaut mieux utiliser les termes :
	- integer (`1L`, `-4L`): nombres entiers
	- double (`pi`, `0`, `-11.1`): nombres réels

## `@returns`

Il me semble que le tag `@return` est superseded. Il vaut mieux utiliser le tag `@returns`. Ce tag est obligatoire dans la documentation d'une fonction et doit contenir une description de l'objet (type et strcture) retourné par la fonction.

## `@title` et `@description`

Ces tags sont optionnels mais je conseille de utiliser pour structurer la documentation et ajouter de la lisibilité.

## Regroupement

Il est possible de documenter plusieurs fonctions qui fonctionnent de manière similaire ou ensemble avec les tags `@name` et `@rdname`.

## S3 méthodes

Lorsqu'on déclare une méthode S3 `f` pour une nouvelle classe c1, on va créer une fonction `f.c1`.
Ne pas oublier d'ajouter les tags correspondants à la nouvelle méthode si on veut l'exporter et la documenter :

```r
#' @exportS3Method f c1
#' @method f c1
```

Aussi, ne pas oublier les tags `@export` si on veut exporter la méthode et `@rdname` pour documenter la méthode.

## Fonctions internes

Toutes les fonctions exportées ou non exportées, internes ou non doivent être documentées.

Pour toutes les fonctions internes, je recommande le tag :

```r
#' @keywords internal
```

Pour les fonctions non exportées, actuellement on pose le tag :

```r
#' @noRd
```

Mais on devrait tester le tag :

```r
#' @dev
```

Et utiliser le package [{devtag}](github.com/moodymudskipper/devtag).

## CHANGELOG

- Les packages R suivent la convention [Semantic Versioning](https://semver.org/) pour le numérotage des versions.
- Ils suivent aussi le format [Keep a Changelog](https://keepachangelog.com/).
- La GHA `check-changelog` s'occupe de vérifier que le CHANGELOG (NEWS.md) a la bonne forme.

Qqs règles supplémentaires de cohérence :

- Lorsqu'on faut une modification *substancielle* dans un package R, il convient de modifier le NEWS.md en conséquence
- Les listes à puce commencent par `*` puis une majuscule pour plus de lisibilité
- Lorsqu'on ferme une issue, il faut ajouter une ligne correspondante à l'issue fermée avec le motif et le numéro de l'issue (par exemple `#13`) pour lier facilement l'issue.



