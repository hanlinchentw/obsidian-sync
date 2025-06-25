**Goal: Learn how to use the VGA card data ports**

## VGA Text Buffer Basics

- The VGA text buffer starts at **memory address `0xB8000`**.
- Each **character cell** on screen is **2 bytes**:
    - 1 byte for the character (e.g., `'X'`)
    - 1 byte for the color attribute (e.g., `0x0F` = white on black)
- The screen is usually **80 columns × 25 rows**, so 2000 cells → 4000 bytes total.


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

	int offset_from_vga = position;
	char *vga = (char *) 0xb8000;
	vga[offset_from_vga] = 'X';
	vga[offset_from_vga + 1] = 0x0f; // White on black
}
```