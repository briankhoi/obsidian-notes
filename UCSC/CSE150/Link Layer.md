The data link layer has the responsibility of transferring datagrams from one node to a physically adjacent node over a link. Nodes are defined as hosts and routers.

The link layer provides:
- framing/link access
	- encapsulates datagrams into frame, adding a header, and trailer channel access if shared medium
	- "MAC" addresses used in frame headers to identify source, dst (diff from IP address)
- reliable delivery between adjacent nodes
- flow control: pacing between adjacent sending and receiving nodes
- error detection
	- errors caused by signal attenuation, noise
	- receiver detects presence of errors and signals sender for retransmission or drops frame
- error correction: receiver identifies and corrects bit errors without resorting to retransmission
- half-duplex and full-duplex
	- with half duplex, nodes at both ends of link can transmit but not at same time

The link layer is implemented in every host in the adapter (network interface card aka NIC) or on a chip and attaches into the host's system buses. It is a combination of hardware, software, and firmware.
![[Pasted image 20250610184748.png]]

**High Level Overview**
Adapters communicating:
![[Pasted image 20250610184835.png]]

**Error Detection**
Error detection revolves around adding Error Detection and Correction (EDC) bits to the datagram before sending, which will then be checked on the receiving side for errors. Error detection is not 100% reliable, and a larger EDC field yields better detection and correction.
![[Pasted image 20250610185559.png]]

**Parity Checking**
<u>Single Bit Parity</u>
For a block of d data bits, we append a parity bit. There are two types of parity, even and odd parity with each type meaning that there are an even/odd respectively count of 1's in the entire block including the parity bit. So if we have 8 1's in d, and we have even parity, we would want our parity bit to be 0 to keep the even constraint.
![[Pasted image 20250610190028.png]]
Single bit parity can only detect single bit errors and cannot detect even bit errors (like if you flipped 2 diff bits it can still pass)

<u>Two-Dimensional Bit Parity</u>
Same concept as single bit parity but now we have a parity bit for each column and row. The example below uses even parity:
![[Pasted image 20250610190411.png]]
This once again can only detect and correct single bit errors.

**Cyclic Redundancy Check (CRC)**
Compared to parity checking, CRC is a more powerful error-detection mechanism. It is widely used in practice (Ethernet, 802.11 WiFi, ATM), and works by:
![[Pasted image 20250610220939.png]]
![[Pasted image 20250610221001.png]]

![[Pasted image 20250610190727.png]]

**Multiple Access Links**
There are two types of "links":
- point to point:
	- PPP for dial-up access
	- point-to-point link between Ethernet switch and host
	- ![[Pasted image 20250610222338.png]]
- broadcast (shared wire or medium)
	- old fashioned Ethernet
	- upstream HFC
	- 802.11 wireless LAN

**Multiple Access Protocols**
Imagine you have a single shared broadcast channel. If you have two or more simultaneous transmission by nodes over this channel, the signals will interfere. If a node receives two or more signals at the same time, you get a collision. 

This is where multiple access protocol comes in, which is a distributed algorithm that determines how nodes share channel (determines when nodes can transmit). Crucially, the very messages or signals used to coordinate who gets to transmit must also be sent over the same shared communication channel as there is no out-of-band channel for coordination.

The ideal multiple access protocol is that given a broadcast channel of rate R bits per second (bps):
![[Pasted image 20250610223014.png]]

There are three broad classes of MAC protocols:
- channel partitioning
	- divide channel into smaller "pieces" (time slots, frequency, code)
	- allocate piece to node for exclusive use
- random access
	- channel not divided, allow collisions
	- "recover" from collisions
- "taking turns"
	- nodes take turns, but nodes with more to send can take longer turns

**Channel Partitioning MAC Protocols**
Pros: Each direction has fixed time to go
Cons: If one station has nothing to send at its
time slot or frequency, this resource cannot be
used by others and is wasted

<u>Time Division Multiple Access (TDMA)</u>
- access to channel in "rounds"
- each station gets fixed length slot (length = pkt transmission time) in each round
- unused slots go idle
![[Pasted image 20250610224825.png]]

