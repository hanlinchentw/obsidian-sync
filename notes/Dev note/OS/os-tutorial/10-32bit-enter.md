**Goal: Enter 32-bit protected mode and test our code from previous lessons**

To jump into 32-bit mode:
1. Disable interrupts
2. Load our GDT
3. Set a bit on the CPU control register `cr0`
4. Flush the CPU pipeline by issuing a carefully crafted far jump
5. Update all the segment registers
6. Update the stack
7. Call to a well-known label which contains the first useful code in 32 bits