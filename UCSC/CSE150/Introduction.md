A computer network is a group of two or more computer systems linked together.

Networks are made of:
- Links: connections between nodes and terminals
- Nodes: Routers, switches - used to forward communication
	- need routers and switches to serve as relays - can't just physically connect stuff together because sometimes you don't know who you're going to want to connect to
- Terminals: Hosts, end-user devices - source/destination of communication

**The Internet vs. an Internet**
The term "internet" is an abbreviation of internetwork, which is a collection of interconnected networks with no central administration or management
- "Intranet" is communications within a singular network (like NASA Intranet)
- A network has a single administrative authority

The internet is comprised of:
- millions of connected computing devices
- connection links like fiber, copper, radio, satellite
- routers to forward packets
- a hierarchical structure
	![[Pasted image 20250403142906.png]]
- a communication infrastructure that enables distributed applications: web, VoIP, email, games, e-commerce
- communication services provided to apps
	- reliable data delivery from source to destination

**Protocols**
All communication in internet is governed by protocols. They define format, order of messages sent and received, and actions taken on message transmission and receipt

**Switch**
A network switch is a piece of networking hardware that connects multiple devices on a network, enabling them to communicate and share resources by intelligently forwarding data packets based on their destination MAC address.

**Network Structure**
- Network edges: the end systems (hosts) that run applications like the web or email. They are at the "edge of the network"
	- they (hosts) can be either clients or servers
	- servers often in data centers
- Access networks, physical media: wired & wireless communication links that connect **directly** to end users, like the institutional or mobile network
	- they are edge routers that connect to end systems
	- home network example
		![[Pasted image 20250403145455.png]]
		- the router forwards connections to devices on the network, while the cable/DSL modem connects your home to the internet
		- the wireless access points provides wireless connectivity to an existing network, allowing devices to connect wirelessly to a network that is already wired
	- enterprise access networks (ethernet)
		![[Pasted image 20250403145600.png]]
	- wireless access networks
		![[Pasted image 20250403145641.png]]
- Network core: not directly connected to users like ISP networks
	- interconnected routers
	- network for networks

**Hosts**
 Hosts send packets of data through a sending function that takes an application message and breaks it into smaller chunks (packets) of length L bits, then transmits the packet into the access network at transmission rate R.

The packet transmission delay represents the time needed to transmit a L-bit packet into a link. It is given by:
![[Pasted image 20250408133922.png]]
- so basically, takes L/R seconds to transmit (push out) L-bit packet into link at R bps

**Units in Networking and Computing**
- 1b = 1 bit
- 1B = 1 byte
- 1 byte = 8 bits
- 1 Kbps = 1 kilobit per second = 1000 bits per second
- 1 Mbps = 1,000,000 bits per second = 1000 Kbps

**Four Sources of Packet Delay**
![[Pasted image 20250409162425.png]]
- $d_{nodal} is the total delay experienced at each node (like a router) for a packet
- $d_{proc}$ = is the nodal processing delay, which represents the time for checking bit errors and determining the output link. It is typically less than a millisecond.
- $d_{queue}$ is the queuing delay, which represents the time waiting a output link for transmission and also depends on the congestion level of the router.
- $d_{trans}$ is the transmission delay which covered in the section above is the length of the bits of queue processing
- $d_{prop}$ is the propagation delay, which represents the time it takes for the packet to travel between source and destination. It is given by the formula $d_{prop} = d/s$ where d is the length of the physical link and s is the propagation speed in the medium (~$2 \ x \ 10^8\  m/sec$)
- Analogies: **transmission delay** is like the time to write a mail, **propagation delay** is the post truck carrying the mail to the next post office, **processing delay** is post officers distributing the mail to the right box for the next station, and **queuing delay** is the mail waiting in the post office for processing

**The Network Core**
The network core is a mesh of interconnected routers (blue in diagram).
![[Pasted image 20250408134338.png]]

**Packet-Switching**
Packet-switching is when the host breaks application layer messages into packets.
- It forwards packets from one router to the next across links on the path from source to destination
- Each packet is transmitted at full link capacity, meaning that one packet is fully transmitted first, and then another starts to be transmitted after the previous one has been fully transmitted. (sequential transmission not parallel)
- great for bursty data
	- resource sharing
	- simpler, no call setup

Concepts:
- Store and Forward
	- the entire packet must arrive at the router before it can be transmitted on the next link
- End-to-end delay
	- propagation delay is when there's a delay between the packet going into a router and then continuing on the link
	- assuming there is no propagation delay, the end-end delay is **2 * L/R**
- Queuing and loss
	- if the arrival rate (in bits) to the link exceeds the transmission rate of the link for a period of time, then packets will queued in memory waiting to be transmitted on link. However, if this memory buffer fills up, then packets can be dropped/lost
	- and this type of excessive congestion that leads to queuing and loss is possible, so you need protocols for reliable data transfer and congestion control

**Circuit Switching**
Circuit switching is when end-to-end resources are allocated to and reserved for the transmission/call between the source and destination.

As a result, it has its own dedicated resources (no sharing with others) and has circuit-like performance.
- because it has its own dedicated resources end-end, then there's no queuing and delay issues
- the circuit segment is idle if not used since it can't be shared
- commonly used in traditional telephone networks
![[Pasted image 20250408140811.png]]

