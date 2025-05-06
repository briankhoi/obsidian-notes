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

So, our approach is that the sender waits a "reasonable" amount of time for the ACK and retransmits if no ACK is received in this time. If the packet or ACK is just delayed and not lost, then retransmission (retrx) will result in a duplicate packet but the sequence number mechanism already handles duplicates. The receiver must also specify the sequence number of the packet being ACKed and the system overall requires a countdown timer.

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

Sender Diagram:
![[Pasted image 20250505223830.png]]

Sender FSM:
![[Pasted image 20250505222755.png]]

Receiver FSM:
![[Pasted image 20250505222901.png]]
- The receiver always sends an ACK for the correctly-received packet with the highest in-order sequence number. This can generate duplicate ACKs but the FSM only needs to remember the exepctedseqnum. 
- For out of order packets, the FSM discards it (no buffering) and re-ACKs the packet with the highest in-order sequence number.

Example:
![[Pasted image 20250505222927.png]]

Visualization: https://www.tkn.tu-berlin.de/teaching/rn/animations/gbn_sr/

<u>Selective Repeat</u>
Sender can have up to N unack'ed packets in pipeline. The receiver sends an individual ack for each packet (and buffers packets as needed for eventual in-order delivery to upper layer) while the sender maintains a timer for each unack'ed packet and re-transmits the packet only when its associated timer expires signifying the ACK was not received.

Diagram:
The sender window is comprised of N consecutive sequence numbers and the window limits the sequence number of sent, unack'ed packets.
![[Pasted image 20250505224758.png]]
![[Pasted image 20250505224834.png]]

Visualization (change setting on top left to selective repeat): https://www.tkn.tu-berlin.de/teaching/rn/animations/gbn_sr/

Example:
![[Pasted image 20250505225601.png]]
When ACK 2 arrives, it checks if the window base (oldest unacknowledged packet) can move forward and since it does, and it sees that packets 3-5 are already marked as ACK in its internal records and thus advances the window to the next unacknowledged sequence number which is 6, so the window is now \[6, 7, 8 , 9] 

Example 2:
![[Pasted image 20250505230349.png]]Issue: receiver sees no difference in scenario A were things are working normally and there's wrap around behavior of the window and sequence numbers, compared to scenario B where it treats duplicate/re-transmitted packets as new ones when they're not.
Fix: Make the window size W <= k/2 where k = number of sequence numbers (so max_seq_num = 3 leads to k=4 cause we start at 0).

**TCP**
<u>Properties</u>
- point to point: one sender, one receiver
- reliable, in order byte stream (no message boundaries)
- pipelined: TCP congestion and flow control set window size
- full duplex data: bi-directional data flow in same connection using MSS (maximum segment size)
- flow controlled (sender will not overwhelm receiver)
- connection-oriented: need handshaking (exchange of control messages) to initialize the sender and receiver state before data exchange

<u>Structure</u>
![[Pasted image 20250505231608.png]]

<u>Example</u>
![[Pasted image 20250505232550.png]]
![[Pasted image 20250505232542.png]]

<u>Timeout</u>
The TCP timeout value must be longer than the RTT (but RTT varies).
- if it's too short, then we have premature timeout, unnecessary retransmissions
- if it's too long, we have a slow reaction to segment loss
So, we estimate the RTT with a few ways:
- **SampleRTT**: get the average of several recent measurements of time from segment transmission until ACK receipt (ignoring retransmissions). this approach is generally not as smooth since it varies quite a bit
- **EstimatedRTT**: (1 - a) \* EstimatedRTT + a \* SampleRTT
	- exponential weighted moving average so influence of past samples decreases exponentially fast
	- typical value a = 0.125
	![[Pasted image 20250505232757.png]]
	- note: this is not a recursive formula but rather an iterative one, where the EstimatedRTT on the right is the new value to be calculated while EstimatedRTT on the right is the old EstimatedRTT value
	- weights are the same over iterative calls
	- EstimatedRTT is first initialized as the first SampleRTT
Finally, the timeout interval is given by EstimatedRTT + a safety margin (larger safety margin if EstimatedRTT variation is large). This safety margin (DevRTT) is given by:
![[Pasted image 20250505233011.png]]
and thus, the timeout interval is given by:
![[Pasted image 20250505233047.png]]

<u>Reliable Data Transfer</u>
TCP creates an RDT service on top of IP's unreliable service that has:
- pipelined segments
- cumulative acks
- single retransmission timer which is triggered by timeout events and duplicate acks

![[Pasted image 20250506001203.png]]
![[Pasted image 20250506001210.png]]![[Pasted image 20250506001222.png]]![[Pasted image 20250506001231.png]]![[Pasted image 20250506001245.png]]

