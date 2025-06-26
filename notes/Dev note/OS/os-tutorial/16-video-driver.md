**Goal: Write strings on the screen**

print
```c
#include "ports.h"

void kprint(char *message)
{
	char *vga =  (char *)0xb8000;

	int offset;
	port_byte_out(0x3d4, 14);
	offset = port_byte_in(0x3d5) << 8;
	port_byte_out(0x3d4, 15);
	offset |= port_byte_in(0x3d5);

	offset *= 2;

	int i = 0;
	while (message[i] != 0) {
		vga[offset] = message[i];
		vga[offset + 1] = 0x0f // white on black
		offset += 2;
	}
}
```


Advanced features:
print at 