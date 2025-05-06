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

<u>RDT 1: Reliable Transfer over a Reliable Channel</u>
If the underlying channel is perfectly reliable (has no bit errors and no packet loss), then we have separate FSMs for the sender and receiver where they send and receive data into/from the underlying channel respectively.
![[Pasted image 20250505203604.png]]

<u>RDT 2: Underlying Channel with Bit Errors</u>
In this scenario, we are given an underlying channel that may flip the bits in the packet.

How do we recover from errors?
Starting off, to detect the bit errors initially we use a checksum, and then to recover we use an acknowledgement system:
- Acknowledgements (ACKs): receiver tells sender that packet received OK
- Negative acknowledgements (NAKs): receiver tells sender that packet had errors
New mechanisms in RDT 2.0 (beyond RDT 1.0): Establishes both an error detection and feedback mechanism (checksum, control messages of ACK/NAK from receiver to sender).
![[Pasted image 20250505204112.png]]

But there's a fatal flaw - what happens if ACK/NAK is corrupted? In that case the sender doesn't know what happened at the receiver and can't retransmit it since there might be a possible duplicate.

So our solution is to stop-and-wait, where the sender sends one packet and waits for receiver response. If the ACK/NAK is corrupted, the sender retransmit the current packet but adds a sequence number to each packet so that the receiver discards and doesn't deliver up the duplicate packet.

<u>RDT 2.1: Handling Corrupt ACK/NAKs</u>
In RDT 2.1, the sender puts a sequence number of either 0 or 1 on each segment. It then sends a segment with 0 and waits for an ACK, and if it does receive one, it sends a segment with 1 signifying that it received an ACK. Otherwise, if it received a NAK or corrupted ACK, it resends the segment with 0. On the receiving end, if the receiver receives a segment with 0, it replies with an ACK and knows that it's either the sender's initial request or that the sender did not receive the ACK response initially. Then if it receives a segment with 1, then the receiver knows that the sender must have received an ACK.

The state must remember whether the expected packet should have a sequence number of 0 or 1 and must check if the received packet is a duplicate (indicated by the current state's expected packet). Also, the receiver **cannot** know if its last ACK/NAK received OK at sender but rather the sender keeps sending sequence 1 packets until it receives an ACK then moves on to the next sequence 0 packet.

The Sender FSM:
![[Pasted image 20250505205808.png]]

The Receiver FSM:
![[Pasted image 20250505205824.png]]

<u>RDT 2.2: NAK-Free Protocol</u>
in RDT 2.2, we use only ACKs and the receiver sends the ACK for the last packet that was OK, crucially including the sequence number of the packet being ACKed. When the sender receives an ACK, it checks the sequence number in the ACK. If the sender receives an ACK for a packet it has already successfully received an ACK for (a "duplicate ACK"), it treats this duplicate ACK as an implicit signal that the other packet (the one it sent after the one being ACKed again) was not received correctly by the receiver and thus the sender's response to receiving a duplicate ACK is the same as its response to receiving a NAK in rdt2.1: it retransmits its current, unacknowledged data packet.

Fragments of the FSM (cause rest is somewhat identical to RDT 2.1)
![[Pasted image 20250505213446.png]]

<u>RDT 3.0: Channels with Error and Loss</u>
With RDT 3.0, we introduce a new assumption that the underlying channel can also lose packets (data, ACKs).

So, our approach is that the sender waits a "reasonable" amount of time for the ACK and retransmits if no ACK is received in this time. If the packet or ACK is just delayed and not lost, then retransmission will result in a duplicate packet but the sequence number mechanism already handles duplicates. The receiver must also specify the sequence number of the packet being ACKed and the system overall requires a countdown timer.

Sender FSM:
![[Pasted image 20250505214903.png]]

<u>Different RDT 3.0 Scenarios</u>
![[Pasted image 20250505214932.png]]
![[Pasted image 20250505214950.png]]

RDT 3.0 Performance:
![[Pasted image 20250505221450.png]]
![[Pasted image 20250505221433.png]]![[Pasted image 20250505222021.png]]
![[Pasted image 20250505222242.png]]
![[Pasted image 20250505222120.png]]
Low utilization is bad because you're effectively wasting the expensive link you have.

**Pipelined Protocols**
Pipelining allows the sender to send multiple "in-flight", yet-to-be-acknowledged packets. With pipelining, there is buffering at the sender and/or receiver and the range of sequence numbers must be increased.
![[Pasted image 20250505222420.png]]

Two types of pipelined protocols: Go-Back-N and Selective Repeat

<u>Go-Back-N</u>
Sender can have up to N unack'ed packet in its pipeline. The receiver only sends a cumulative ack which doesn't happen if there a gap. The sender has a timer for the oldest, unack'ed packet and when the timer expires it retransmits all unack'ed packets.


Sender FSM:
![[Pasted image 20250505222755.png]]

Receiver FSM:
![[Pasted image 20250505222901.png]]

Example:
![[Pasted image 20250505222927.png]]

<u>Selective Repeat</u>
Sender can have up to N unack'ed packets in pipeline. The receiver sends an individual ack for each packet while the sender maintains a timer for each unack'ed packet and re-transmits the packet only when its associated timer expires.
