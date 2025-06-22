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
特別注意：
1. Register 是共用的， side effects
2. 