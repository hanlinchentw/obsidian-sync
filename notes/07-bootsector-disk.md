Knowledge
### **Heads** refer to:

> The number of **read/write heads** in a **cylinder-head-sector (CHS)** disk geometry.

- Each **platter surface** in a hard drive has a **head**.
    
- For example, a 2-sided floppy has 2 heads — one for each side.

**Goal: Let the bootsector load data from disk in order to boot the kernel**

we need to read data from a disk in order to run the kernel.

we set `al` to `0x02` (and other registers with the required cylinder, head and sector) and raise `int 0x13`
[a detailed int 13h guide here](http://stanislavs.org/helppc/int_13-2.html)


In the int 13h interrupt, we set ah to 0x02 for read sector mode.
and set the al (number of sectors) we want to read to dh (in this case is 2).
and we set the cl (sector number) to 2.
```
mov ah, 0x02
mov al, dh
mov cl, 0x02
```

after interrupting, we pop dx and do cmp al, dh.

**So `cmp al, dh` is really:**

_“Did the BIOS read the same number of sectors that we asked for?”_
- If **equal**, all is well.
- If **different**, control flows to `sectors_error`.