Fast Retransmit: If the sender receives 3 ACKs for the same data (triple duplicate ACKs), it resends the unack'ed segment with the smallest sequence number. This is because if there are a lot of duplicate ACKs, it is likely that the unack'ed segment is lost so we don't want for timeout and instead just re-send it.
![[Pasted image 20250506002050.png]]Why duplicate ACKs are bad:
![[Pasted image 20250506002211.png]]

<u>Flow Control</u>
Flow control is when receivers control the sender's transmission rate so that the sender won't overflow the receiver's buffer by transmitting too much, too fast which if the buffer is filled up, would cause data loss.
![[Pasted image 20250506002307.png]]

Flow Control Mechanism:
![[Pasted image 20250506004358.png]]
Here, the receiver "advertises" free buffer space by including rwnd (receiver window) in the TCP header or receiver-to-sender segments. RcvBuffer size is set via socket options (default 4096 bytes) with it being auto-adjusted by many OSes.
The sender limits the amount of unack'ed in-flight data to the receiver's rwnd value, which acts at the flow control and guarantees that the receive buffer will not overflow.

<u>Connection Management</u>

Establishing a Connection:
Before exchanging data, the sender and receiver must handshake where they agree to establish the connection (each knowing the other is willing to establish connection) and the connection parameters
![[Pasted image 20250506004845.png]]
- estab = established

