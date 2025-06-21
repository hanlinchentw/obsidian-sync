## Knowledge
**Stack in Real-Mode Assembly:**
- **SP (Stack Pointer)** points to the top of the stack.
- **BP (Base Pointer)** is often used to reference values relative to the base of the stack.
- The stack **grows downward** (i.e., pushing values decrements `SP`).
- Stack is word-addressed: `push` and `pop` operate on **2 bytes (16 bits)** at a time.
- **BIOS video service** (interrupt `0x10`) uses `AL` to specify **which character to print** — but **only for function `AH = 0x0E`** (teletype output)

**Goal: Learn how to use the stack**

use 0x8000 as our base address.
and move bp into sp (stack is empty then sp points to bp).
```assembly
mov bp, 0x8000
mov sp, bp
```

```
; to show how the stack grows downwards
mov al, [0x7ffe] ; 0x8000 - 2
int 0x10
; show 'A'

; however, don't try to access [0x8000] now, because it won't work
; you can only access the stack top so, at this point, only 0x7ffe (look above)
mov al, [0x8000]
int 0x10
```

execute the following procedure three times, we can get CBA.
```
pop bx ; pop into bx
mov al, bl
int 0x10 ; prints C
```