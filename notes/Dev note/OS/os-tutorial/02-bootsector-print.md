#### Prerequisite knowledge

Interrupts: An **interrupt** is a mechanism that **temporarily pauses the current CPU execution** to handle an event — like a hardware signal or a software instruction.

**BIOS Interrupts (Real Mode Interrupts)**

In real mode (early boot, before the OS loads), BIOS provides **software interrupts** for basic I/O. These are known as **BIOS interrupt calls**.

For example:

- `INT 10h` – Video services (e.g., display text)
    
- `INT 13h` – Disk services (e.g., read/write sectors)
    
- `INT 16h` – Keyboard input
    
- `INT 19h` – Bootstrap loader
    

These let the bootloader interact with the hardware **without writing complex drivers**.
#### Make our previously silent boot sector print some text



