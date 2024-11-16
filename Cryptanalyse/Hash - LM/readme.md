Nous devons ici casser le hash LM de l’Administrateur. Ce type de hash se trouve dans la section Dumping local SAM hashes de la sortie de l’outil secretsdump :
```
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:d3bf255c530633b9aad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
ASPNET:1025:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DBAdmin:1028:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
sshd:1037:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
service_user:1038:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
```
Nous récupérons la ligne correspondant à l’administrateur, dont le format est (uid:rid:lmhash:nthash). Le hash LM est donc l’avant dernier :
```
Administrator:500 :d3bf255c530633b9aad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:: :
```
Pour casser ce hash avec hashcat, il faut utiliser le mode 3000 correspondant aux type des hashs LM, et la wordlist rockyou.txt
```
echo "d3bf255c530633b9aad3b435b51404ee" > h
hashcat --status --hash-type 3000 --attack-mode 0 ./h /usr/share/wordlists/rockyou.txt
```
Et nous avons :
```
Dictionary cache built:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 27181943
* Bytes.....: 139921507
* Keyspace..: 27181943
* Runtime...: 2 secs

d3bf255c530633b9:censuré!                        
Session..........: hashcat                      
Status...........: Cracked
Hash.Name........: LM
Hash.Target......: ./h
Time.Started.....: Tue Jul 13 10:28:32 2021 (1 sec)
Time.Estimated...: Tue Jul 13 10:28:33 2021 (0 secs)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:  5006.7 kH/s (0.56ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 2/2 (100.00%) Digests
Progress.........: 3612672/27181943 (13.29%)
Rejected.........: 0/3612672 (0.00%)
Restore.Point....: 3604480/27181943 (13.26%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidates.#1....: AGUADA3 -> ADAMM6
Started: Tue Jul 13 10:28:16 2021
Stopped: Tue Jul 13 10:28:34 2021
```
