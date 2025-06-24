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