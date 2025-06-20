## Prerequisite knowledge
#### Assembler
**Assembler: program that translates assembly language into machine code.**

**Assembly Code (human-readable mnemonics):**
```assembly
mov ax, 0x1234    ; Move the value 0x1234 into register AX
add ax, bx        ; Add the value in register BX to AX
int 0x10          ; Call BIOS interrupt 0x10
```

**Machine Code (what the assembler produces that the CPU can execute directly):**
```
B8 34 12    ; mov ax, 0x1234
01 D8       ; add ax, bx  
CD 10       ; int 0x10
```

**The Assembly Process**
```
Assembly Source Code (.asm)
          ↓
    [ASSEMBLER] (like NASM)
          ↓
    Machine Code (.bin/.o)
          ↓
      [LINKER] (optional)
          ↓
   Executable Program
```

#### **BIOS** 
stands for **Basic Input/Output System** - it's the first software that runs when you turn on your computer, before any operating system loads.

## **Create a file which the BIOS interprets as a bootable disk**

When the computer boots, the BIOS doesn't know how to load the OS, so it delegates that task to the boot sector. Thus, the boot sector must be placed in a known, standard location. That location is the first sector of the disk (cylinder 0, head 0, sector 0) and it takes **512** bytes.
To make sure that the "disk is bootable", the BIOS checks that bytes 511 and 512 of the alleged boot sector are bytes `0xAA55`.

This is the simplest boot sector ever:
```
e9 fd ff 00 00 00 00 00 00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
[ 29 more lines with sixteen zero-bytes each ]
00 00 00 00 00 00 00 00 00 00 00 00 00 00 55 aa
```
## 工作流程
```
1. BIOS 啟動 → 2. 尋找可開機磁碟 → 3. 讀取第一個 sector (512 bytes)
    ↓
2. 檢查是否有 0x55AA (bootable) → 5. 載入到記憶體 0x7C00 → 6. 跳轉執行
```

## Simplest boot sector ever

You can either write the above 512 bytes with a binary editor, or just write a very simple assembler code:

```assembly
; Infinite loop (e9 fd ff)
loop:
    jmp loop 

; Fill with 510 zeros minus the size of the previous code
times 510-($-$$) db 0
; Magic number
dw 0xaa55 
```

To compile: `nasm -f bin boot_sect_simple.asm -o boot_sect_simple.bin`

`qemu-system-x86_64 boot_sect_simple.bin`
![[01-boot.png]]