Traditional 2-Way Handshake:
![[Pasted image 20250506004942.png]]In network, the 2-way handshake won't always work due to variable delays, retransmitted messages due to message loss, message reordering, and not being able to see the other side (don't know the state or intentions of other party).
![[Pasted image 20250506005612.png]]
- Left failure: half open connection (server establishes connection but there's no client)
- Right failure: Client assumes that the accepted connection request is for its retransmitted request not its initial connection request. As a result, it sends data and then once the connection completes, a new connection with the server opens via the retransmitted connection of the client and it receives stale data from the previous connection.

<u>Solution: TCP 3-Way Handshake</u>
![[Pasted image 20250506005311.png]]

SYN:
- SYN stands for "Synchronize". It's a flag (a single bit) in the TCP header.
- When the SYN bit is set to 1 in a TCP segment, it signifies that the sender wants to establish a connection and is proposing an initial sequence number.
- SYN segments also consume one sequence number (even if they don't carry application data) because the synchronization itself is an event in the byte stream.

3-Way Handshake because you have:
1. Client ->Server SYN
2. Server -> Client (SYN-ACK)
3. Client->Server (ACK)

Process:
- Client sends SYN to server then moves to SYNSENT
- Server sends SYN-ACK to client then moves to SYN RCVD
- Client sends ACK to server for things like buffers and variables for the connection and then moves to ESTAB
- Server receives client's ACK and moves to ESTAB
-
The process is more robust than 2-way because it requires both parties to acknowledge each other's initial synchronization attempts before the connection is considered fully open.

<u>TCP: Closing a Connection</u>
To close a connection, the clients and server each close their side of the connection by sending a TCP segment with FIN bit = 1. Then, they respond to the received FIN with ACK (or on receiving FIN, ACK can be combined with the party's own FIN)
![[Pasted image 20250506015429.png]]

**Principles of Congestion Control**
Congestion is where there's too much data for the network to handle due to too many sources sending too much data too fast. It is different from flow control and can cause:
- long delays (queueing in router buffers)
- lost packets (buffer overflow at routers)

<u>First Cause/Cost of Congestion</u>
We are given $\lambda_{in}$ as the rate of one sender sending data.
As $\lambda_{in}$ gets bigger, the total traffic trying to go through the router (2 * $\lambda_{in}$) starts to exceed the output link capacity which causes congestion due to buffers filling up and high delay. The left graph shows that up until R/2, the senders can get the throughput they want, but at a certain point it's capped at R/2 since the output link capacity is R. Meanwhile, the right graph is showing that as $\lambda_{in}$ reaches its bottlenecked point of R/2, the costs of congestion will appear aka delay will go up exponentially.
![[Pasted image 20250506015724.png]]

<u>Second Cause/Cost of Congestion</u>
In this example, we have one router and finite shared output link buffers. 
$\lambda_{in}$ represents the application layer input which is equal to the application layer output of $\lambda_{out}$. However, the transport layer input includes retransmissions, so it is given by $\lambda_{in}'$ and $\lambda_{in}'>=\lambda_{in}$ .
The green rectangles denote copies of the original data used for retransmission.
![[Pasted image 20250506021139.png]]
Concepts:
Ideally, we would like to have:
- perfect knowledge: sender sends only when router buffers available (axes on graph do not exceed R/2)
![[Pasted image 20250506022202.png]]
- known loss: packets can be lost, dropped at router due to full buffers and sender only sends the packet if the packet is known to be lost 
![[Pasted image 20250506022230.png]]

However, realistically we have:
- Duplicates: packets can be lost, dropped at router due to full buffers. sender times out prematurely, sending two copies both of which are delivered
![[Pasted image 20250506022409.png]]
- costs:
	- more work (retransmission) for given goodput
	- unneeded transmissions: link carries multiple copies of packet which decreases goodput

<u>Throughput</u>: data rate at the receiver
<u>Goodput</u>: rate at the receiver for data without duplicates

<u>Third Cause/Costs of Congestion</u>
Since the paths share the same router, on the top router since it has finite shared output link buffers, as the input of the red packet rate increases, it dominates the buffer capacity of the top router leaving no room for the blue packets which then get dropped. As a result, the throughput goes to 0.
![[Pasted image 20250506022857.png]]

When packets are dropped, any upstream transmission capacity used for that packet is wasted. C = capacity in graph below
![[Pasted image 20250506022910.png]]- 
![[Pasted image 20250506023711.png]]
![[Pasted image 20250506023720.png]]

**Approaches for Congestion Control**
<u>End-End Congestion Control</u>
- no explicit feedback from network
- congestion inferred from end-system observed loss and delay
- approach taken by TCP

<u>Network-Assisted Congestion Control</u>
- routers provide feedback to end systems
- single bit indicated congestion (SNA, DECbit, TCIP/IP ECN, ATM)
- explicit rate for sender to send at

**TCP Congestion Control**
A TCP sender manages its sending rate to avoid overwhelming the network, primarily using its congestion window (cwnd). Essentially, only packets inside the current dynamic size of cwnd can be sent, the others have to wait. 
![[Pasted image 20250506024122.png]]
In this example, blue packets can be sent without waiting for previously sent packets to be ACKed. However, the gray packets must wait

cwnd:
![[Pasted image 20250506024155.png]]

The TCP Sending Rate is roughly determined by sending cwnd bytes, waiting for RTT for ACKs, then sending more bytes.
![[Pasted image 20250506024244.png]]

<u>Slow Start</u>
Slow start is a process where the initial rate of sending data is slow but ramps up exponentially fast until the first loss event.
- initially cwnd = 1 MSS (maximum segment size)
- double cwnd every RTT which is done by increment cwnd for every ACK received
![[Pasted image 20250506024431.png]]

<u>Detecting and Reacting to Loss</u>
When TCP slow starts, this can quickly lead to congestion if unchecked. So we introduce a threshold called $ssthresh$ (slow start threshold) which serves an an estimate of the point where congestion was last encountered and tells TCP when to switch from the aggressive growth of Slow Start to a more conservative, linear growth phase called Congestion Avoidance (CA).
- If we detect loss, we set $ssthresh$ to half the cwnd (because we previously doubled cwnd which led to the loss itself)

TCP Tahoe and TCP Reno are two early versions of TCP's Congestion Control Algorithms. 
- TCP Tahoe starts with cwnd set to 1 MSS (maximum segment size) and then enters slow start. If it detects any loss like a timeout or 3 duplicate ACKs, then it sets ssthresh = cwnd / 2 and resets the cwnd size to 1 MSS then re-enters slow start until it hits the initialized ssthresh size, then switches to linear growth. Now on linear growth if it experiences loss again, it re-adjusts ssthresh and restarts the entire cycle.
- TCP Reno also starts with cwnd set to 1 MSS and enters slow start. If there is a loss due to timeout, it repeats Tahoe's behavior by setting ssthresh = cwnd / 2 and cwnd = 1 MSS and re-enters slow start until hitting ssthresh then goes into CA mode. However, if it has loss due to 3 duplicate ACKs, then it sets ssthresh = cwnd / 2, cwnd = ssthresh and grows cwnd more linearly now (Congestion Avoidance) because it recognizes that duplicate ACKs mean that data is still flowing and thus the network is still capable of delivering some segments.

![[Pasted image 20250506030923.png]]

<u>Congestion Avoidance Algorithm: Additive Increase Multiplicative Decrease (AIMD)</u>
Sender increases transmission rate (window size) until loss occurs
- additive increase: increase cwnd by 1 MSS every RTT until loss detected
- multiplicative decrease: cut cwnd in half after loss
![[Pasted image 20250506030800.png]]

<u>Summary</u>:
![[Pasted image 20250506025729.png]]

**TCP Throughput**
Average Window Size = 3/4 W
Average TCP Throughput = (3/4 * W/RTT) bytes/sec
![[Pasted image 20250506032147.png]]
![[Pasted image 20250506032155.png]]

Example:
![[Pasted image 20250506032233.png]]