<u>Frequency Division Multiple Access (FDMA)</u>
- channel spectrum divided into frequency bands
- each station assigned fixed frequency band
- unused transmission time in frequency bands go idle
![[Pasted image 20250610225037.png]]

**Random Access Protocols**
When a node has a packet to send, we transmit at full channel data rate R with no coordination amongst nodes. If there are two or more transmitting nodes at the same time we call that a collision.

Random access MAC protocols specify how to detect collision and how to recover from them (like via delayed retransmissions).

<u>Slotted ALOHA</u>
We assume that all frames are the same size and divide time into equal size slots (time to transmit 1 frame). All nodes have the same understanding of when each time slot begins and ends (all on the same clock) and can only transmit at the beginning of each time slot. 

If two or more nodes transmit at the beginning of the same slot, their signals collide and all nodes listening on the channel immediately know that a collision has occurred.

For Slotted ALOHA, when a node obtains a fresh frame it transmits it in the next available slot.
- no collision: node can send new frame in next slot
- if collision: node retransmits frame in each subsequent slot with probability p until success

C is collision, E is empty/idle since they only retransmit with probability p, so 1-p chance that they don't and wait for next slot after, S is success as in a frame was successfully transmitted through the channel
![[Pasted image 20250610231534.png]]

Pros:
- single active node can continuously transmit at full rate of channel
- highly decentralized: only slots in nodes need to be in sync
- simple
cons:
- collisions, wasting slots
- idle slots
- even if a collision is detected, the whole slot is wasted
- clock synchronization

In Slotted ALOHA, efficiency is defined as the long-run fraction of successful slots (many nodes, all with many frames to send). 

For example, suppose we have N nodes with many frames to send each transmits in slot with probability p. Then, the probability that the given node has a success in a slot = $p(1-p)^{N-1}$ and the probability that any node has a success is $Np(1-p)^{N-1}$. The best probability that maximizes the success is denoted as p* and as we approach the limit of N to infinity, the max efficiency we get is 1/e = .37 = 37% of time at best channel used for useful transmissions

<u>Pure (Unslotted) ALOHA</u>
Simpler than slotted ALOHA, no need for synchronization. 

Key principle: when the frame first arrives, transmit it immediately. This leads to a likelier chance of collision:
![[Pasted image 20250611013001.png]]
![[Pasted image 20250611013012.png]]

