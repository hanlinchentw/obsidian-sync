### GDT - Global Descriptor Table

The **Global Descriptor Table** (**GDT**) 是一個 **IA-32** 和 **x86-64 架構專用** 的二進位資料結構。  
它包含了用來告訴 CPU 各種記憶體**區段（segment）**的資訊。

類似的還有一個叫做 [Interrupt Descriptor Table（IDT）](https://wiki.osdev.org/Interrupt_Descriptor_Table) 的東西，它是給 **中斷（interrupt）** 和 **任務切換（task switching）** 使用的。

> 💡 **GDT 就是 CPU 用來認識記憶體區塊的說明書**
> - 告訴 CPU 哪一段記憶體是程式用的、哪一段是資料用的    
> - 每一段可以自訂起點、長度、存取權限（是否可執行、讀寫等）

### 🧱 GDT 定義說明

gdt_start（標記 GDT 開始的位置）
```
gdt_start:
	dd 0x0 ; 4 bytes
	dd 0x0 ; 4 bytes
```
- GDT 的第一個項目一定要是 **空的（null descriptor）**，這是 Intel CPU 的規定
- 這 8 個位元組都設為 0，代表「無效的區段」

Code Segment（程式區段定義）
```
gdt_code: 
    dw 0xffff        ; 區段大小的低 16 位元（0xFFFF）
    dw 0x0           ; 區段基底位址的低 16 位元
    db 0x0           ; 區段基底位址的第 16~23 位元
    db 10011010b     ; 權限與型態（這是「可執行、可讀、已存在」的程式區段）
    db 11001111b     ; 區段大小高 4 位 + flag（例如 granularity 設為 4KB 單位）
    db 0x0           ; 區段基底位址的第 24~31 位元
```
- 這段設定一個 **基底為 `0x00000000`、大小為 `0xFFFFF`（實際大小 4GB）** 的 code segment 
- 使用了 granularity 為 4KB，因此實際大小是 `0xFFFFF * 4K = 約 4GB`
- 可用於 CS（程式碼段）切換進保護模式後的跳
- 在 `flags`（第 6 個 byte）中，有一個叫 **G 位元（Granularity Bit）**：

| G 值 | limit 單位        |
| --- | --------------- |
| 0   | bytes（1 位元組）    |
| 1   | 4KB（4096 bytes） |
```
0xFFFFF = F F F F F（每個 F 是 4 個 bit）
        = 5 個 F → 5 × 4 bits = 20 bits
        = 1111 1111 1111 1111 1111（二進位）
        = 2^20 - 1
```

```
0xFFFFF * 4KB
= (2^20 - 1) * 4096
= 約 4GB（準確來說是 4GB - 4KB）
```

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

Entire GDT implementation:
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
