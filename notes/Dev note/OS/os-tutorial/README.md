# OS Tutorial Learning Notes

This folder contains my personal notes and experiments while working through the excellent OS development tutorial by Carlos Fenollosa.

## 📚 Source Tutorial

**Repository**: [cfenollosa/os-tutorial](https://github.com/cfenollosa/os-tutorial/tree/master)

This tutorial teaches how to create an operating system from scratch, covering everything from basic bootloaders to memory management and file systems.

## 📁 Folder Structure

```
├── 01-bootsector/          # Basic bootloader concepts
├── 02-bootsector-print/    # Printing to screen from bootloader
├── 03-bootsector-memory/   # Memory addressing and segmentation
├── 04-bootsector-stack/    # Stack operations
├── 05-bootsector-functions/ # Function calls in assembly
├── 06-bootsector-segmentation/ # Memory segmentation
├── 07-bootsector-disk/     # Reading from disk
├── 08-32bit-print/         # 32-bit mode and VGA
├── 09-32bit-gdt/          # Global Descriptor Table
├── 10-32bit-enter/        # Switching to 32-bit protected mode
├── 11-kernel-crosscompiler/ # Setting up cross compiler
├── 12-kernel-c/           # First C kernel
├── 13-kernel-barebones/   # Kernel basics
├── 14-checkpoint/         # Milestone checkpoint
├── 15-video-ports/        # VGA and text mode
├── 16-video-driver/       # Video driver implementation
├── 17-video-scroll/       # Screen scrolling
├── 18-interrupts/         # Interrupt handling
├── 19-interrupts-irqs/    # Hardware interrupts
├── 20-interrupts-timer/   # Timer interrupts
├── 21-shell/              # Basic shell implementation
├── 22-malloc/             # Dynamic memory allocation
├── 23-fixes/              # Bug fixes and improvements
├── experiments/           # My own experiments and modifications
└── notes/                 # General learning notes and references
```

## 🎯 Learning Goals

- [ ]  Understand x86 boot process and BIOS interactions
- [ ]  Learn assembly language fundamentals
- [ ]  Master memory management concepts (GDT, paging, heap)
- [ ]  Implement interrupt handling and device drivers
- [ ]  Build a basic kernel with C integration
- [ ]  Create a simple shell and command processing
- [ ]  Understand file systems and disk operations

## 🔧 Development Environment

**Tools Used:**

- **Emulator**: QEMU for testing the OS
- **Assembler**: NASM for assembly code
- **Compiler**: GCC cross-compiler for C code
- **Debugger**: GDB for debugging
- **Build System**: Make

**Setup Commands:**

bash

```bash
# Install required tools
brew install qemu nasm

# Test QEMU installation
qemu-system-x86_64 --version
```

## 📝 Progress Tracking

| Lesson               | Status | Date Completed | Key Learnings                  |
| -------------------- | ------ | -------------- | ------------------------------ |
| 01-bootsector        | ✅      | 2025-06-20     | Basic boot process             |
| 02-bootsector-print  | 🔄     | -              | BIOS interrupts, string output |
| 03-bootsector-memory | ⏳      | -              | Memory addressing, real mode   |
| ...                  | ⏳      | -              | ...                            |

**Legend:**

- ✅ Completed
- 🔄 In Progress
- ⏳ Not Started
- ❌ Needs Review

## 💡 Key Concepts Learned

### Boot Process

- BIOS loads first **512** bytes from bootable device
	- The bootloader is limited to 512 bytes and acts as a standard between the BIOS and the OS.
- Magic number `0xAA55` required at end of boot sector
- Real mode vs protected mode differences
### Assembly Programming

- x86 register usage and calling conventions
- Memory segmentation and addressing modes
- BIOS interrupt services (INT 0x10, INT 0x13, etc.)

### Kernel Development

- Cross-compilation setup and linking
- C and assembly integration
- Hardware abstraction principles

## 🚀 Experiments & Extensions

This section documents my own modifications and experiments beyond the tutorial:

- **Enhanced VGA Driver**: Added support for different text colors and graphics modes
- **Improved Shell**: Extended command set with file operations
- **Memory Debugger**: Tools for inspecting memory layout and usage
- **Custom Bootloader**: Alternative bootloader with additional features

## 📖 Additional Resources

- [OSDev Wiki](https://wiki.osdev.org/) - Comprehensive OS development resource
- [Intel Software Developer Manuals](https://software.intel.com/content/www/us/en/develop/articles/intel-sdm.html)
- [AMD64 Architecture Manual](https://www.amd.com/system/files/TechDocs/40332.pdf)