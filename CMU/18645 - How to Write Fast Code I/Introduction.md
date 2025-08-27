## Matrix Multiplication
Matrix multiplication can be done through our traditional three nested loops to get O($n^3$). However, depending on the ordering of loops the number of execution cycles can be drastically different.
![[Pasted image 20250825162035.png]]
Option #3 is the fastest, and 1 and 2 tie.
https://claude.ai/chat/4e449210-0d3e-4ab6-8577-831d2fccac7e
This is because it accesses memory/the array continuously rather than strided