Le pattern de déverrouillage est stocké dans le fichier /android/data/system/gesture.key sous la forme d’un hash SHA1.

Pour retrouver la combinaison associée au SHA1, j’ai généré une table de correspondance à l’aide du script python suivant :
```
import hashlib
 
def genKeys(prefix, digits, f):
    for i in digits:
        pre = prefix[:]
        pre.append(i)
        if len(pre)>=4:
            f.write("".join([str(i) for i in pre]) + ' ' + hashlib.sha1("".join([chr(i) for i in pre])).hexdigest() + '\n')
        d = digits[:]
        d.remove(i)
        genKeys(pre, d, f)
       
with open("gesture.txt", "w") as f:
    genKeys([], [0,1,2,3,4,5,6,7,8], f)
```

Ensuite il suffit de faire un grep dans le fichier :
```
grep -i 2C3422D33FB9DD9CDE87657408E48F4E635713CB gesture.txt
```
