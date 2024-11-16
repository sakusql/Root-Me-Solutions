Avec gdb
```
> gdb ch12.bin
```
```
gdb > b main
gdb > disas main
```
on repérer les adresses des sauts conditionnels
```
0x0804837a <+113> : jne 0x80483d0
```
```
..........................
0x080483aa <+161> : jne 0x80483c2
```
on remplace le jne (0x75) par je(0x74)
```
gdb > set {short}0x0804837a = 0x74
gdb > set {short}0x080483aa = 0x74
```
on reprend l’execution :
```
gdb> n
############################################################
## Bienvennue dans ce challenge de cracking ##
############################################################

username : fdsdf
password : dfsdf
```
Bien joue, vous pouvez valider l’epreuve avec le mot de passe : censuré
