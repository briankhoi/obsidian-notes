A distributed system is a set of cooperating computers communicating with each other over a network to get a task done.
Examples:
- Storage for big websites
- Big computation
- Peer to peer file sharing/communications

**People build distributed systems commonly for:**
- Parallelism
- Fault tolerance
	- At the scale of distributed systems like 1000 computers, normally rare errors almost always happen and are constant problems
- - Consistency 
- Physical reasons (like requiring more than one computer)
- Security/Isolation of components
- Performance/Scalability
	- It's rare/hard to get $nx$ scale from n computers

Types of fault tolerance
- Availability
	- System keeps operating despite certain kind of failures - if one you have two services and one of them fails, the other one is still available
- Recoverability
	- After the system is repaired, it continues to operate correctly as it nothing went wrong
- These two types are often achieved through non-volatile storage and replication

**Consistency**
Consistency is centered around two core operations: put and get
- Strong consistency: The get value is the same as the most recent put operation
- Weak consistency: No guarantees about the get value being the same as the most recent put, you can get old values. Many different types of weak consistency.
The reason why people are so interested in weak consistency is strong consistency is so expensive as an operation, for each write you have to check each copy of a stored value and ensure it's the same. Thus, people would rather use weak consistency as an alternative and there's a lot of research on how to maximize the correctness of weak consistency values.
