En observant le contenu du fichier, on se rend compte que les lignes se terminent par des séries d’espaces et de tabulations. Cette observation, ainsi que le titre et l’indice du challenge, permettent d’orienter les recherches et de découvrir qu’on a à faire au langage Whitespace (https://fr.wikipedia.org/wiki/Whitespace).

Ce langage étant composé uniquement d’espace, de tabulation et de retours à la ligne, j’ai d’abord tenté de simplement éliminer tous les autres caractères :
```
cat ch19.txt | tr -cd ' \t\n'
```
Mais le résultat ne donne rien avec les différents interpréteurs Whitespace. En observant le texte de départ avec plus d’attention, on peut remarquer que les espaces entre les mots du poème sont toujours des espaces simples : il n’y a ni tabulations, ni espaces multiples. On peut donc penser que ces espaces sont simplement les espaces du poème et ne font pas partie du code à extraire. Il ne faut donc conservé que les groupes continus d’espaces et de tabulations situés en fin de lignes, ce qui se fait avec cette simple commande sed :
```
sed -E 's/^.*[^ \t]([ \t]*)$/\1/' ch19.txt > ch19.ws
```
Il n’y a plus qu’à passer ce code Whitespace à un interpréteur pour voir apparaître le flag.
