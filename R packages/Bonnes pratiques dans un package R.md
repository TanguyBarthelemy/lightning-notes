## Faut-il utiliser l'opérateur `::` lors de l'appel d'une fonction d'un autre package ?

Plutôt.
Voilà qqs sources qui permettent d'argumenter la discussion.

Pour l'utilisation des `::`
- [R packages](https://r-pkgs.org/dependencies-in-practice.html#sec-dependencies-in-imports-r-code) : Par défault utiliser `::` sauf si:
	- un opérateur
	- une fonction qui est beaucoup utilisée (et on perd en lisibilité)
	- une fonction qui est utilisée dans une boucle et `::` fait perdre de la performance
- [Stackoverflow : goodpractice](https://stackoverflow.com/questions/23232791/is-it-a-good-practice-to-call-functions-in-a-package-via) conseille de ne pas charger trop de fonctions pour ne pas alourdir les namespace
- Le package [{origin}](https://github.com/mnist91/origin) propose de clarifier un script R en ajoutant les `::`
- Google a réalisé un [R style guide](https://google.github.io/styleguide/Rguide.html#qualifying-namespaces) il y a qqs années. ils y encourage l'utilisation de `::` et non de `import()`
Contre
- [`require` vs `::`](https://stackoverflow.com/questions/4372145/r-writing-style-require-vs) utiliser `::` dans un script que si  il y a un conflit
## L'utilisation de `::` ne nécessite pas d'appel `importFrom`
Oui c'est vrai.
Il y a plusieurs méthodes d'utilisation d'une fonction d'un package externe.

Prenons l'exemple de notre package `aaa` qui a une fonction `f`. Si nous aimerions que `f` dépende de la fonction `g` du package `bbb` alors :
### `::`
Soit j'appelle `g` dans `f` via `bbb::g`

Avantages :
- Pas d'écriture dans le `NAMESPACE`
- Package plus léger
- Clarifie la fonction que l'on utilise en cas de conflit
Désavantages :
- Le coût de l'import de la fonction se fait lors de l'appel et non de l'ouverture du package (donc des très très faibles lenteurs)
- Le code peut vite perdre en lisibilité avec des `::`
### `importFrom`
Soit je charge la fonction `g` dans le `NAMESPACE` de mon package `aaa` en ajoutant la balise 
`#' @importFrom bbb g` dans la description de ma fonction (ou directement dans le fichier `NAMESPACE` via la ligne `importFrom(bbb, g)`

Avantages :
- Fonction rapide d'utilisation
- Code lisible

Désavantages :
- `aaa` s'alourdit en dépendance dans le `NAMESPACE`
- Ne permet pas de régler les conflits de noms

### `import`
Soit je charge l'ensemble du package a fonction `bbb` le `NAMESPACE` de mon package `aaa` en ajoutant la balise `#' @import bbb` dans la description de ma fonction (ou directement dans le fichier `NAMESPACE` via la ligne `import(bbb)`

Avantages :
- Fonction rapide d'utilisation
- Code lisible

Désavantages :
- `aaa` s'alourdit très fortement en dépendance dans le `NAMESPACE`
- Le chargement de `aaa` peut être ralenti (car `aaa` va charger `bbb` en même temps)
- Ne permet pas de régler les conflits de noms

### Conclusion

Dans tous les cas, il est important de mentionner le package `bbb` dans le fichier `DESCRIPTION`.

Plus d'information :
- https://stackoverflow.com/a/23232888/22441094
- https://r-pkgs.org/dependencies-in-practice.html#sec-dependencies-in-imports-r-code
## Comment déclarer et documenter une méthode S3 ?
## Comment documenter des fonctions sur une même page ?


