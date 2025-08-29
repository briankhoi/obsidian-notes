Refactoring is a program transformation intended at preserving the observable behavior of the software system while improving its internal structure.

Software decay is the gradual deterioration in performance, stability, and maintainability over time - happens primarily due to software's evolving ecosystem (changes in dependencies, libraries, operating systems, hardware, etc.) and technical debt/poor code quality (code duplication, poor documentation, lack of modularity, etc.)

Code smells: things wrong w/ code like feature envy 

Feature envy:
![[Pasted image 20250828101951.png]]
If number of external calls > internal calls

Abstract Syntax Tree (AST): Tree representation of the abstract syntatic structure of source code written in a programming language
![[Pasted image 20250828194332.png]]

ASTVisitor: A design pattern which navigates through an AST node by node and collects information regarding it