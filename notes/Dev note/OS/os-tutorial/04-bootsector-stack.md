## Knowledge
**Stack in Real-Mode Assembly:**
- **SP (Stack Pointer)** points to the top of the stack.
- **BP (Base Pointer)** is often used to reference values relative to the base of the stack.
- The stack **grows downward** (i.e., pushing values decrements `SP`).
- Stack is word-addressed: `push` and `pop` operate on **2 bytes (16 bits)** at a time.
- **BIOS video service** (interrupt `0x10`) uses `AL` to specify **which character to print** — but **only for function `AH = 0x0E`** (teletype output)

**Goal: Learn how to use the stack**