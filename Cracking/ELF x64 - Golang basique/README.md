```
(gdb) file ch32.bin

(gdb) i file
Symbols from "/root/Downloads/Cracking/ch32.bin".
Local exec file:
       `/root/Downloads/Cracking/ch32.bin', file type elf64-x86-64.
       Entry point: 0x4522f0
       0x0000000000401000 - 0x0000000000493180 is .text
       0x0000000000494000 - 0x00000000004d784e is .rodata
       0x00000000004d7980 - 0x00000000004d86dc is .typelink
       0x00000000004d86e0 - 0x00000000004d8740 is .itablink
       0x00000000004d8740 - 0x00000000004d8740 is .gosymtab
       0x00000000004d8740 - 0x000000000052b785 is .gopclntab
       0x000000000052c000 - 0x0000000000538c44 is .noptrdata
       0x0000000000538c60 - 0x000000000053e670 is .data
       0x000000000053e680 - 0x000000000055cc88 is .bss
       0x000000000055cca0 - 0x000000000055f398 is .noptrbss
       0x0000000000400fc8 - 0x0000000000401000 is .note.go.buildid

(gdb) find 0x0000000000494000, 0x00000000004d784e, 'w', 'r', 'o', 'n', 'g', ' ', 'f', 'l', 'a', 'g'
0x4c4d7a
warning: Unable to access 12491 bytes of target memory at 0x4d4784, halting search.
1 pattern found.

(gdb) x /s $_
0x4c4d7a:       "wrong flag (targetpc= gcwaiting= gp.status= heap_live= idleprocs= in status  m->mcache= mallocing= ms clock,  p->mcache= p->status= schedtick= span.list=/dev/stderr/dev/stdout/usr/lib/go30517578125: f"...

(gdb) x /s $_
0x4c4d7a:       "wrong flag (targetpc= gcwaiting= gp.status= heap_live= idleprocs= in status  m->mcache= mallocing= ms clock,  p->mcache= p->status= schedtick= span.list=/dev/stderr/dev/stdout/usr/lib/go30517578125: f"...

(gdb) rwatch *0x4c4d7a
Hardware read watchpoint 1: *0x4c4d7a

(gdb) r
Starting program: /root/Downloads/Cracking/ch32.bin
[New LWP 54163]
[New LWP 54164]
toto

Thread 1 "ch32.bin" hit Hardware read watchpoint 1: *0x4c4d7a

Value = 1852797559
runtime.memmove () at /usr/lib/go/src/runtime/memmove_amd64.s:173
173     /usr/lib/go/src/runtime/memmove_amd64.s: No such file or directory.

