J’ai travaillé sur ce challenge via BGB (un émulateur et debugger gameboy) et IDA.

La première étape a été de tester le programme :
 Nous pouvons déplacer un smiley en haut, bas, gauche, droite
 Nous pouvons appuyer sur "start" pour proposer notre pattern et récupérer le flag (ou prendre un "nope")

Lors des tests sous BGB on peut voir que les valeur RIGH, LEFT, UP, DOWN circulent sur la stack, mais rien de plus.
J’ai donc décidé de charger le programme sous IDA en spécifiant que l’architecture du processeur est : « Zilog 80 [z80] »
Le but étant dans un premier temps d’aller voir les strings pour repérer quelque chose de positif comme "Yeah ! Flag is %s".
En suivant les références on peut identifier notre good_boy en 3FC :
```
ROM:03FC good_boy:                             
ROM:03FC                 ld      hl, 444h <--- Notre "YEAH"
ROM:03FF                 push    hl
ROM:0400                 call    sub_EA5
[...]
ROM:0411                 inc     b
ROM:0412                 jp      loc_41E
```
Et juste au dessus, quatre fonctions ayant chacune la même suite d’instructions dont une comparaison.
```
ROM:03ED loc_3ED:                                <--- Dernier cp, si valide il saute au good_boy
ROM:03ED                 ld      bc, 0C0B3h
ROM:03F0                 ld      a, (bc)
ROM:03F1                 ld      c, a
ROM:03F2                 cp      38h ; '8'
ROM:03F4                 jp      nz, bad_boy
ROM:03F7                 jr      good_boy
```

Etant donné qu’ils sont au nombre de quatre, on peut se douter qu’il font la comparaison de nos direction (haut, bas, gauche, droite)
Chaque comparaison se fait sur le "registre" (?) a.


Il nous suffit donc de poser 4 breakpoints et de jouer avec les différentes directions pour incrémenter/décrémenter "a" et valider les quatre comparaisons.

Ce qui nous donne :
7 * Droite
9 * Gauche
2 * Haut
1 * Bas
