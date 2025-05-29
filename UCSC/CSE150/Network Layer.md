**Traditional Computer Network Architecture**
A traditional computer network is composed of two layers: the data plane and the control plane. There is also a management plane on top but it represents humans managing the network.

<u>Data Plane:</u>
The data plane is the 
![[Pasted image 20250527085737.png]]



<u>Control Plane</u>:
![[Pasted image 20250527085747.png]]

<u>Management Plane: </u>
![[Pasted image 20250527085816.png]]

**Software Defined Networking**
![[Pasted image 20250527085920.png]]
openflow networks

dont forget these slidees 11-15
![[Pasted image 20250527090656.png]]




**Network Layer**
The network layer is the 3rd layer in the IP stack, below transport and above data link. It is responsible for supporting the upper transport layer by transporting segments from the sending to receiving host. On the sending side, the network layer encapsulates segments into datagrams while on the receiving side, it delivers the received segments to the upper transport layer.

There are network layer protocols in every host and router, and the router examines the header fields in all IP datagrams passing through it.

<u>Key Functions</u>:
The network layer has two key functions:
- forwarding: moving packets from router's input to appropriate router output
- routing: determining the route taken by packets from source to destination (using routing algorithms)