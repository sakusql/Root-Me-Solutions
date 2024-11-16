Une solution avec angr

Voici le code :

```
import angr
import claripy
 
if __name__ == '__main__':
 
    buf = 0x7fff7e1c # la ou est stocke le flag ( obtenu via GDB )
    start_from = 0x400960   # on demarre l'exploration a partir d'ici
    find = 0x4007c0   # on veut atteindre "win"
    avoid = 0x400814  # tout en evitant "fail"
 
    proj = angr.Project('./ch27.bin')
 
    state = proj.factory.blank_state(addr=start_from, add_options={angr.options.LAZY_SOLVES})
 
    flag = claripy.BVS('flag', 19*8, explicit_name=True) # apres analyse statique, on sait que le pass fait 19 chars
    state.memory.store(buf, flag)
    # on set le frame pointer
    state.regs.fp = 0x7fff7e00
 
    #for i in range(19):
    #   state.solver.add(flag.get_byte(i) >= 0x30)
    #   state.solver.add(flag.get_byte(i) <= 0x7f)
 
    simgr = proj.factory.simgr(state)
 
    simgr.explore(find=find, avoid=avoid)
    found = simgr.found[0]
    print found.solver.eval(flag, cast_to=str)
```

Et voila 😉
