J’ai essayer de rentrer une injection SQL standard 
```
' OR 1=1; -- ET ' OR 1=1 #
```
Rien d’anormal s’est passé, j’ai donc essayé avec un caractères GBK (comme le titre l’indique :) 
```
呵' or 1=1; -- ET 呵' or 1=1 #
```
Et bingo ! 呵' or 1=1 # nous donne l’accès !

Pourquoi ? car la function mysql_real_escape_string() ne support pas les caractères GBK et créer un "BUG" ne filtrant pas le caractère quote " ’ "
