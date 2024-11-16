Il suffit simplement de posséder Hashcat.
On connait l’algorithme de chiffrement utilisé, en l’occurence MD5, on regarde sa valeur-code pour hashcat (hashcat -h, elle est de 0.
On peut utiliser un dictionnaire basique pour bruteforcer le hash (rockyou.txt)
```
Ce qui nous donne cette commande : hashcat -a0 -m0 md5.txt rockyou.txt
```
