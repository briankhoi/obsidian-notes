The transport layer is the second layer in the IP stack and is right below the application layer. Its purpose is to provide logical communication between app processes running on different hosts. There is more than one transport protocol available to apps like TCP and UDP for the Internet, and transport protocols as a whole run in end systems.
- send side: break application messages into segments and passes it to network layer
- receiving (rcv) side: re-assembles segments into messages and passes it to the application layer

<u>Transport vs. Network Layer</u>
The network layer provides logical communication between hosts while transport layer provides logical communication between processes (and relies on network layer services). A good analogy for this all is:
![[Pasted image 20250505183251.png]]
