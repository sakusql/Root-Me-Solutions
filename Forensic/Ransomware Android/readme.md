Le but est ici de déchiffrer un document chiffré par un ransomware Android.

Un fichier chiffré est présent dans le dump :
```
./Android-dump/media/Documents $ ls -l
-rwxr-xr-x 1 fab fab 144000  5 mai   23:50 Confidentiel.jpg.enc
```
On note la présence d’une seule application dans le répertoire app :
```
./Android-dump/app $ ls
-rw-r--r--  1 fab fab 4868609  5 mai   23:30 org.simplelocker-1.apk
```
Il suffit alors de la décompiler et de jeter un oeil aux classes "FilesEncryptor.class" et "AesCrypt.class". Les outils utilisés sont dex2jar (pour convertir le classes.dex en .jar) et jad pour décompiler les classes Java.

Dans FilesEncryptor on voit :
```
public void decrypt()
        throws Exception
    {
        ...
        AesCrypt aescrypt;
        ...
        aescrypt = new AesCrypt("mcsTnTld1dDn");
        ...
```

Et dans la classe AesCrypt :
```
public AesCrypt(String s)
        throws Exception
    {
        MessageDigest messagedigest = MessageDigest.getInstance("SHA-256");
        byte abyte0[] = s.getBytes("UTF-8");
        messagedigest.update(abyte0);
        byte abyte1[] = new byte[32];
        byte abyte2[] = messagedigest.digest();
        int i = abyte1.length;
        System.arraycopy(abyte2, 0, abyte1, 0, i);
        Cipher cipher1 = Cipher.getInstance("AES/CBC/PKCS7Padding");
        cipher = cipher1;
        SecretKeySpec secretkeyspec = new SecretKeySpec(abyte1, "AES");
        key = secretkeyspec;
        AlgorithmParameterSpec algorithmparameterspec = getIV();
        spec = algorithmparameterspec;
}
 
...
 public AlgorithmParameterSpec getIV()
    {
        byte abyte0[] = new byte[16];
        return new IvParameterSpec(abyte0);
    }
```

Il est alors assez simple de recréer un code Python effectuant le déchiffrement :
```
from Crypto.Cipher import AES
import sys
import hashlib
 
with open(sys.argv[1],"rb") as fff:
    data = fff.read()
 
iv = "\x00"*16
key = hashlib.sha256("mcsTnTld1dDn").digest()
 
blah = AES.new(key, AES.MODE_CBC, iv)
 
with open("/tmp/out.jpg","wb") as fff:
    fff.write(blah.decrypt(data))
```

Le flag apparait dans l’image déchiffrée.
