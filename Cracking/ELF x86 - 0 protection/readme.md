En utilisant GDB :
```
$ gdb ./ch1.bin
(gdb) break main
Breakpoint 1 at 0x80486ab
(gdb) run
Starting program: /home/ludovic/ch1.bin

Breakpoint 1, 0x080486ab in main ()
(gdb) disass
La première commande lance gdb sur le programme à analyser
La deuxième permet de positionner un point d’arrêt sur la fonction main (point d’entré du programme)
La troisième exécute le programme (jusqu’au point d’arrêt)
La dernière affiche le code assembleur
```
Le code affiché est donc (je ne mets ici que les lignes qui nous intéressent) :
```
  0x080486f0 <+83>:        mov    %eax,-0xc(%ebp)
  0x080486f3 <+86>:        mov    -0x8(%ebp),%eax
  0x080486f6 <+89>:        mov    %eax,0x4(%esp)
  0x080486fa <+93>:        mov    -0xc(%ebp),%eax
  0x080486fd <+96>:        mov    %eax,(%esp)
  0x08048700 <+99>:        call   0x80484d8 <strcmp@plt>
```
On peut voir qu’une comparaison de chaine est réalisée . Il serait intéressant de voir les 2 chaines comparées.
Pour cela on pose un point d’arrêt sur l’instruction et on continue l’exécution :
```
(gdb) break *0x08048700
(gdb) continue
```
On entre un mot de passe bidon ("Toto" dans mon exemple) puis lorsque gdb reprend la main, on va examiner ce qui est placé dans la pile (les paramètres de la fonction strcmp sont passés dans la pile).
Cependant il faut faire une indirection car ce qui est concrètement placé dans la pile est les adresses des chaînes de caractères à comparer.
```
(gdb) x/1xw $esp
0xbffff370:        0x0804b008
(gdb) x/1xw $esp+4
0xbffff374:        0x08048841
```
la commande "x/1wx $esp" permet d’afficher (x) 1 (1) mot de 16 bits (w) au format hexadécimal (x) à l’adresse contenu dans le registre ESP. Cela correspond au 2ième paramètre de la fonction stcmp
la commande "x/1wx $esp+4" permet d’afficher (x) 1 (1) mot de 16 bits (w) au format hexadécimal (x) à l’adresse contenu dans le registre ESP+4. Cela correspond au 1er paramètre de la fonction stcmp
Cela nous renvoi donc les adresses des 2 chaines de caractères :
```
0x0804b008 : Adresse du second paramètre
0x08048841 : Adresse du premier paramètre
```
On regarde à ces adresses :
```
(gdb) x/1s 0x0804b008
0x804b008:        "Toto"
(gdb) x/1s 0x08048841
0x8048841:        "[censuré]"
```
On voit que la chaine "Toto" (celle qu’on a rentrée) est comparée à "[censuré]".

Le flag attendu est donc "[censuré]".
