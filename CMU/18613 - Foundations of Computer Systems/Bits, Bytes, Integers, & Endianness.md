We use voltage to represent two states, binary 0's and 1's. We don't measure exactly, just check if it's high enough to be "on" and low enough to be "off."
![[Pasted image 20250827183843.png]]

By encoding/interpreting sets of bits (bit = 0 or 1) in different ways, we can represent different things. For example, counting in base2:
![[Pasted image 20250827184024.png]]

Any power of two base allows us to represent numbers densely such that fewer digits are required e.g. octal (base8) and hexadecimal (base16).

Hexadecimal uses 0-9 and A-F for 10-15; C prefixes hexadecimal with "0x" to not confuse them with decimal numbers, e.g. 0xFA1D37B or 0xfa1d37b (same thing).

### Boolean Algebra
Operates on two states: 1 = true, 0 = false
![[Pasted image 20250828082116.png]]
You can also operate on bit vectors and operations are applied bit per bit (bitwise)
![[Pasted image 20250828082146.png]]

Imagine you have a bit vector $a$, where $a_j=1$ if $j \in A$ . This essentially means you have like $a = 01101010$ where instead of this representing a number, it represents a set of states where a\[0] = state 0, a\[1] = state 1, etc. (e.g. you're taking class attendance for class size of 4, so you use 0000 where the i-th bit represents if the i-th student attended class or not.)
Then, we have the following operations on our sets.
![[Pasted image 20250828094338.png]]
![[Pasted image 20250828083333.png]]

These bit-level operators of &, |, ~, and ^ are in C and can be applied to any "integral" data type like long, int, short, char, unsigned. Its arguments are bit vectors and applied bit-wise.
![[Pasted image 20250828094842.png]]
v delete
![[Pasted image 20250828083444.png]]

In contrast to the bit-level operators, our logic operators of &&, || and ! have the following rules:
- 0 is false
- anything non-zero is true
- always return 0 or 1
- early termination (short-circuiting)
![[Pasted image 20250828095040.png]]
v delete
![[Pasted image 20250828083452.png]]
### Shift Operations
Left Shift: $x << y$ 
This shifts a bit-vector $x$, $y$ positions to the left and throws away the extra bits on the left. The right side is filled with 0's.

Right Shift: $x >> y$
This shifts a bit-vector $x$, $y$ positions to the left and throws away the extra bits on the left. Crucially there are two modes: logical shift fills with 0's on the left, and arithmetic shift replicates the most significant bit on the left.

Undefined behavior: Shift amount < 0 or >= word size
![[Pasted image 20250828083825.png]]

### Binary Number Lines
The number of bits in the data type size determines the number of points on the number line (no infinite numbers!)
![[Pasted image 20250828083952.png]]

If you exceed the size of your number line (e.g. 3-bit 111 and you add 1 to it), since there isn't an extra bit to accommodate the carry-over, you lose the leading 1 and get overflow.

Example: 111 + 001 = 1000 (but the leading 1 gets dropped since it's 3 bit!)

More overflow examples - notice how the arithmetic "wraps around" when it overflows:
![[Pasted image 20250828084329.png]]

One's complement: Just take the compliment of the number, and highest order bit is the sign. So for example -34 in 1's complement is ~34 aka complement of 34.

Two's complement: Use highest order bit as a sign, 0 = positive, 1 = negative. If positive number just read it like binary. If negative, take the complement of the number and add 1 to get the binary/decimal value (but don't forget about the sign!).

Ranges:
- Unsigned goes from 0 ($U_{min}$) to $2^{w}-1$ ($U_{max}$)
- 2's complement goes from $-2^{w-1}$ ($T_{min}$) to $2^{w-1}-1$ ($T_{max}$)
![[Pasted image 20250828101708.png]]
In general with 2's complement, -x = ~x + 1, with the only exception being the minimum number in 2's complement due to the ranges

take notes
![[Pasted image 20250828085824.png]]
![[Pasted image 20250828090125.png]]

In C by default numbers are signed - to make unsigned prepend "unsigned" before the type like unsigned int or add a "U" as a suffix such as "429U".
![[Pasted image 20250828090452.png]]
![[Pasted image 20250828090512.png]]
-1 > 0 cause casts to unsigned 
![[Pasted image 20250828090739.png]]

numberline rounds by division always to the left no matter neg or pos so 2.5 -> 2 and -2.5 -> -3
### Terminology
- bit-width: width/length of a bit-vector
- C short is 2 bytes long
