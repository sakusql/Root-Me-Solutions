Dans un premier temps, on va s’essayer à comprendre l’exploitation de la faille en se documentant sur cette dernière et en s’amusant sur les différents inputs.

Au niveau de la page register.php, si l’on essaye :
```
login = "admin"
password = "staycalm2phi"
User already in DB or password too short (8 chars min)
```
On comprend vite qu’un utilisateur nommé "admin" existe déjà en essayant de l’inscrire sur le site.
```
login = "admin "
password = "staycalm2phi"
User already in DB or password too short (8 chars min)
```
On a l’impression que SQL tronque les espaces et ne les prend pas en compte.
```
login =  "admin 2phi"
password  = "staycalm2phi"
User saved
```
Bingo  😄 . Comment exploiter cela ?
Une information importante sur le schéma de base de données réside dans le code de la page :
```
CREATE TABLE IF NOT EXISTS USER(  
    id INT NOT NULL AUTO_INCREMENT,
    login VARCHAR(12),
    password CHAR(32),
    PRIMARY KEY (id)
);
```

Ici l’on peut voir que le login n’accepte que 12 caractères. L’objectif est d’insérer assez d’espace pour que la chaîne du login saisie par l’utilisateur ne soit pas directement égale à "admin" mais qu’elle le devienne après la "truncation" effectuée par SQL lors du dépassement de la taille maximum allouée à un attribut.

La chaîne admin fait 5 caractères, il suffit d’y ajouter 7 caractères vides ainsi que notre suffixe afin de tronquer la fin de la chaîne lors de la création de notre utilisateur et d’insérer un autre admin dans la base de données.

En quelques lignes de code :
```
import requests
 
suffixe = "2phi"
padding = "  " * 7
data = {"login":"admin" + padding + suffixe, "password":"censuré"}
r = requests.post("http://challenge01.root-me.org/web-serveur/ch36/register.php", data)
```


Il suffisait ensuite de se rendre sur la page admin.php et de tenter de se connecter avec le password indiqué dans le code.
