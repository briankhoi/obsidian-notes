Software written is not software executed. Everything is compiled into binary 0/1's. 

We introduce Instruction Set Architecture (ISA), used for assembly. ISA can change to introduce new capabilities and can also be limited in scope based on the architecture
![[Pasted image 20250827131938.png]]
Fast code is also dependent on how compatible the ISA is with the hardware.
![[Pasted image 20250827131928.png]]
It's knowing how well the software matches the hardware and making sure both are optimal pairings that matters.

Even ISA, which is the lowest level which we write software, is an abstraction. Instructions may be executed in a different order based on the hardware.

For instance, our ISA registers are 16/32 but the actual physical ones are much larger.
![[Pasted image 20250827132621.png]]

Different details in the hardware impact software performance. Understanding these details and modifying our code based on that is the key to writing fast code. We need to know the cost and hardware impact on choosing a particular instruction.

Thus, assembly language does not mean performance.

In this class, we do not design for CPU/GPU, but rather design for a set of common features that are present no matter what hardware: personal laptops, microcontrollers, cloud services, accelerators, etc. These features include:
![[Pasted image 20250827132836.png]]

All hardware can be divided into two main parts: parts that do computation, and parts that move data from one place to another.
![[Pasted image 20250827132924.png]]

### Functional Units/Pipeline/Queue (Interchangeable)
There are a lot of functional units in hardware, we want to use as many pipelines as possible and avoid stalling in any of them
![[Pasted image 20250827140651.png]]
Need to know how many pipelines there are, which ones are being used for which instruction and optimize it. For example if you're doing floating point addition you want to optimize for the FP ADD pipeline.

### Model Instruction Pipeline
We want to pipeline instructions as much as possible and fill in all stages of the pipeline. Different instructions can be in different stages. However, if we have a dependency we need to wait/stall for the dependency to finish, then resume executing our instruction, resulting in a bubble.
![[Pasted image 20250827141219.png]]
So, what you can do is while you're waiting for your dependency to finish, you let another instruction go first. However, sometimes this does not completely eliminate stalls. Note how the second instruction is dependent on the variable $c$ as well, so it needs to wait for the first instruction to finish, and for its own $*$ operator to finish before adding it to $c$.
![[Pasted image 20250827141354.png]]
This process of letting other instructions advance first while you wait for a dependency is called instruction reordering. Also note that you need to find independent instructions to let pass first, or else like the image above you won't completely eliminate stalls.
### Instruction Reordering
- Hardware solution: out of order execution
	- Has an instruction buffer
	- When there's a stall, find next instruction in buffer that's independent then execute it
	- Write back stage must be managed to ensure correctness
	- Works only if it can find independent instructions, but if there's a lot of dependent instructions it's ineffective
- Compiler solution
	- Software pipelining
	- Compiler tries to identify and moves instructions during the compilation process
	![[Pasted image 20250827142247.png]]
Both solutions although great, are still not enough. Instruction rescheduling must be implemented at the hardware, software, and algorithms level.

Essentially, we know that pipelining is key. But we can't leave it just to the hardware or compiler. We ourselves need to write code to make pipelining easier.

### Analyzing Assembly
Even if you think there are no dependencies in your code, you need to look at the ISA because that's what's been actually ran. When going to office hours they'll always ask if you've read the assembly.