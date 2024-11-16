En regardant la chaîne en ASCII dans un éditeur hexadécimal, on se rend compte que le caractère * revient régulièrement, ce qui pourrait laisser penser qu’il s’agit de l’espace.

Le code ASCII de l’espace étant le 42 et l’étoile 32, on test un décodage en powershell en ajoutant 10.
```
Get-Content .\ch7.bin -Encoding byte | %{ [char]($_ - 10)}
```