**Packet-Switching vs. Circuit-Switching**
Packet switching allows more users to use the network.

Example:
Let's say you have a 1 Mb/s link and each user uses 100 kb/s when they're "active", with an active rate of 10% of the time.

In this case, circuit switching requires there to be dedicated resources to handle 100Kb/s for each user, and they can't be shared. So we partition the 1 Mb/s into 10 100Kb/s links/resources to fully support each user.

Meanwhile, packet-switching is able to have a lot more users. For instance, with 35 users, the probability of 10 users being active at the same time is less than 0.0004, and even in that case they can still queue the packet for a while.

**Internet Structure: Network of Networks**
As we know, the internet is a network of networks. Each end system connects to the Internet via access ISPs (Internet Service Providers) like residential, company, and university ISPs. In turn, the access ISPs must also be interconnected so that any two hosts can send packets to each other. This leads to a network structure that is very complex.

This leads to the following structure:
![[Pasted image 20250409160709.png]]
![[Pasted image 20250409161122.png]]
- Each access ISP connects to a global transit ISP or a regional network that helps them connect to a global transit ISP
- Tier 1 commercial ISPs (Sprint, AT&T, etc.) provide national and international coverage
- Each global transit ISP connects to an IXP (Internet Exchange Point) so that they can link and share data
- Content provider networks (private networks that connect data centers to Internet, often bypassing Tier 1 and regional ISPs) like Google and Microsoft can run their own network on top to bring services and content closer to end users

**Throughput**
Throughput is the rate (bits/time unit) at which bits are transferred between the sender/receiver
- instantaneous: rate at given point in time
- average: rate over longer period of time

Given this diagram:
$R_c$ denotes the rate near/going into end system/computer and $R_s$ denotes the rate going from a server

![[Pasted image 20250409164121.png]]

The **bottleneck link**, which is the link on the end-end path that constraints end-end throughput is always given by the lowest throughput on the link. So for example, if we are given $R_s < R_c$, then the average end-end throughput is no higher than $R_s$ since that's the bottleneck link. Vice-versa as well if $R_c > R_s$.

Example:
Given this Internet where you had 10 connections, the **per-connection** end-end throughput si given by $min(R_c, R_s, R/10)$ with R/10 cause of 10 connections evenly sharing R bit/sec
![[Pasted image 20250409164456.png]]

**Protocol Layers**
We know networks are very complex and have many different parts: hosts, routers, links, protocols, hardware/software, applications, etc. How do we organize this structure?

We use a **layered** approach, where each layer implements a service via its own internal-layer actions and relies on services provided by the layer below.

This approach is useful for dealing with complex systems:
- The explicit structure allows identification and relationship of complex system's pieces (layered reference model)
- Modularization of components eases maintenance and updating of system
	- change of implementation of layer's service doesn't affect the rest of the system

**Internet Protocol**
The Internet uses a layered approach for organizing this network of networks. We divide it into five layers (in the following order from top to bottom):
- **application**: supporting network applications (HTTP)
- **transport**: process-to-process data transfer (TCP, UDP)
- **network**: routing of datagrams from source to destination (IP, routing protocols
- **link**: data transfer between neighboring network elements (Ethernet, WiFi, PPP)
- **physical**: bits "on the wire"

This is a diagram of the flow through the different protocols:
![[Pasted image 20250410141120.png]]
As the packet travels from source to destination, through each part of the network it adds headers corresponding to which layer it travels through and then drops them when it's done. The purpose of adding the header in general is to talk to the other same layers. So adding a link header $H_l$ is used to talk to other link layers. And at the link layer destination, you don't need the packet anymore so you drop it and send it to the next layer.

This process is called **encapsulation**.

**ISO/OSI Reference Model**
There's another model of the Internet called the ISO/OSI reference model which includes two more layers between application and transport. It's the same as the internet protocol stack but with the components divided a bit differently. Like the IP stack are not missing these layers but rather mostly bundles them in the application layer.

Regardless, here are the two layers below application and above transport in this order:
- **presentation**: allows applications to interpret meaning of data (encryption, compression, machine-specific conventions)
- **session**: synchronization, checkpointing, and recovery of data exchange

**Network Security**
The Internet wasn't originally designed with much security in mind. So, here are some potential security vulnerabilities on the Internet:
- malware can get in host from
	- virus: self-replicating infection by receiving/executing object (like email attachment)
	- worm: self-replicating infection by **passively** receiving object that gets itself executed (like replicating through a network; it doesn't need a host)
- spyware malware can record keystrokes, websites visited, and upload the info to a collection site
- an infected host can be enrolled in a network of bots (**botnet**), used for spam and distributed denial of service attacks (DDoS)
- denial of service (DoS): attackers make resources (server, bandwidth) to legitimate traffic by overwhelming resources with bogus traffic. the procedure:
	- select target
	- break into hosts around network (like a botnet)
	- send packets to target from comprised hosts
- packet sniffing: promiscuous network interface (like wireshark) reads and records all packets (including passwords) passing by ![[Pasted image 20250410142039.png]]
- IP spoofing: sending a packet but with a false source address ![[Pasted image 20250410142411.png]]
	
w