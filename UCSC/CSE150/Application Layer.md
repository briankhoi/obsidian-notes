**Creating a Network Application**
- we write programs that:
	- run on different end systems
	- communicate over network
- we don't write software for network-core devices:
	- network-core devices do not run user applications
	- applications on end systems allow for rapid app development

Two potential application structures are:
- client-server
- peer-to-peer (P2P)

**Client-Server Architecture**
In this architecture, there are two components: the client and server.

The server is an always-on host with a permanent IP address and is typically stored in data centers for scaling. 

Meanwhile, the clients can have dynamic IP addresses, can be intermittently connected, and communicate with the server, but not directly with each other.
- The IP address depends on the network that the client is connected to that it is connecting to the server from.

**Peer-to-Peer (P2P) Architecture**
In this architecture, there is no always-on server, but rather the end systems directly communicate.

Peers are intermittently connected and change IP addresses, and request service from other peers and provide services in return to other peers.

As a result, this architecture has self-scalability built in, but due to the dynamic nature of changing IP addresses, managing this architecture is difficult.

**Client-Server vs. P2P Architectures**
- throughput and scalability: P2P wins
	- P2P wins due to its self-scaling nature and due to the fact that a server can only serve a limited number of clients
- management: client-server wins
	- users in P2P are unreliable (how do you determine who is online or who holds specific data when all the addresses are changing constantly?)
	- thus, we now use client-server architecture since management of clients in a P2P architecture has become a big issue

**Hybrid Architecture Examples**
<u>Skype</u>: Voice-over-IP (VoIP) P2P application
- centralized server: finding address of remote party
- direct client to client connection for calls/messaging

<u>Instant Messaging</u>:
- chatting between two users (can be) P2P
- centralized server: detects client presence and location
	- user registers its IP address with central server when it comes online and contacts it to find the IP addresses of buddies

**Processes**
A process is a program running within a host. Processes within the same host communicate using <u>inter-process communication</u> (defined by OS) while processes in different hosts communicate by exchanging messages
- client process initiates communication while server process waits to be contacted

Processes send and receive messages to and from their <u>socket</u>
![[Pasted image 20250423161837.png]]

To receive messages processes must have an <u>identifier</u> that is comprised of the host device's unique 32-bit IP address and the port numbers associated with the process on the host (ex. http server = 80, mail server = 25). The IP address of the host alone does not suffice since many processes can be running on the same host.

**Application Layer Protocol**
The application layer protocol defines:
- types of messages exchanged (request, response)
- message syntax (fields in message & how they're delineated)
- message semantics (meaning of info in fields)
- rules for when and how processes send & respond to messages

there are open protocols (defined in RFC - official document for specifying protocols) like HTTP and SMTP and proprietary ones like Skype

**Transport Services**
In general, apps need a transport service that has data integrity, good throughput, low delay/timing, and security.

Common app requirements:
![[Pasted image 20250423163346.png]]
"elastic" for throughput means it makes use of whatever throughput it can get - there's no minimum throughput for it to be "effective"

**Internet Transport Protocol Services**:
- Transmission Control Protocol (TCP)
	- reliable transport between sending and receiving process
	- good flow and congestion control so send doesn't overwhelm receiver and it throttles sender when network is overloaded (controls/limits rate of messages)
	- does not provide timing, minimum throughput guarantee, security
	- setup required between client and server processes
- User Datagram Protocol (UDP)
	- has unreli