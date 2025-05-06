The transport layer is the second layer in the IP stack and is right below the application layer. Its purpose is to provide logical communication between app processes running on different hosts. There is more than one transport protocol available to apps like TCP and UDP for the Internet, and transport protocols as a whole run in end systems.
- send side: break application messages into segments and passes it to network layer
- receiving (rcv) side: re-assembles segments into messages and passes it to the application layer

<u>Transport vs. Network Layer</u>
The network layer provides logical communication between hosts while transport layer provides logical communication between processes (and relies on network layer services). A good analogy for this all is:
![[Pasted image 20250505183251.png]]

**Internet Transport Layer Protocols**:
- Transmission Control Protocol (TCP)
	- reliable transport between sending and receiving process
	- in-order delivery
	- good flow and congestion control so send doesn't overwhelm receiver and it throttles sender when network is overloaded (controls/limits rate of messages)
	- does not provide timing, minimum throughput guarantee, security
	- setup required between client and server processes
- User Datagram Protocol (UDP)
	- has unreliable data transfer between sending and receiving process
	- unordered delivery
	- does not provide reliability, flow or congestion control, timing, throughput guarantee, security, or connection setup
	- useful because it has much lower overhead than TCP, so it's faster and more efficient for apps where speed is important and some small data loss is fine (streaming audio/video, gaming, VoIP, DNS, simple network management protocol aka SNMP)
	- UDP segment header
	 ![[Pasted image 20250505194931.png]]
- both TCP and UDP cannot provide both delay guarantees (cannot guarantee data packets will take a predictable amount of time to travel from sender to receiver) and bandwidth guarantees (cannot guarantee a specific minimum data transfer rate aka throughput between transfer and receiver)
![[Pasted image 20250423182657.png]]

**Multiplexing and Demultiplexing**
When applications want to send data, each  application passes its data down to the transport layer via its specific socket. The transport layer takes these data chunks from different sockets, adds a header to each one (containing crucial info like source port number identifying the sending application socket, and destination port number identifying the intended receiving application socket), and then passes these segments down to the network layer. When the segments arrive at the receiving network layer, they get passed up to the transport layer. The transport layer then examines the transport header on each incoming segment and uses it to deliver the received segments to the correct socket.

<u>Multiplexing</u> is the process of handling data from multiple sockets and adding a transport header to them to be delivered while <u>demultiplexing</u> is the process of using the header information on the receiving end to deliver the segments to the correct socket.

![[Pasted image 20250505183814.png]]

**Demultiplexing In-Depth**
<u>Functionality</u>
Host receives IP datagrams that each have a source and destination IP address and carry one transport-layer segment containing a source and destination port number. The host then uses both IP addresses and port numbers to direct the segment to the appropriate socket.
![[Pasted image 20250505185849.png]]

<u>Connectionless Demultiplexing (UDP)</u>
For UDP, you only need a 2-tuple of destination IP address and destination port number. Then when the host receives the UDP segment it directs the UDP segment to the socket with the port number regardless of its source IP address.
The source port is still also needed for a return message.
![[Pasted image 20250505192737.png]]

<u>Connection-Oriented Demultiplexing (TCP)</u>
TCP Sockets have a 4-tuple that is needed by the receiver to demultiplex and direct the segment to appropriate socket.
- source IP address
- source port number
- destination IP address
- destination port number
Sockets are identified by their own 4-tuple and the server host can support many simultaneous TCP sockets. For instance, web servers have different sockets for each connecting client where non-persistent HTTP will have different sockets are each request.

Example:
![[Pasted image 20250505194450.png]]
When a client (like Host A or Host C) connects to a server (Host B) on its listening port (port 80, commonly used for HTTP), the server typically accepts the connection and creates a new, dedicated socket and process specifically for handling communication with that particular client. This new socket is uniquely associated with the 4-tuple defining that specific connection. The original socket on port 80 remains listening for new connection requests.

Example 2:
![[Pasted image 20250505194555.png]]In this example, rather than create a separate process for each connection, we have a threaded server that creates a worker thread to handle the communication using the worker thread's dedicated socket, but all the worker threads run in the context of a single process allowing concurrent work.

**Checksum**
<u>UDP Checksum</u>
The goal of a checksum is to detect errors (flipped bits) in a transmitted segment.

The sender treats segment contents including header fields as a sequence of 16-bit integers (a word is one of those 16-bit integers) and does a checksum (one's complement addition sum) of the segment's contents (so add up all the 16 bit integers together using one's complement) and puts the checksum's value into the UDP checksum field. Afterward, the receiver computes the checksum of the received segment and checks if the checksum equals the checksum field value. If it does, there is no errors detected while if it doesn't, then we know there's an error in the segment which can arise from things like maliciously modified packets. We know if there's an error if the end computed value is not all 1's.
- minor revision: the checksum algorithm may not be able to detect even-number bit errors (errors where there are two flips that flip the same bit and cancel each other out) because if the bits cancel each other out, the correct state of 1 is restored and you never know, but can detect odd-number bit errors because if you flip it an odd number of times, you can't cancel the 0 out and thus a 0 will be detected
Example:
![[Pasted image 20250505200817.png]]
In this example, we do normal binary addition to get an intermediate sum value (the wraparound). Then, we take the carry out bit (the left-most bit that was there due to addition overflow) and add it back to the 16-bit (not 17-bit cause we're removing the carry out) result to get the sum. After, we do a one's complement (flip bits 0 to 1 and 1 to 0) to get the check sum.

**Principles of Reliable Data Transfer**
In the application layer, when applications send data it interacts with the transport layer and relies on it to provide a "reliable channel", which from its viewpoint functions as a black box/service abstraction that reliably transports data.

But under the hood, the reliable data transfer protocol (RDT) provides a function like rdt_send() for the application to call. Then, the RDT protocol takes the application data and adds control information like checksums and creates a packet. The RDT protocol itself relies on the layer below (the network layer) to transmit the packet which is represented as an unreliable channel in which the packet could be lost, corrupted, or re-ordered. After the RDT protocol receives packets from the unreliable channel, it checks the packet for errors and correctness and if everything is correct, then it extracts the original data and sends it up to the application layer using deliver_data().

The characteristics of unreliable channel will determine complexity of reliable data transfer protocol.
![[Pasted image 20250505202445.png]]

**Reliable Data Transfer**

<u>Finite State Machines (FSMs)</u>
Used to specify sender and receiver where the nodes are states and edges are events and actions.
![[Pasted image 20250505203246.png]]

<u>Scenario 1: Reliable Transfer over a Reliable Channel</u>
