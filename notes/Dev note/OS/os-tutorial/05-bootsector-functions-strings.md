**Goal: Learn how to code basic stuff (loops, functions) with the assembler**

## Strings

Define strings like bytes, but terminate them with a null-byte (yes, like C) to be able to determine their end.

```assembly
mystring:
    db 'Hello, World', 0
```

用引號包起來會被 assembler 轉成 ASCII，而 0 會被 0x00 (null byte) 表示
## Control structures

cmp 判斷式
使用 jmp, je 前往流程

| Instruction | Meaning                | Depends on Condition? | Typical Use                               |
| ----------- | ---------------------- | --------------------- | ----------------------------------------- |
| `jmp`       | **Unconditional jump** | ❌ No                  | Always jumps to the label                 |
| `je`        | **Jump if Equal**      | ✅ Yes (ZF = 1)        | Jumps only if last comparison was "equal" |

Example
```assembly
cmp ax, 4      ; if ax = 4
je ax_is_four  ; do something (by jumping to that label)
jmp else       ; else, do another thing
jmp endif      ; finally, resume the normal flow
```

## Calling functions

As you may suppose, calling a function is just a jump to a label.
`jmp function`
注意：
1. Register , memory address 是共用的

```
mov al, 'X'
jmp print
endprint:
    ...
print:
    mov ah, 0x0e  ; tty code
    int 0x10      ; I assume that 'al' already has the character
    jmp endprint  ; this label is also pre-agreed
```

You can see that this approach will quickly grow into spaghetti code. The current `print` function will only return to `endprint`. What if some other function wants to call it? We are killing code reusage.

The correct solution offers two improvements:
- We will store the return address so that it may vary
- We will save the current registers to allow subfunctions to modify them without any side effects

To store the return address, the CPU will help us. Instead of using a couple of `jmp` to call subroutines, use `call` and `ret`.

To save the register data, there is also a special command which uses the stack: `pusha` and its brother `popa`, which pushes all registers to the stack automatically and recovers them afterwards.
## 🧠 Summary: Key Differences

|Feature|`jmp`-based “functions”|`call` + `ret` subroutines|
|---|---|---|
|Reusable?|❌ No|✅ Yes|
|Return path?|❌ Hardcoded|✅ Dynamic via stack|
|Stack use?|❌ None|✅ Uses return address|
|Register safety?|❌ Not guaranteed|✅ `pusha` / `popa` protect|

## Rewrite the code using `call`/`ret`:

```
mov al, 'X'
call print_char

print_char:
    pusha           ; Save all registers
    mov ah, 0x0e
    int 0x10        ; Print character in AL
    popa            ; Restore all registers
    ret             ; Return to caller (via stack)
```