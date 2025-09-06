## Matrix Multiplication
Matrix multiplication can be done through our traditional three nested loops to get O($n^3$). However, depending on the ordering of loops the number of execution cycles can be drastically different.
![[Pasted image 20250825162035.png]]
Option #3 is the fastest, and 1 and 2 tie.
https://claude.ai/chat/4e449210-0d3e-4ab6-8577-831d2fccac7e
This is because it accesses memory/the array continuously rather than strided

![[Pasted image 20250827143413.png]]

Algorithm 2 for instance is super slow because you're writing to the same place over and over and close together
First iteration you're writing to $c[0][0]$ a bunch of times which can lead to stalls in pipelining. For algorithm 3 you still are writing to that location a lot, but the calls are more spread out so you remove the dependency in sequential operations 

![[Pasted image 20250827143643.png]]