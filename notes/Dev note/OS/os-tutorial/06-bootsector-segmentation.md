**Goal: learn how to address memory with 16-bit real mode segmentation**

This is done by using special registers: `cs`, `ds`, `ss` and `es`, for Code, Data, Stack and Extra (i.e. user-defined)

Beware: they are _implicitly_ used by the CPU, so once you set some value for, say, `ds`, then all your memory access will be offset by `ds`. [Read more here](http://wiki.osdev.org/Segmentation)
```
mov bx, 0x7c0 ; remember, the segment is automatically <<4 for you
mov ds, bx
; WARNING: from now on all memory references will be offset by 'ds' implicitly
```
For example, if `ds` is `0x4d`, then `[0x20]` actually refers to `0x4d0 + 0x20 = 0x4f0`

Hint: We cannot `mov` literals to those registers, we have to use a general purpose register before.
