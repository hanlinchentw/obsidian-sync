```
Start
  |
  v
+----------------------------+
|   🧾 Learned about BIOS &   |
|   x86 real mode (16-bit)  |
| - Memory layout (0x7C00)  |
| - Instruction set (16-bit)|
+----------------------------+
  |
  v
+----------------------------+
|   ✍️ Wrote 16-bit bootloader |
| - Using NASM              |
| - Printed to screen       |
| - Set up stack            |
+----------------------------+
  |
  v
+----------------------------+
|   📚 Learned memory I/O     |
| - Video memory at 0xb8000 |
| - Writing characters      |
+----------------------------+
  |
  v
+----------------------------+
|   💾 Learned disk loading    |
| - Load kernel from disk   |
| - Understand INT 0x13     |
| - Used `cat` to combine   |
+----------------------------+
  |
  v
+----------------------------+
|   ⚙️ Learned segmentation     |
| - Global Descriptor Table |
| - Segment selectors       |
| - GDT structure           |
+----------------------------+
  |
  v
+----------------------------+
|   🏁 Learned protected mode  |
| - Enable CR0 PE bit       |
| - Far jump                |
| - Segment register reload |
+----------------------------+
  |
  v
+----------------------------+
|   🔢 Switched to 32-bit mode |
| - [bits 32] in NASM       |
| - Setup 32-bit stack      |
| - Print in protected mode |
+----------------------------+
  |
  v
+----------------------------+
|   🧠 Learned about C kernel   |
| - Created `main()` in C    |
| - Printed via memory      |
| - Avoided `libc`          |
+----------------------------+
  |
  v
+----------------------------+
|   🧰 Built a cross-compiler   |
| - Targeted `i686-elf`     |
| - Without headers         |
| - Linked flat binaries    |
+----------------------------+
  |
  v
+----------------------------+
|   🎯 Controlled entry points |
| - Dummy function trick    |
| - Linker layout control   |
+----------------------------+
  |
  v
Next: What’s next?
- Interrupts / IDT
- Paging
- Syscalls
- File system
- Kernel shell
- Multitasking

```