(gdb) where
#0  runtime.memmove () at /usr/lib/go/src/runtime/memmove_amd64.s:173
#1  0x00000000004843cc in fmt.(*fmt).padString (f=0xc420068040, s=...) at /usr/lib/go/src/fmt/print.go:82
#2  0x0000000000485181 in fmt.(*fmt).fmt_s (f=0xc420068040, s=...) at /usr/lib/go/src/fmt/format.go:328
#3  0x000000000048833f in fmt.(*pp).fmtString (p=0xc420068000, v=..., verb=118) at /usr/lib/go/src/fmt/print.go:430
#4  0x000000000048a6b5 in fmt.(*pp).printArg (p=0xc420068000, arg=..., verb=118) at /usr/lib/go/src/fmt/print.go:664
#5  0x000000000048d7d2 in fmt.(*pp).doPrint (p=0xc420068000, a=...) at /usr/lib/go/src/fmt/print.go:1124
#6  0x0000000000486b18 in fmt.Fprint (w=..., a=..., n=4867776, err=...) at /usr/lib/go/src/fmt/print.go:215
#7  0x0000000000486c17 in fmt.Print (a=..., n=842350534928, err=...) at /usr/lib/go/src/fmt/print.go:225
#8  0x00000000004930fa in main.main () at /home/jenaye/dev/C/CTF_perso/reverseGo.go:22
```

La syntaxe de find peut surprendre : pourquoi énumérer les caractères de "wrong flag" ? C’est que cela ne produit aucun résultat, parce que "wrong flag" ne se termine pas par \0 en mémoire. On voudrait alors suivre la documentation de GDB et utiliser cette notation... :

```
(gdb) find 0x0000000000494000, 0x00000000004d784e, {char[10]} "wrong flag"
```

...mais cela ne fonctionne pas, car GDB interprète les expressions dans le language du programme débogué, soit GO. Peut-être existe-t-il une syntaxe en GO équivalente, mais je ne l’ai pas trouvée... De ce fait, j’ai dû me contenter d’énumérer les caractères pour que GDB recherche "wrong flag" sans \0 final.

C’est donc dans la fonction main.main () que se déroule l’appel à la fonction qui va afficher le message "wrong flag".

A partir de là, le plus simple est de demander à Ghidra de reconstruire un code C approximativement lisible pour comprendre ce qui se passe.

Pour installer Ghidra, il suffit de télécharger et désarchiver sa dernière version :

https://ghidra-sre.org/

Ghidra est en Java, et il faut donc télécharger et désarchiver le JDK version 11 (un simple tar -xvf dans un répertoire quelconque) :

https://adoptopenjdk.net/releases.html?variant=openjdk11&jvmVariant=hotspot

Enfin, il suffit de lancer Ghidra, qui va réclamer le chemin d’accès au JDK :

```
./ghidraRun
```

De là, il faut créer un projet, et y importer ch32.bin. Ensuite, il faut cliquer sur la zolie tête de dragon qui zoome, et voilà apparaître l’environnement dans lequel Ghidra nous propose de machouiller ch32.bin pour en recracher une version C. Il faut alors ouvrir la copie de ch32.bin que Ghidra a logée dans le répertoire du projet, et accepter toutes les analyses par défaut.

Après avoir beaucoup mouliné, Ghidra produit cette version de main.main (). Pour y accéder il suffit d’ouvrir le dossier "main..." dans l’aborescence de la fenêtre "Symbol Tree", et de cliquer sur "main.main" :

```
void main.main(void)

{
 undefined **ppuVar1;
 byte *pbVar2;
 ulong uVar3;
 byte *pbVar4;
 ulong uVar5;
 long in_FS_OFFSET;
 byte **local_b0;
 long local_a0;
 ulong local_98;
 long local_88;
 undefined *local_38 [2];
 undefined *local_28;
 string *local_20;
 undefined *local_18;
 string *local_10;
 
 ppuVar1 = (undefined **)(*(long *)(in_FS_OFFSET + 0xfffffff8) + 0x10);
 if (local_38 < *ppuVar1 || local_38 == (undefined **)*ppuVar1) {
        runtime.morestack_noctxt();
        main.main();
        return;
 }
 runtime.newobject();
 local_38[0] = &DAT_0049ed80;
 fmt.Scanln();
 runtime.stringtoslicebyte();
 runtime.makeslice();
 pbVar2 = local_b0[1];
 pbVar4 = *local_b0;
 uVar5 = 0;
 while( true ) {
        if ((long)pbVar2 <= (long)uVar5) {
          bytes.Compare();
          if (local_88 == 0) {
                local_18 = &DAT_004a4580;
                local_10 = &main.statictmp_0;
                fmt.Print();
          }
          else {
                local_28 = &DAT_004a4580;
                local_20 = &main.statictmp_1;
                fmt.Print();
          }
          return;
        }
        if (local_98 == 0) break;
        if (local_98 == 0xffffffffffffffff) {
          uVar3 = 0;
        }
        else {
          uVar3 = (long)uVar5 % local_98;
        }
        if ((local_98 <= uVar3) || (local_98 <= uVar5)) {
          runtime.panicindex();
          do {
                invalidInstructionException();
          } while( true );
        }
        *(byte *)(local_a0 + uVar5) = *pbVar4 ^ *(byte *)(local_a0 + uVar3);
        pbVar4 = pbVar4 + 1;
        uVar5 = uVar5 + 1;
 }
 runtime.panicdivide();
 do {
        invalidInstructionException();
 } while( true );
}
```

La logique d’ensemble se dessine. Visiblement, les caractères de la chaîne saisie soient combinés par XOR avec les caractères d’une clé pour produire un ciphertexte, dont on suppose qu’il est comparé au résultat de la même opération appliquée au flag.

Le grand jeu consiste alors à repérer dans Ghidra les instructions en assembleur qui correspondent aux accès aux variables identifiées par Ghidra, et de placer des breakpoints dans GDB pour lire les valeurs auxquelles elles correspondent. Par exemple, en cliquant sur pbVar4 sur cette ligne... :

```
*(byte *)(local_a0 + uVar5) = *pbVar4 ^ *(byte *)(local_a0 + uVar3);
```
...Ghidra pointe le code assembleur suivant :

```
00492fbc MOVZX  R10D,byte ptr [RBX]
```

Dès lors, il suffit de placer un breakpoint dans GDB et de regarder sur quoi pointe RBX, qui stocke dans R10D le caractère qui va subir un XOR :

```
(gdb) file ch32.bin

