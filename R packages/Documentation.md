# Conventions de rédaction de la documentation pour les packages 'rjdverse'

Ce document regroupe les conventions de rédaction de la documentation pour les packages 'rjdverse', dans un but de clarté et de cohérence entre packages.

## `@param`

### Principes généraux:

- Chaque argument doit indiquer **le type** de l’objet attendu (boolean, integer, data.frame, character or numeric vector, ts object…).

- La description doit préciser :
  - **le rôle de l’argument**,
  - **les conditions d’utilisation** (obligatoire, optionnel, nécessaire dans tel ou tel cas (à compléter avec la section `@details`)),
  - **la valeur par défaut**, ou l’absence de valeur par défaut.

- Toujours commencer par une **Majuscule** et terminer par un **point**.

- Lorsque la description ne tient pas sur une ligne : indenter la ligne suivante avec **2 espaces**.

### Exemples

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
---

## `@examples` ou `@examplesIf`

### Principes généraux:

- Respect des règles de **linting**.
- Toutes les fonctions **exportées** qui sont du niveau de l'utilisateur (pas fonctions internes) doivent avoir un ou plusieurs exemple(s) illustratif(s).
- Pour les fonctions longues (runtime > 5 s), utiliser `\donttest` (et/ou proposer un exemple simplifié).
- Ne jamais utiliser `\dontrun`.
- Si nécessaire, utiliser le tag **@examplesIf** pour vérifier la version de Java avant d'exécuter l'exemple.

### Exemples

```r
#' @examplesIf rjd3toolkit::get_java_version() >= rjd3toolkit::minimal_java_version
#'
#' y <- rjd3toolkit::ABS$X0.2.09.10.M
#' sp <- tramo_spec("trfull")
#' sp <- rjd3toolkit::add_outlier(sp,
#'     type = c("AO"), c("2015-01-01", "2010-01-01")
#' )
#' tramo_fast(y, spec = sp)
#' 
#' # or
#' \donttest{
#' tramo_fast(y, spec = sp)
#' }
```

---

## `@details`

### Principes généraux:

Reprend toute précision ou explication supplémentaire relatif au fonctionnement propre de la **fonction**.

En particulier, cela permet de :

- compléter la documentation d'**objets plus complexes**.
- décrire les **relations** entre arguments.
- détailler les **hypothèses**, **restrictions** et **comportements subtils**,

### Exemples

```r
#' @details
#'
#' ... 
#'
#' ### Estimate of the constant term with Fernandez and Litterman :
#'
#' In practice, Chow-Lin, Fernandez and Litterman are estimated based on a 
#' state space representation of the model. By default, for Fernandez and Litterman, 
#' a diffuse initialization is considered for the estimation of the initial values 
#' of the states so that those integrate the estimate of the constant term. 
#' The latter is thus ‘hidden’ and does not appear explicitly in the output. 
#' To make the estimates of the constant term visible - thus recovering the usual 
#' output from the classic formulation of the model - one option is to change the 
#' argument `zeroinitialization = TRUE` with `constant = TRUE` when calling the function.

```

---

## Vignettes

Une vignette est le guide détaillé de votre **package**. 

La documentation des fonctions est utile si vous connaissez le nom de la fonction 
à utiliser mais est inutile dans le cas contraire. La vignette peut fournir les 
informations nécessaires sur la structure du package et le lien entre les fonctions.

Lorsque vous rédigez une vignette, vous apprenez à quelqu'un à utiliser votre package. 
Vous devez vous mettre à la place du lecteur et adopter un « esprit de débutant ».

Il est possible d'avoir plusieurs vignettes. Par exemple, une vignette décrivant le 
fonctionnement général du package, et une autre autour de la résolution d'un problème spécifique.

---

## Vocabulaire

Certains termes sont à privilégier par rapport à d'autres:

- Boolean plutôt que logical
- Character vector => length > 1
- Character string => length = 1
- Vector => length > 1
- Scalar => length = 1
- Pour parler de variable numérique, il vaut mieux utiliser les termes :
	- integer (`1L`, `-4L`): nombres entiers
	- double (`pi`, `0`, `-11.1`): nombres réels

---

## `@returns`

- Toujours utiliser `@returns` (et non `@return`).
- Décrire **le type** et **la structure** de l’objet retourné. Par exemple, si votre fonction 
renvoie un tableau de données, vous pouvez décrire les noms et les types des colonnes ainsi que 
le nombre de lignes attendu.

### Exemple
```r
#' @returns An object of class "JD3TempDisagg" containing the results of the temporal disaggregation procedure. 
#'   The following are returned invisibly as a list:
#'   \itemize{
#'   \item \code{regression} \verb{[[1]]} regression coefficients
#'   \item \code{estimation} \verb{[[2]]} disaggregated time series and its errors, regression effects and smoothing part, other parameters and residuals 
#'   \item \code{likelihood} \verb{[[3]]} information regarding the likelihood 
#'   }
```

---

## `@title` et `@description`

Recommandés pour améliorer la lisibilité et structurer la documentation.

---

## Regroupement : `@name` et `@rdname`

Il est possible de documenter plusieurs fonctions qui fonctionnent de manière similaire ou ensemble avec les tags `@name` et `@rdname`.

---

## Méthodes S3

Lorsqu'on déclare une méthode S3 `f` pour une nouvelle classe c1, on va créer une fonction `f.c1`.
Ne pas oublier d'ajouter les tags correspondants à la nouvelle méthode si on veut l'exporter et la documenter :
Aussi, ne pas oublier les tags `@export` si on veut exporter la méthode et `@rdname` pour documenter la méthode.

```r
#' @exportS3Method f c1
#' @method f c1
```

---

## Fonctions internes

Toutes les fonctions exportées ou non exportées, internes ou non doivent être documentées.

Pour toutes les fonctions internes, utiliser :

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


---

## CHANGELOG (NEWS.md)

- Les packages R suivent la convention [Semantic Versioning](https://semver.org/) pour le numérotage des versions.
- Ils suivent aussi le format [Keep a Changelog](https://keepachangelog.com/).
- La GHA `check-changelog` s'occupe de vérifier que le CHANGELOG (NEWS.md) a la bonne forme.

Quelques règles supplémentaires de cohérence :

- Lorsqu'on faut une modification *substancielle* dans un package R, il convient de modifier le NEWS.md en conséquence
- Les listes à puce commencent par `*` puis une majuscule pour plus de lisibilité
- Lorsqu'on ferme une issue, il faut ajouter une ligne correspondante à l'issue fermée avec le motif et le numéro de l'issue (par exemple `#13`) pour lier facilement l'issue.

