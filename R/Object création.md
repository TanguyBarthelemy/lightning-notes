En R, la méthode de création d'un objet peut avoir beaucoup d'importance sur la complexité temporelle (et spatiale) de l'opération.

On va prendre comme exemple la création d'un `data.frame()`
### Niveau 1 : Objets existants
C'est le cas le plus simple, les objets existent déjà (ont été créé à un autre moment de la chaîne) :
```r
df <- data.frame(x, y, z)
```

### Niveau 2 : Objets vectorisés
C'est le cas le plus répandu et celui que l'on veut atteindre. Il est possible de créer les colonnes de notre `data.frame` à partir de calcul vectorisé.
Exemple :
```r
x <- 1
y <- runif(100)
z <- 2 * y + x
df <- data.frame(x, y, z)
```

### Niveau 3 : Colonnes indépendantes
Lorsque la création des colonnes se fait de manière indépendante et que la fonction utilisée n'est pas vectorisée, il est possible de l'inclure dans un wrapper qui va itérer de manière optimisé notre calcul. C'est l'utilisation des fonctions `Reduce`, `stats::filter`, `vapply`, `Map`

### Niveau 4 : Travail itéré
Dans le cas où chaque observation dépend des précédentes et de manière croisée avec les autres colonnes, il peut être nécessaire d'utiliser ALORS une boucle `for` ou `while`. 

Dans ce cas là, je fais une comparaison de la vitesse dans le paragraphe [[Speedtest - R#Création]].
