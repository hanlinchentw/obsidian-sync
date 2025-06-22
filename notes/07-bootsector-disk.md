**Goal: Let the bootsector load data from disk in order to boot the kernel**

we need to read data from a disk in order to run the kernel.

we set `al` to `0x02` (and other registers with the required cylinder, head and sector) and raise `int 0x13`
[a detailed int 13h guide here](http://stanislavs.org/helppc/int_13-2.html)

