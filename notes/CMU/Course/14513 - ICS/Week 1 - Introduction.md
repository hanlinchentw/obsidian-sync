### Great Reality #1
ints are not integers, Floats are not Reals
#### Example 1: is x^2 >= 0?
<span style="color:red">Not true when integer overflows!</span>
What if we square floats => we might get something that is closed to the real answer.
=> Float precision issue
#### Example 2: is (x+y)+z = x+(y+z)?
Unsigned & signed Ints: Yes
Float's:
1e20 + -1e20 + 3.14 => 3.140000000000001 (approximately) 

=> Cannot assume all "usual" mathematical properties
### Great Reality #2
You've gotten to know assembly
### Great Reality #3
| Random Access Memory (RAM) is an unphysical abstraction
- Memory is not unbounded
	- Must be allocated and managed
- Memory reference bugs especially pernicious
	- Effects and distant in both time and space
- Memory performance is not uniform
	- Cache and virtual memory effects can greatly affect program performance
C and C++ do not provide any memory protection
- Out of Bounds array reference
- invalid pointer values
- Abuses of malloc/free
### Great Reality #4
| performance than asymptotic complexity `O(n)`
- Constant factors matter too
- And even exact op count does not predict performance
- Must understand system to optimize performance
```
for (int i = 0; i < 2048; i++) {
	for (int j = 0; i < 2048; j++) {
		dst[i][j] = src[i][j];
	}
}
=> 4 ms
for (int j = 0; j < 2048; j++) {
	for (int i = 0; i < 2048; i++) {
		dst[i][j] = src[i][j];
	}
}
=> 81 ms
```
### Great Reality #5
| Computers do more than execute programs
- They need to get data in and out
- They communicate with each other






