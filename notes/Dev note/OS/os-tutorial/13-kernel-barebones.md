Executable and Linkable Format，縮寫ELF
大致可以分成幾個部份：
1. 記錄基本資料的表頭 (ELF header)
2. 記錄程式該怎麼載入記憶體的 program header table
3. 記錄檔案內的分段的 section header table
4. 數個分段 (section)

**Goal: Create a simple kernel and a bootsector capable of booting it**

Create a basic kernal.c

```c
// kernal.c
/* This will force us to create a kernel entry function instead of jumping to kernel.c:0x00 */
void dummy_entry() {}

void main() 
{
	char *video_memory = (char*) 0xb8000;
	*video_memory = 'X';
}
```

dummy function that does **nothing**. That function will force us to create a kernel entry routine which does not point to byte 0x0 in our kernel, but to an actual label which we know that launches it. In our case, function `main()`.

Let compile our kernal
```
i686-elf-gcc -ffreestanding -c kernel.c -o kernel.o
```

That routine is coded on `kernel_entry.asm`. Read it and you will learn how to use `[extern]` declarations in assembly. To compile this file, instead of generating a binary, we will generate an `elf` format file which will be linked with `kernel.o`

```asm
// kernal_entry.asm
[bits 32]
[extern main]
call main
jmp $

```

`nasm kernel_entry.asm -f elf -o kernel_entry.o`

## The linker

A linker is a very powerful tool and we only started to benefit from it.

To link both object files into a single binary kernel and resolve label references, run:

`i686-elf-ld -o kernel.bin -Ttext 0x1000 kernel_entry.o kernel.o --oformat binary`

Notice how our kernel will be placed not at `0x0` in memory, but at `0x1000`. The bootsector will need to know this address too.

## The bootsector
```asm
[org 0x7c00]
KERNEL_OFFSET equ 0x1000
	mov [BOOT_DRIVE], dl
	mov bp, 0x9000
	mov sp, bp

    mov bx, MSG_REAL_MODE
    call print
    call print_nl

    call load_kernel
    call switch_to_pm
    jmp $

%include "./boot_print.asm"
%include "./boot_print_hex.asm"
%include "./boot_disk.asm"
%include "./32bit-gdt.asm"
%include "./32bit-print.asm"
%include "./32bit-switch.asm"

[bits 16]
load_kernel:
	mov bx, MSG_LOAD_KERNEL
	call print
	call print_nl

	mov bx, KERNEL_OFFSET
	mov dh, 2
	mov dl, [BOOT_DRIVE]
	call disk_load
	ret
[bits 32]
BEGIN_PM:
	mov ebx, MSG_PROT_MODE
	call print_string_pm
	call KERNEL_OFFSET
	jmp $
	
BOOT_DRIVE db 0
MSG_REAL_MODE db "Starting in 16-bit Real mode", 0
MSG_PROT_MODE db "Landed in 32-bit Protected mode", 0
MSG_LOAD_KERNEL db "Loading kernel into memory", 0

times 510 - ($-$$) db 0
dw 0xaa55
```

Compile it with `nasm bootsect.asm -f bin -o bootsect.bin`

## Putting it all together

Now what? We have two separate files for the bootsector and the kernel?

Can't we just "link" them together into a single file? Yes, we can, and it's easy, just concatenate them:

`cat bootsect.bin kernel.bin > os-image.bin`
## Run!
You can now run `os-image.bin` with qemu.
use `qemu-system-i386 -fda os-image.bin`

You will see four messages:
- "Started in 16-bit Real Mode"
- "Loading kernel into memory"
- (Top left) "Landed in 32-bit Protected Mode"
- (Top left, overwriting previous message) "X"