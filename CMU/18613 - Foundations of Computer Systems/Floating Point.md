How do we represent fractional numbers?

Fractional Binary Numbers
One approach is to use fractional binary numbers, where we introduce a decimal dot and all numbers to the right are $2^{-i}$ starting with i=1. 
![[Pasted image 20250904113458.png]]

However, this has 2 limitations:
- we can only exactly represent numbers of the form x/2^k
- just one setting of binary point within the w bits, so limited range of numbers

### Floating Point
Instead, we opt for a binary scientific notation approach, using the formula $v = (-1)^s * M * 2^E$, where v is the decimal value.
![[Pasted image 20250904122817.png]]
![[Pasted image 20250904114253.png]]
In this example, we translate 15213 to binary, which gives $11101101101101_2$. After, we move the decimal from the right to the left and leave a 1, so $1.1101101101101_2$. Since we moved 13 spots, our exponent is 13 giving us $2^{13}$. Putting this all together, we get the equation on the top right. However note this is the decimal representation and not the encoding! 

IEEE Floating Point 
IEEE Standard 754 is a uniform standard for floating point arithmetic with support for rounding, underflow, and overflow.

It's goal is to give a lot of precision for small values/densely pack them, and have increasingly bigger steps the larger you go. 
![[Pasted image 20250904121032.png]]

It supports two main precision types but can be extended to half precision or quad precision:
![[Pasted image 20250904121015.png]]

There are 3 types of floating point numbers:
- denormalized
- normalized
- special
![[Pasted image 20250904121216.png]]

### Normalized Values
Normalized values happen when exp != all 0's and != all 1's.
Since the exp part in the floating point encoding has to be unsigned, but the actual exponent we use in $2^E$ can be signed (so negative & positive), we encode the unsigned $exp$ as a biased value following the equation $exp = E + bias$, which allows us to represent E's negative number into a positive $exp$ format, which at runtime can be decoded back into the actual negative $E$ number using the same rearranged formula of $E = exp - bias$. 

The bias for a k-bit exp value is $2^{k-1}-1$. This gives us the following ranges, where the bias for single precision is 127 and double is 1023. The image below also gives the new range of values:
![[Pasted image 20250904123732.png]]
For example, exp = 1 would equal E = -126. Because in scientific notation, the range of the significand is always \[1.0, 2.0) and also exp != 0 for normalized values, we know that there will at least be a one. So thus we encode our significand with an "implied" 1, meaning we omit it from the encoding then add it back at decode time because we know it's always going to be there and it helps us save 1 bit of space.

