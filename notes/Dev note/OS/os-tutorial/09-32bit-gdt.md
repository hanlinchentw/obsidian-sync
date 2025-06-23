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

Code Segment（程式區段定義）
```
gdt_code: 
    dw 0xffff        ; 區段大小的低 16 位元（0xFFFF）
    dw 0x0           ; 區段基底位址的低 16 位元
    db 0x0           ; 區段基底位址的第 16~23 位元
    db 10011010b     ; 權限與型態（這是「可執行、可讀、已存在」的程式區段）
    db 11001111b     ; 區段大小高 4 位 + 旗標（例如 granularity 設為 4KB 單位）
    db 0x0           ; 區段基底位址的第 24~31 位元
```
- 這段設定了一個 **基底為 0x00000000、大小為 0xFFFFF（1MB）** 的程式段
- `10011010b`：代表這是 **code segment**、在 **ring 0（核心權限）**、是 **可執行並可讀取** 的
- `11001111b`：代表 segment 用 4KB 為單位（實際大小是 0xFFFFF * 4KB）

Data Segment（資料區段定義）
```
gdt_data:
    dw 0xffff
    dw 0x0
    db 0x0
    db 10010010b     ; 權限與型態（這是「可讀寫」的資料區段）
    db 11001111b
    db 0x0
```
- 跟 code segment 幾乎一樣，但這個是 **資料用的**
- `10010010b` 表示這是 **data segment**、在 **ring 0**、**可讀寫**，但不能執行

GDT Descriptor（告訴 CPU GDT 的大小與位址）
```
gdt_descriptor:
    dw gdt_end - gdt_start - 1 ; GDT 的長度（單位是位元組），要 -1 是因為 Intel 要求
    dd gdt_start               ; GDT 在記憶體中的起始位址
```
`lgdt` 指令會用這個結構來載入 GDT

定義 segment selector（讓你之後可以方便使用）
```
CODE_SEG equ gdt_code - gdt_start
DATA_SEG equ gdt_data - gdt_start
```

這會讓你之後可以這樣寫：
```
jmp CODE_SEG:label
mov ds, DATA_SEG
```
其中這兩個值是 GDT 中每個 segment 的 offset（通常是 0x08 和 0x10）