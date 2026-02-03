Ici je me concentre sur le format csv et les fonctions `write.csv`, `write.csv2` et `write.table`

### Separator

Par défault, 
- `write.csv` utilise une virgule (comma) `,`
- `write.csv2` utilise un point-virgule (semicolon) `;` 
- `write.table` utilise un espace ` ` 

`write.table` a aussi un argument `sep` qui permet de changer ce separateur.

Ainsi les commandes suivantes sont équivalentes:

```r
write.table(iris, file = "table.csv", sep = ",", row.names = FALSE)
write.csv(iris, file = "table.csv", row.names = FALSE)
```

Rq : Il faut mettre `row.names = FALSE` car sinon `write.csv` écrit une nouvelle colonne avec les numéros de ligne.

```r
write.table(iris, file = "table.csv", sep = ";", row.names = FALSE)
write.csv2(iris, file = "table.csv", row.names = FALSE, dec = ",")
```

Même remarque que précédemment. Aussi `write.csv2` utilise la virgule (comma) `,` comme séparateur de décimal.

### Libre Office

Par défault, Libre Office utilise le même séparateur que celui utilisé pour importer le fichier.
Pour changer le mode d'export et les paramètres du CSV, tout est expliqué [ici](https://superuser.com/a/738415).
