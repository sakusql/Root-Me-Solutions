C’est aussi possible d’aller fouiller toutes les entrées de la table pour trouver les autres utilisateurs, dans notre cas l’utilisateur ’admin’.

On part sur le principe que la requête sur la colonne user peut retourner plus d’un resultat, mais que le code ne prendra toujours que le premier résultat. 

Il suffit d’injecter dans le champs user (sans oublier de fournir un mot de passe bidon) :
```
' OR 1 LIMIT 1,2 --
```
Si l’on continue après avoir trouvé l’administrateur, on peut s’apercevoir qu’il existe un 3eme utilisateur, appelé user2 et ayant pour mot de passe *censuré* :
```
' OR 1 LIMIT 2,3 --
```
