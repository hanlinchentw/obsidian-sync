### GDT - Global Descriptor Table

The **Global Descriptor Table** (**GDT**) is a binary data structure specific to the [IA-32](https://wiki.osdev.org/IA32_Architecture_Family "IA32 Architecture Family") and [x86-64](https://wiki.osdev.org/X86-64 "X86-64") architectures. It contains entries telling the CPU about memory [segments](https://wiki.osdev.org/Segmentation "Segmentation"). A similar [Interrupt Descriptor Table](https://wiki.osdev.org/Interrupt_Descriptor_Table "Interrupt Descriptor Table") exists containing [task](https://wiki.osdev.org/Task "Task") and [interrupt](https://wiki.osdev.org/Interrupts "Interrupts") descriptors.

> **GDT 就是 CPU 用來認識記憶體區塊的說明書**
- 告訴 CPU 哪一段記憶體是程式用的、哪一段是資料用的
- 每一段都可以設起點、長度、權限
- 在進入 protected mode 之前一定要設好 GDT，不然 CPU 沒辦法正確執行程式

gdt_start（標記 GDT 開始的位置）
```
gdt_start:
	dd 0x0 ; 4 bytes
	dd 0x0 ; 4 bytes
```
- GDT 的第一個項目一定要是 **空的（null descriptor）**，這是 Intel CPU 的規定
- 這 8 個位元組都設為 0，代表「無效的區段」


Entire implementation os GDT:
```
gdt_start: ; don't remove the labels, they're needed to compute sizes and jumps
    ; the GDT starts with a null 8-byte
    dd 0x0 ; 4 byte
    dd 0x0 ; 4 byte

; GDT for code segment. base = 0x00000000, length = 0xfffff
; for flags, refer to os-dev.pdf document, page 36
gdt_code: 
    dw 0xffff    ; segment length, bits 0-15
    dw 0x0       ; segment base, bits 0-15
    db 0x0       ; segment base, bits 16-23
    db 10011010b ; flags (8 bits)
    db 11001111b ; flags (4 bits) + segment length, bits 16-19
    db 0x0       ; segment base, bits 24-31

; GDT for data segment. base and length identical to code segment
; some flags changed, again, refer to os-dev.pdf
gdt_data:
    dw 0xffff
    dw 0x0
    db 0x0
    db 10010010b
    db 11001111b
    db 0x0

gdt_end:

; GDT descriptor
gdt_descriptor:
    dw gdt_end - gdt_start - 1 ; size (16 bit), always one less of its true size
    dd gdt_start ; address (32 bit)

; define some constants for later use
CODE_SEG equ gdt_code - gdt_start
DATA_SEG equ gdt_data - gdt_start
```