So for M = 1.xxxxx, we only encode the xxxxx bits in the frac field. frac can range from all 0's when M = 1.0, and all 1's when M = $2.0 - \epsilon$ (this represents all 1's so like 1.11111...) but the decimals don't sum up to 2 but super close which is what the epsilon is for. So essentially:
1. The system first checks the exp field. If it sees a pattern that is not all zeros and not all ones, it knows it's dealing with a normalized number.
2. Because it has identified the number as normalized, it then knows how to interpret the frac field: it must prepend the "free" leading 1 to the bits from the frac field to assemble the full significand, M.
![[Pasted image 20250904125405.png]]

### Denormalized Values
Denormalized values occur when exp = all 0's. 

The exponent value $E$ is represented by $E=1-Bias$ and similar to normalized values, the significand is coded with an implied bit, in this case an implied 0, so M = 0.xxxxx gets encoded as xxxxx. 

There are two types of denormalized values:
- When both exp and frac = all 0's. This represents the zero value. Note that there can be +0 and -0 due to the sign bit.
- When exp = all 0's but frac != all 0's. These numbers represent numbers closest to 0.0 and are evenly spaced apart, in contrast to normalized values which increase in distance the further you stray from 0.0

### Special Values
Special values occur when exp = all 1's. 

There are two types of special values:
- When exp = all 1's and frac = all 0's, this represents the value of infinity and the result of an operation that overflows. This infinity can be both positive and negative (e.g. 1.0/0.0 = −1.0/−0.0 = +infinity, 1.0/−0.0 = −infinity)
- When exp = all 1's and frac != all 0's (so basically a non-zero number. this includes 1. NaN is NOT all 1's for both exp and frac it can be any non-zero value for frac). This represents the NaN (not a number) value, which represents cases when no numeric value can be determined (e.g. sqrt(-1))

![[Pasted image 20250904235019.png]]

### C Floating Point Decoding Examples
![[Pasted image 20250904234802.png]]
![[Pasted image 20250904234907.png]]
+ 1-3=-2
+ 1.10000
+ 1.1 
+ 1.5 * 2^-2
+ 3/2 * 1/4
+ 3/8
### Properties
The IEEE 754 Format was deliberately designed so that a computer's hardware can compare two positive floating-point numbers very quickly. It can do this by taking a shortcut: instead of doing complicated math, it just treats their 32-bit (or 64-bit) patterns as if they were simple unsigned integers and compares those instead.

For positive numbers, if one number's integer representation is bigger than another's, its floating-point value is also bigger. This makes comparisons extremely fast.
![[Pasted image 20250905001249.png]]
![[Pasted image 20250905001331.png]]

![[Pasted image 20250905000559.png]]
![[Pasted image 20250905000829.png]]
### Rounding
Since computers have a limited number of bits to store a number, they can't always represent the exact result of a calculation.

Thus, for floating point arithmetic and multiplication, we follow a two-step process:
1. Compute the exact result
2. Fit result into desired precision
	1. Overflow if exponent too large
	2. Round to fit into $frac$

In general, C uses a round-to-nearest-even approach for rounding since other methods such as always round up are statistically biased. This approach means that when a number is exactly halfway between two choices, you round to the option whose last digit is even.
![[Pasted image 20250905215615.png]]
For the bottom two examples, since they're both in the middle we round. The second to last example is between 89 and 90, but gets rounded to 90 since 90 is even. Similar logic applies to the last example between 88 and 89, then rounding to 88.

![[Pasted image 20250905215718.png]]
In this case, we know a number is halfway if the red bits are 100 (1 followed by all 0's) and even if the LSB is 0.
1. The extra bits 011 are less than halfway, so we round down by dropping them
2. The extra bits are more than halfway, so we round up and add a 1 to the last bit we're keeping
3. The discarded bits are halfway, so we use the round to even rule. If we rounded down, the number we would be left with is 10.11, but if we round up it's 11.00. Since 11.00 is an even number (LSB = 0), we round up
4. The discarded bits are halfway, so we use the round to even rule. If we round down we get 10.10. Since this is already even (LSB = 0), we keep it as the final result

Guard-Round-Sticky (GRS) bit method, which is a hardware optimization for performing rounding efficiently. Instead of dealing with a potentially long string of bits that need to be discarded, the hardware simplifies them into just three bits. Together, the GRS bits tell the hardware everything it needs to know about the value being discarded.
![[Pasted image 20250905224955.png]]

### Floating Point Operations
![[Pasted image 20250905225056.png]]
![[Pasted image 20250905230049.png]]
![[Pasted image 20250905230135.png]]
![[Pasted image 20250905225253.png]]

You can't add two numbers in scientific notation unless their exponents are the same (e.g. 150 and 3.2). So, we first assume the first number we add is larger, so E1 > E2, then adjust the smaller number to match the first. So we need to make E2 = E1. For this to happen, we shift M2 by (E1 - E2) first, then add M1 and M2. After adding, we can have a small/larger number than what we need, so we need to fix the sum by the following 2 rules:
- If M >= 2: Shift significand one bit to the right and increment E
- If M < 1: Shift significant to the left until there is a 1 just before the binary point. You must count how many positions (k) you shifted and decrement the exponent E by k to compensate.
![[Pasted image 20250905225312.png]]
![[Pasted image 20250905225302.png]]

### More Properties
- C guarantees two levels:
	- float for single precision (32-bit)
	- double for double precision (64-bit)
- Casting between int, float, and double changes the bit representation
![[Pasted image 20250905232616.png]]
![[Pasted image 20250905233226.png]]

Examples:
Given
```C
int x = ...;
float f = ...;
double d = ...;
```
![[Pasted image 20250905232953.png]]![[Pasted image 20250906010926.png]]
![[Pasted image 20250906010936.png]]
![[Pasted image 20250906011001.png]]
![[Pasted image 20250906011011.png]]

![[Pasted image 20250906011143.png]]
![[Pasted image 20250906011152.png]]
![[Pasted image 20250906011202.png]]
![[Pasted image 20250906011306.png]]