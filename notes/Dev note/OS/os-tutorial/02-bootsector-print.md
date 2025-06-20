#### Prerequisite knowledge

Interrupts: An **interrupt** is a mechanism that **temporarily pauses the current CPU execution** to handle an event — like a hardware signal or a software instruction.

**BIOS Interrupts (Real Mode Interrupts)**

In real mode (early boot, before the OS loads), BIOS provides **software interrupts** for basic I/O. These are known as **BIOS interrupt calls**.

For example:
- `INT 10h` – Video services (e.g., display text    
- `INT 13h` – Disk services (e.g., read/write sectors
- `INT 16h` – Keyboard input
- `INT 19h` – Bootstrap loader

These let the bootloader interact with the hardware **without writing complex drivers**.

Registers hold:
- **Data** (like numbers or instructions)
- **Addresses** (memory locations    
- **Control information** (flags, state, etc.)

They’re **much faster than RAM**, but also much smaller.

## 🛠️ Why Are Registers Important in Booting?

- **Bootloaders** use them to talk to BIOS (via `INT` instructions)
- **Assembly code** manipulates them constantly
- **OS kernels** set them up for task switching, interrupt handling, memory management
#### Make our previously silent boot sector print some text

In following example, I'm going to write each character of the "Hello" into the register `al` (lower part of ax), the bytes `0x0e` into `ah` (the higher part of `ax`) and raise interrupt `0x10` which is a general interrupt for video services (e.g., display texts).

`0x0e` on `ah` tells the video interrupt that the actual function we want to run is to 'write the contents of `al` into tty mode'.

```assembly
mov ah, 0x0e ; tty mode
mov al, 'H'
int 0x10
mov al, 'e'
int 0x10
mov al, 'l'
int 0x10
int 0x10 ; 'l' is still on al, remember?
mov al, 'o'
int 0x10

jmp $ ; jump to current address = infinite loop

; padding and magic number
times 510 - ($-$$) db 0
dw 0xaa55 
```
![[截圖 2025-06-20 下午6.07.19.png]]