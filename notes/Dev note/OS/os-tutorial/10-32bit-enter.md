**Goal: Enter 32-bit protected mode and test our code from previous lessons**

To jump into 32-bit mode:
1. Disable interrupts
	- `cli` — Disable Interrupts
	- **Why?** Because while switching modes, you don’t want an interrupt (which uses 16-bit mode handlers) to fire and crash the CPU.
2. Load our GDT
	- `lgdt [gdt_descriptor]`
	- **GDT (Global Descriptor Table)** defines memory segments (like code/data segments) with base, size, and access rights.
3. Set a bit on the CPU control register `cr0`
	- **CR0** is a CPU control register.
	- Bit 0 of `cr0` is the **PE (Protection Enable)** bit.
	- Setting this bit transitions the CPU into **protected mode** — 32-bit flat addressing.
4. Flush the CPU pipeline by issuing a carefully crafted far jump
5. Update all the segment registers
6. Update the stack
7. Call to a well-known label which contains the first useful code in 32 bits


The code:
```assembly
[bits 16]
switch_to_pm:
    cli ; 1. disable interrupts
    lgdt [gdt_descriptor] ; 2. load the GDT descriptor
    mov eax, cr0 ; read current control register
    or eax, 0x1  ; 3. set 32-bit mode bit in cr0
    mov cr0, eax ; write it back
    jmp CODE_SEG:init_pm ; 4. far jump by using a different segment

[bits 32]
init_pm: ; we are now using 32-bit instructions
    ; These registers still contain old 16-bit segment values.
    ; You set all of them to the data segment selector from the GDT so that memory accesses work as intended under protected mode.
    mov ax, DATA_SEG ; 5. update the segment registers
    mov ds, ax
    mov ss, ax
    mov es, ax
    mov fs, ax
    mov gs, ax

    mov ebp, 0x90000 ; 6. update the stack right at the top of the free space
    mov esp, ebp

    call BEGIN_PM ; 7. Call a well-known label with useful code
```

## Stack in 16-bit vs 32-bit

|Register|Size|Mode|Name|
|---|---|---|---|
|`sp`|16-bit|Real mode|Stack Pointer|
|`bp`|16-bit|Real mode|Base Pointer|
|`esp`|32-bit|Protected mode|Stack Pointer|
|`ebp`|32-bit|Protected mode|Base Pointer|