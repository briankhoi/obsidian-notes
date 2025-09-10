![[Pasted image 20250908134045.png]]
In this class, a kernel is not an OS kernel. It represents the most important part of the code that we rewrite to make it the most efficient.

Many compute problems can be decomposed into loops over smaller computation. For example, in matrix multiply we are looping and doing dot products. In this case, the kernel would be the dot product operator.

To design an efficient kernel, we need to design it to maximize the use of our available hardware. This involves three questions:
- What and how much hardware is available?
	![[Pasted image 20250908134642.png]]
- What is the peak performance?
	- Peak performance: best the hardware can do under idealized conditions (data in register/L1 cache). We are not bottlenecked by bringing in data from memory, we assume data is already there (not reality) but for benchmarking we assume so
	- No faster than "speed of light" computation so:
		- check correctness of implementation
		- check theoretical peak computation
- How much compute is needed to maximize the hardware?

Peak performance can be different based on architecture:
![[Pasted image 20250908135358.png]]
It is determined by core compute (core compute also tells us what hardware is important):
- logical/binary operations (ANDs/cycle, ORs/cycle)
- transposition (SWAPs/cycle)
- compute (ADDs, MULs, flops/cycle)

To find the peak, we determine:
- how many instructions per cycle (benchmarking results) and take note if instruction is SIMD or not
- what HW is available
- what is the computational peak for

We also need to find the number of independent instructions to maximize our hardware. This helps us:
- pipeline to maximize use of individual hardware unit
- use multiple hardware units
- vectorize into SIMD instructions

There are two types of registers: logical and physical. The ISA exposes only 16 or 32 logical registers for us to use (aka only 16 or 32 variables to use) while there are hundreds or thousands of physical registers. We need to design only using logical registers and explicit loads and stores.

An independent operation (different from an independent instruction) is:
- one that computes 1 output per operation
- outputs chain of dependent instructions
- the instructions in an independent operation are independent of instructions in other operations

There are many independent operations per computation, and these independent operations form the core computation (kernel)

We want to identify as many independent operations as possible. In the example below, the independent operation is the equation, where it outputs a single output of that summation of dot products.
![[Pasted image 20250908140351.png]]

Maximizing throughput on matrix multiply through independent operations:
![[Pasted image 20250908141255.png]]
![[Pasted image 20250908141313.png]]
![[Pasted image 20250908141339.png]]
![[Pasted image 20250908141354.png]]

Designing a Kernel for Matrix Multiplication
To design a kernel for matrix multiplication, we ask ourselves the following questions:
- What is the size (m, n, k) of the kernel? (assuming 16 registers)
	- If you have 8 outputs, then you need 8 registers. But since we're using FMAs, we need 3 registers for a, b, and c. But since c is being re-used we can use just 2 for an FMA. This is a problem since that means 24 registers when we only have 16. Thus, 

2 operations each cycle, takes 4 cycles to complete so u have 8