
**Goal: Scroll the screen when the text reaches the bottom**

in `screen.c::print_char`
```c
   /* Check if the offset is over screen size and scroll */
   if (offset >= MAX_ROWS * MAX_COLS * 2)
   {
      int i;
      for (i = 1; i < MAX_ROWS; i++) {
         char *source = (char *)get_offset(0, i) + VIDEO_ADDRESS;
         char *dest = (char *)get_offset(0, i - 1) + VIDEO_ADDRESS;
         int nbytes = MAX_COLS * 2;
         memory_copy(source, dest, nbytes);
      }

      /* Blank last line */
      char *last_line = (char *)get_offset(0, MAX_ROWS - 1) + VIDEO_ADDRESS;
      for (i = 0; i < MAX_COLS * 2; i++)
         last_line[i] = 0;

      offset -= 2 * MAX_COLS;
   }
```
We override the old data by the data next to it, i.e, 
- `row 1 → row 0`
- `row 2 → row 1`
- ...
- `row 24 → row 23`
and leave a blank like