<u>Carrier Sense Multiple Access (CSMA)</u>
Key principle: Listen before transmitting. If you sense the channel is idle, transmit the entire frame else defer transmission (aka don't interrupt others).

Collisions can still occur and result in entire packet transmission time being wasted. Propagation delay means that two nodes may not hear each other's transmission, with distance & propagation delay playing a big role in determining collision probability.

<u>CSMA/Collision Detection (CSMA/CD)</u>
Key principle: CSMA but now collisions are detected within a short time and now colliding transmissions are aborted which reduce channel wastage as the entire frame is now no longer sent.

For collision detection, it's easy to do in wired LANs: you measure signal strengths, compared transmitted and received signals, but is difficult to do in wireless LANs due to the receiving signal strength being too weak in comparison to the local wireless transmission strength of wireless LANs

As soon as yellow and red frames detect collisions (checkerboard pattern), they stop descending straight down and only the residual packets finish their transmission to the left/right.
![[Pasted image 20250611021025.png]]

<u>Ethernet CSMA/CD Algorithm</u>
1. Network Interface Card (NIC), a piece of hardware that allows a computer or other network device to connect to a network, and is the physical link between your device and the network cable (or wireless signal), receives datagram from network layer and creates a frame
2. If NIC senses channel is idle, starts frame transmission. If NIC senses channel is busy, it waits until channel is idle then transmits.
3. If NIC transmits entire frame without detecting another transmission, NIC is done with the frame.
4. If NIC detects another transmission while transmitting, aborts and sends jam signal.
5. After aborting, NIC enters binary (exponential) blockoff:
	1. after mth collision, NIC chooses K at random from ${0, 1, 2,...,2^{m}-1}$. NIC waits K\*512 bit time and returns to step 2
	2. longer backoff interval with more collisions

<u>Efficiency</u>
![[Pasted image 20250611022838.png]]

<u>IEEE 802.11 LAN Architecture</u>
Wireless host communicates with base station = access point (AP). Basic Service Set (BSS) in infrastructure mode contains wireless hosts, AP/base station, and is for hosts only (ad hoc mode).
![[Pasted image 20250611023024.png]]

Hosts must associate with an AP by:
- scanning channels and listening for beacon frames containing the AP's name (SSID) and MAC address
- selecting the AP to associate with and maybe performing authentication
- typically running DHCP to get IP address in AP's subnet
![[Pasted image 20250611023219.png]]

![[Pasted image 20250611023330.png]]![[Pasted image 20250611023713.png]]

<u>IEEE 802.11 MAC Protocol: CSMA/Collision Avoidance (CSMA/CA)</u>
Difficult to receive/sense collision when transmitting due to weak received signals and can't sense all collisions in any case, so the goal is to avoid collisions.

IEEE 802.11 is the widely recognized technical standard for Wireless Local Area Networks (WLANs). In simpler terms, it's the foundation for what we commonly know as Wi-Fi.

Sender:
- If it senses that the channel is idle for DIFS (distributed coordination function interframe space) then it transmits the entire frame with no cooldown
- ![[Pasted image 20250611024532.png]]
- if it senses the channel is busy then it starts a random backoff time, timer counts down while channel is idle and transmit when timer expires. however if there's no ACK increase random backoff interval and restart

Receiver:
- If frame received OK, return ACK after SIFS (short interframe space). ACK needed due to hidden terminal problem
	![[Pasted image 20250611024613.png]]

The idea is to allow the sender to "reserve" the channel rather than random access of dataframes to avoid collisions to long data frames. The sender first transmits a small request-to-send (RTS) packets to BS using CSMA. RTSs may still collide with each other but they're short. BS broadcasts clear-to-send CTS in response to RTS which is heard by all nodes, so the original sender transmits data frame while other stations defer transmissions. So we essentially avoid data frame collisions completely using small reservation packets
![[Pasted image 20250611024749.png]]
![[Pasted image 20250611024804.png]]

**"Taking Turns" MAC Protocols**
Taking turns protocols look to combine the best of both worlds for channel partitioning and random access protocols.

<u>Polling</u>
Main node invites worker nodes to transmit in turn; typically used with "dumb" worker devices.

Concerns:
- polling overhead
- latency
- single point of failure (main node)
![[Pasted image 20250611025135.png]]

<u>Token Passing</u>
Control token passed from one node to next sequentially.
![[Pasted image 20250611025314.png]]
Concerns:
- token overhead
- latency
- single point of failure (token)

**MAC Addresses**
MAC addresses are 32-bit IP addresses that serve as the network-layer address for interface and used for network layer forwarding.

For MAC/LAN/physical/Ethernet address, it is used "locally" to get frame from one interface to another physically-connected interface (same network in IP-addressing sense).
- 48 bit MAC address (for most LANs) burned in NIC ROM and also sometimes software settable

MAC address allocation administered by the IEEE with the manufacturer buying a portion of MAC address space (to assure uniqueness).

Analogy: MAC address is like SSN, IP address is like postal address. MAC addresses are flat and can be portable (moved from one LAN card to another) while IP addresses depends on IP subnet node is attached to

<u>LAN Addresses</u>
Each adapter on LAN has a unique LAN address
![[Pasted image 20250611025545.png]]

**Address Resolution Protocol (ARP)**
Used to determine an interface's MAC address knowing its IP address.

ARP Table: Each IP node on LAN has a table of IP/MAC address mappings for some LAN nodes and a TTL for it typically 20min: <IP address; MAC address; TTL> 

<u>ARP, Same LAN</u>
- A wants to send datagram to B but B's MAC address is not in A's ARP table
- A broadcasts ARP query packet containing B's IP address
	- dest MAC address = FF-FF-FF-FF-FF-FF
	- all nodes on LAN receive ARP query
- B receives ARP packet, replies to A with its MAC address (frame is sent to A's MAC address)
- A caches IP-to-MAC address in ARP table until TTL expires

<u>ARP, Routing to Another LAN</u>
Want to send datagram from A to B via R.
![[Pasted image 20250611030247.png]]
Assuming that A knows B's IP and A knows IP of first hop router (via DHCP) and knows R's MAC address (ARP).

A creates an IP datagram with IP source A, destination B and creates link-layer frame with R's MAC address as destination frame containing A-to-B IP datagram
![[Pasted image 20250611030328.png]]
The frame is sent from A to R and received at R. The datagram is removed and passed up to IP. R forwards the datagram with IP source A, dst B and creates a link-layer frame with B's MAC address as dst frame containing A-to-B's IP datagram

**Ethernet**
The "dominant" and widely used wired LAN technology. It is super simple and cheaper than token LANs and ATM.

<u>Physical Topology</u>
Used to use bus in mid 90s where all nodes in same collision domain (can collide with each other).
![[Pasted image 20250611031342.png]]
Now use a star where you have an active switch the center and each "spoke" runs a separate Ethernet protocol so nodes do not collide with each other.
![[Pasted image 20250611031352.png]]

<u>Frame Structure</u>
Sending adapter encapsulates IP datagram (or other network layer protocol packet) in Ethernet frame
![[Pasted image 20250611031617.png]]

- preamble
	- 7 bytes with pattern 10101010 followed by one byte with pattern 10101011
	- used to synchronize receiver, sender clock rates
- addresses: 6 byte source, destination MAC addresses
	- if adapter receives frame with matching destination address or with broadcast address (like ARP packet) it passes data in frame to network layer protocol
	- otherwise adapter discards frame
	- type: indicates higher layer protocol (mostly IP)
	- CRC: cyclic redundancy check at receiver
		- error detected: frame is dropped

Properties:
- connectionless: no handshaking between sending and receiving NICs
- unreliable: receiving NIC doesn't send ACKs or NACKs to sending NIC
	- data in dropped frames recovered only if initial sender uses higher layer rdt (like TCP) otherwise dropped data lost
- Ethernet's MAC protocol: unslotted CSMA/CD with binary backoff

<u>Ethernet Switch</u>
A switch is a link-layer device which stores and forwards Ethernet frames. It examines incoming frame's MAC address and selectively forwards the frame to one or more outgoing links when frame is to be forwarded on segment. It uses CSMA/CD to access segment.

Hosts are unaware of the presence of switches (transparent), and switches do not need to be configured (plug-and-play, self-learning).

Hosts have dedicated, direct connections to the switch, and the switch buffers packets. Ethernet protocol is used on each incoming link but there's no collisions; full duplex. Each link is its own collision domain. Switching A to A' and B to B' can transmit simultaneously without collisions.
![[Pasted image 20250611032426.png]]

How does the switch know A' is reachable via interface 4 and B is reachable via interface 5?
- Each switch has a switch table where each entry has the MAC address of host, interface to reach host, and time stamp - similar to a routing table

How are entries created and maintained in the switch table?
- (self-learning) The switch learns which hosts can be reached through which interfaces. When the frame is received, the switch "learns" the location of sender: incoming LAN segment and records sender/location pair in switch table

(frame filtering/forwarding) When frame is received at switch, it:
- records the incoming link and MAC address of sending host
- Index switch table using MAC destination address
- ![[Pasted image 20250611032653.png]]

![[Pasted image 20250611032747.png]]

Switches can also be interconnected together:
![[Pasted image 20250611033002.png]]

![[Pasted image 20250611033011.png]]

![[Pasted image 20250611033046.png]]

**Data Center Networks**
![[Pasted image 20250611033127.png]]
![[Pasted image 20250611033144.png]]