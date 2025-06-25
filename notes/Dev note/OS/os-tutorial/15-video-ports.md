**Goal: Learn how to use the VGA card data ports**

## VGA Text Buffer Basics

- The VGA text buffer starts at **memory address `0xB8000`**.
- Each **character cell** on screen is **2 bytes**:
    - 1 byte for the character (e.g., `'X'`)
    - 1 byte for the color attribute (e.g., `0x0F` = white on black)
- The screen is usually **80 columns × 25 rows**, so 2000 cells → 4000 bytes total.

## How Cursor Position Is Stored in VGA

The VGA hardware keeps the current cursor position in **video memory coordinates**, not bytes. For example:

- Top-left = position `0`
- Next cell right = `1`
- 2nd row start = `80`, and so on.

This position is a **16-bit value**, stored in two 8-bit registers:

- Register **0x3D4** is the control register.
- Register **0x3D5** is the data register.

You must write to 0x3D4 to choose which **part** of the cursor position to read/write, then read/write the data from 0x3D5.

```c
#include "../drivers/ports.h"

void main() 
{
	/* Screen cursor position: ask VGA control register (0x3d4) for bytes
     * 14 = high byte of cursor and 15 = low byte of cursor. */
	port_byte_out(0x3d4, 14);  /* Requesting byte 14: high byte of cursor pos */

	int position = port_byte_in(0x3d5); /* Data is returned in VGA data register (0x3d5) */
	position = position << 8; /* high byte */

	port_byte_out(0x3d4, 15); /* requesting low byte */
	position += port_byte_in(0x3d5);

	char *vga = (char *) 0xb8000;

	/*Each **character cell** on screen is **2 bytes**:
    * 1 byte for the character (e.g., `'X'`)
    * 1 byte for the color attribute (e.g., `0x0F` = white on black) */
	int offset_from_vga = position;
	vga[offset_from_vga] = 'X';
	vga[offset_from_vga + 1] = 0x0f;
}
```

## ✅ Summary

| Step | Action                                                            |
| ---- | ----------------------------------------------------------------- |
| 1.   | Write `14` to port `0x3D4` → selects high byte of cursor position |
| 2.   | Read from port `0x3D5` → gets high byte                           |
| 3.   | Write `15` to port `0x3D4` → selects low byte                     |
| 4.   | Read from port `0x3D5` → gets low byte                            |
| 5.   | Combine to get full 16-bit position (`row * 80 + col`)            |
| 6.   | Multiply by 2 to get byte offset                                  |
| 7.   | Write to `0xB8000 + offset` to modify screen contents             |
 