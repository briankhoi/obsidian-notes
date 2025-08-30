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
In contrast to the bit-level operators, our logic operators of &&, || and ! have the following rules:
- 0 is false
- anything non-zero is true
- always return 0 or 1
- early termination (short-circuiting)
![[Pasted image 20250828095040.png]]
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

In C by default numbers are signed - to make unsigned prepend "unsigned" before the type like unsigned int or add a "U" as a suffix such as "429U".

You can also cast unsigned to 2's complement and vice versa, such as 
```
int tx, ty;
unsigned ux, uy;
tx = (int) ux;
uy = (unsigned) ty;
```
There is also implicit casting from unsigned to 2's/vice-versa that happens during assignments and procedure calls:
```
// value of ux gets converted to signed int before being assigned to tx; tx stays as a signed int;
// however, this leads to two cases:
// if ux is within positive range of the signed int, its value is preserved and converted successfully
// if ux is above that range, its number gets re-interpreted to a negative number, e.g. 1000 would instead be -8 instead of interpreted as +8
tx = ux; 

// same behavior as above but for this one. positive number is fine, but for negative numbers they get interpreted as unsigned, so 1000 instead of -8 would be interpreted as +8
uy = ty;

// tx (signed) is converted to unsigned due to fun()'s parameter. then, the return value of fun() is casted to unsigned since fun() returns a signed but uy which it gets assigned to is unsigned
int fun(unsigned u);
uy = fun(tx);
```

![[Pasted image 20250828090452.png]]
Rule: "If you perform an operation (like > or \==) between a signed int and an unsigned int, the C compiler implicitly casts the signed int to an unsigned int." 
Also remember that -1 when converted to unsigned is the biggest number UMAX.
![[Pasted image 20250828090512.png]]
![[Pasted image 20250829194140.png]]
![[Pasted image 20250829194149.png]]
In general, casting from signed to unsigned or vice versa maintains the bit-pattern but reinterprets it, which can have unexpected effects

Sign extension: Make copies of the sign bit and extend it as much as needed.
![[Pasted image 20250829194347.png]]

Sign truncation: Drop top bits until truncated to desired size.
![[Pasted image 20250829194528.png]]
Numbers smaller in magnitude yield expected behavior because the higher order bits are used for padding and make no difference when truncated.

Unsigned addition: Binary addition and we discard any carry over past the amount of bits in the integer (i.e. final value is {sum} mod $2^w$ where $w$ represents how many bits the integer can hold)
![[Pasted image 20250829200901.png]]
For the last example, assuming 8-bit binary the max number is 256. However since the sum computed is above that 8-bit limit and instead a 9-bit integer, we remove the carryover higher order bit aka 446 mod 256 and get 190 as our final result.

Two's complement addition: It's the same as unsigned addition, but the results are interpreted differently (like two positive numbers can result into a negative number if it causes the high order bit to be 1 or two negative numbers can be added to get a positive number if high order bit is 0)

Multiplication
$u << k$ gives $u * 2^k$. 
![[Pasted image 20250829234532.png]]
Also note this is only true if it doesn't overflow, e.g. 4-bit int 1000 << 2 = 0 !!!

Division
$u >> k$ gives $\lfloor \frac{u}{2^k} \rfloor$. For unsigned this uses logical shift and for 2's complement/signed integers it uses arithmetic shift and rounds to the left of the number line, not toward zero (e.g. -3.5 rounded to -4 and 2.5 rounded to 2).

Unsigned division:
![[Pasted image 20250829235118.png]]

Signed division:
![[Pasted image 20250829235141.png]]

However, in C for signed division we don't want this behavior of always rounding to the left of the number line, especially for negative integers. We want to always round to zero. To achieve this, the C compiler detects if there is a negative number and if so, it adds a bias (does not do anything for positive numbers). This bias is aimed to correct -.25 rounding to -1 and instead round it to 0. This works by adding $2^k-1$ to our negative k-bit integer and doing the shift. This leads to the following cases for a 4-bit signed integer:
![[Pasted image 20250830011029.png]]


Endianness describes the order in which the bytes of a multi-byte number are physically stored in memory.
- **Big-Endian**: Stores the **most significant byte** (the "big end") at the lowest memory address. This is similar to how we write numbers.
- **Little-Endian**: Stores the **least significant byte** (the "little end") at the lowest memory address. This is used by most modern desktop processors.
This is a property of the hardware's memory layout. It does not affect single-byte data types like char or byte arrays like strings, as they have no internal byte order to consider.

As an example, say we are given the variable x with value 0x01234567. It's address (&x) is 0x100.

First, this means that the most significant byte (MSB) is 01 and the least significant byte (LSB) is 67.
![[Pasted image 20250830011327.png]]
Notice how starting at the beginning of the address, we see a different byte depending on the system's architecture. In the big endian system, it stores the number in the same order you would read it, while on a little endian system it stores LSB first at the starting address. Also observe that we are changing the order of the bytes in our multi-byte value, but NOT the order of the bits in each byte (e.g. in little endian, the 67 does not become 76, but rather stays 67).
### Terminology
- bit-width: width/length of a bit-vector
- C short is 2 bytes long