(gdb) b *0x00492fbc

(gdb) r
...
0123456789

(gdb) x /s $rbx
0xc420012120:   "0123456789"
```
Donc pbVar4 est un pointeur sur le flag saisi, ici "0123456789".

C’est ainsi qu’il est possible de trouver la clé. Après avoir cliqué sur "local_a0", Ghidra pointe :

```
00492fed MOVZX  EDX,byte ptr [R8 + RDX*0x1]
```

Et la même manoeuvre dans GDB permet de retrouver la clé :

```
(gdb) b *0x00492fed

(gdb) c

(gdb) x /s $r8
0xc42003df10:   "rootme"
```
Dès lors, il ne reste plus qu’à placer un breakpoint dans GDB au niveau de bytes.Compare () pour récupérer le résultat du chiffrement du flag avec la clé "rootme". Dans le code asssembleur pointé par Ghidra, 5 paramètres sont empilés avant l’appel :

```
00493008 MOV    qword ptr [RSP + local_b0],0xe
00493011 MOV    qword ptr [RSP + local_a8],0xe
0049301a MOV    qword ptr [RSP + local_a0],RDX
0049301f MOV    qword ptr [RSP + local_98],RCX
00493024 MOV    qword ptr [RSP + local_90],RAX
00493029 CALL   bytes.Compare
```
Dans GDB, un breakpoint permet d’explorer la pile pour déterminer quel paramètre correspond au ciphertexte du flag :

```
(gdb) b *0x00493029

(gdb) x /5xg $rsp
0xc42003dec0:   0x000000c42003df02      0x000000000000000e
0xc42003ded0:   0x000000000000000e      0x000000c420012130
0xc42003dee0:   0x000000000000000a

(gdb) x /32xb 0x000000c42003df02
0xc42003df02:   0x3b    0x02    0x23    0x1b    0x1b    0x0c    0x1c    0x08
0xc42003df0a:   0x28    0x1b    0x21    0x04    0x1c    0x0b    0x72    0x6f
0xc42003df12:   0x6f    0x74    0x6d    0x65    0x00    0x00    0x00    0x00
0xc42003df1a:   0x00    0x00    0x00    0x00    0x00    0x00    0x00    0x00

(gdb) x /32xb 0x000000c420012130
0xc420012130:   0x42    0x5e    0x5d    0x47    0x59    0x50    0x44    0x58
0xc420012138:   0x57    0x4d    0x00    0x00    0x00    0x00    0x00    0x00
0xc420012140:   0x00    0x00    0x00    0x00    0x00    0x00    0x00    0x00
0xc420012148:   0x00    0x00    0x00    0x00    0x00    0x00    0x00    0x00
```
Etant données les longueurs, il est clair que le ciphertexte du flag est la seconde série d’octets. Il ne reste plus qu’à écrire quelques lignes de Python :

```
ciphertext = b'\x3b\x02\x23\x1b\x1b\x0c\x1c\x08\x28\x1b\x21\x04\x1c\x0b\x72\x6f\x6f\x74\x6d\x65'
key = b'rootme'
flag = ''
for i in range (len (ciphertext)):
        flag += chr (ciphertext[i] ^ key[i % len (key)])
print (flag)
```
Et voilà !
