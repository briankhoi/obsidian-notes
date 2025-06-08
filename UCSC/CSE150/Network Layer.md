**Traditional Computer Network Architecture**
A traditional computer network is composed of two layers: the data plane and the control plane. There is also a management plane on top but it represents humans managing the network.

<u>Data Plane:</u>
![[Pasted image 20250527085737.png]]

<u>Control Plane</u>:
![[Pasted image 20250527085747.png]]

<u>Management Plane:</u>
![[Pasted image 20250527085816.png]]

**Software Defined Networking**
Define network specs via software.
![[Pasted image 20250608133649.png]]
OpenFlow, a SDN approach, allows you to define and unify different types of network components. It has simple packet handling rules that have patterns matching packet header bits to filter different types of packets, actions on what to do with the packet after it has been pattern matched/default, and a priority rule system where certain rules are applied/matched first. Here are some common examples on packet handling across the different components:
![[Pasted image 20250608135036.png]]

Examples of packet handling:
![[Pasted image 20250608135317.png]]
![[Pasted image 20250608135324.png]]

OpenFlow also defines a POX controller which allows you to create a flow table. Flow tables are data structures in an OpenFlow device/switch that contains a set of flow entries dictating the actions of a packet. So instead of the packet going through the firewall every time, it instead goes through the flow table which essentially serves as a local cache of actions to take depending on the packet - if none of the flow entries match the packet then it goes to firewall.
![[Pasted image 20250608135521.png]]

**Network Layer**
The network layer is the 3rd layer in the IP stack, below transport and above data link. It is responsible for supporting the upper transport layer by transporting segments from the sending to receiving host. On the sending side, the network layer encapsulates segments into datagrams while on the receiving side, it delivers the received segments to the upper transport layer.

There are network layer protocols in every host and router, and the router examines the header fields in all IP datagrams passing through it.

<u>Key Functions</u>:
The network layer has two key functions:
- forwarding: moving packets from router's input to appropriate router output
- routing: determining the route taken by packets from source to destination (using routing algorithms)

The control plane determines the end to end route to take via a routing algorithm, and builds/adds on to a local forwarding table in each router that map header values (like destination IPs) to specific output links. This table is then referenced by the router when a new packet arrives.
![[Pasted image 20250608140459.png]]

**Network Architecture**
There are two fundamental architectures for packet-switched networks at the network layer: virtual-circuit networks and datagram networks. VC networks provide a connection service (similar to TCP is connection-oriented), while datagram networks provide a connectionless service.

<u>Virtual Circuits</u>
In the VC architecture, there is:
- no call setup, teardown for each call before data flow
- each packet carries VC ID (not dst IP)
- each router on source-dest path maitnains "state" for each passing connection
- VC can have dedicated sources (thus predictable service) by allocating links, bandwidth, buffer space, etc.

A VC consists of:
- Path from source-dest
- VC numbers, one number for each link along path
- Entries in forwarding tables in routers along path
VC numbers can be changed on each link via the forwarding table replacing it with a new one. VC is also a form of packet switching, not circuit switching but it does emulate circuit switching.

Each router in VC architecture also has a forwarding table with entries installed via the control plane's routing algorithm telling the router where to send each packet (the "connection state" info)
![[Pasted image 20250608141540.png]]

Misc:
![[Pasted image 20250608141745.png]]

<u>Datagram Networks</u>
Datagram networks are connectionless and thus have:
- no call setup at network layer
- packets (datagrams) are just sent individually, no connection initialized
- routers have no state about end-end connections
- packets are forwarding using dst host address

The datagram forwarding table has entries containing a destination address range, and from there if the dst IP falls into a range, it forwards it to the link interface associated with it
![[Pasted image 20250608142041.png]]
However, often times ranges don't divide up nicely. You can have overlapping ranges or not continuous ranges, and thus arises the need to use longest prefix matching.

Longest prefix matching is another way of storing entries in a forward table where instead of matching a dst IP through its fallen range, we match it with the longest address prefix that matches the dst address.
![[Pasted image 20250608142210.png]]

<u>Datagram vs. VC</u>
Datagrams are mainly the preferred network architecture for the Internet where data exchange among computers is seen as an elastic service with no strict timing requirements. The "smart" end systems can also adapt, perform control and error recovery. However, uniform service is difficult due to the amount of different link types. It has simple inside network complexity at the edge since routers are dumb (they just want to be as fast as possible so all they do is just lookup stuff in forwarding table) while edge is complex due to smart end systems handling complex aspects of reliable communication

Meanwhile, VC architecture is preferred for things like ATMs as VC itself evolved from telephone architecture. It supports strict timing, reliability requirements, and the need for guaranteed service. It has complexity inside network due to more sophisticated core routers and the network maintaining connection state.

**Router Architecture**
There are two key router functions:
- Run routing algorithms/protocols (RIP, OSPF, BGP)
- Forward datagrams from an incoming to outgoing link

The routing processor in control plane is responsible for computing routes and installing them in forwarding tables in the input ports. Input ports then lookup routes in their local forwarding table then tell the high-speed switching fabric to physically move the packet from input port to correct output port.
![[Pasted image 20250608142624.png]]

<u>Input Port</u>
Illustrating the journey of a packet, this is the inside view of what happens inside a router input port.
First, the line termination happens where the raw electrical signals are received physically and then converted to digital bits (0s/1s). After, the link layer protocol processes the packet to verify addresses and check for errors. Finally, the packet is passed to the network layer to lookup the output port using the forwarding table in input port memory and forward them to the right port via the switch fabric, trying to complete the whole operation at line speed. Queuing is also used if datagrams arrive faster than forwarding rate into switch fabric.
![[Pasted image 20250608143455.png]]
If the fabric is slower than the input ports combined, then the queuing delay and loss can occur due to input buffer overflow. This is where we have the problem of head of the line (HOL) blocking where the queued datagram at the front of queue prevents others in queue from moving forward.
![[Pasted image 20250608144425.png]]

<u>Switching Fabric</u>
The role of a switching fabric is to transfer a packet from input buffer to the appropriate output buffer. Its switching rate is defined as the rate at which packets can be transferred from inputs to output, where if you have N inputs, then the switching rate of N times the line rate is desirable. There are three different types of switching fabrics:
![[Pasted image 20250608144015.png]]

<u>Output Ports</u>
Essentially the reverse of an input port. Scheduling discipline chooses among queued datagrams for transmission.
![[Pasted image 20250608144105.png]]

For queuing and delay loss at output port buffering, with N flows it is recommended to be
$\frac{RTT \times C}{\sqrt{N}}$
where RTT is round trip time, C is link capacity

**Internet Protocol (IP)**
The Internet network layer:
![[Pasted image 20250608144817.png]]
![[Pasted image 20250608144835.png]]

**Datagram Fragmentation and Reassembly**
A lot of network links have MTUs (maximum transfer units) that vary and thus large IP datagrams must be divided (fragmented) within the network where one large datagram becomes several smaller datagrams and is reassembled only at the final destination via IP header bits to identify and order related fragments.
![[Pasted image 20250608145712.png]]
![[Pasted image 20250608145728.png]]

**IP Addressing**
An IP address is a 32-bit identifier for the host and router interface. An interface is a connection between the host/router and physical link, and routers typically have multiple interfaces where hosts typically have one or two (wired, Ethernet, wireless, etc.) and there are IP addresses associated with each interface.

**Subnets**
A subnet is a smaller region of a network where devices can physically each other without an intervening router and interface with the same subnet part of IP address. In an IP address, the subnet part represents the higher order bits while the host part represents the lower order bits.

to determine a subnet, detach each interface from its host or router, creating islands of isolated networks. Each isolated network is called a subnet.
![[Pasted image 20250608150436.png]]

Example:
How many subnets?
![[Pasted image 20250608150445.png]]
1. **Top Subnet:** Connects three hosts and one router interface (223.1.1.3).
    - IPs: 223.1.1.1, 223.1.1.2, 223.1.1.4 (hosts), 223.1.1.3 (router interface).
    - This is **Subnet 1**.
2. **Middle Left Link Subnet:** Connects two router interfaces (223.1.9.2 and 223.1.9.1).
    - IPs: 223.1.9.2 (top router), 223.1.9.1 (bottom left router).
    - This is **Subnet 2**.
3. **Middle Right Link Subnet:** Connects two router interfaces (223.1.7.0 and 223.1.7.1).
    - IPs: 223.1.7.0 (top router), 223.1.7.1 (bottom right router).
    - This is **Subnet 3**.
4. **Bottom Left Link Subnet:** Connects two router interfaces (223.1.8.1 and 223.1.8.0).
    - IPs: 223.1.8.1 (bottom left router), 223.1.8.0 (bottom right router).
    - This is **Subnet 4**.
5. **Bottom Left Host Subnet:** Connects two hosts and one router interface (223.1.2.6).
    - IPs: 223.1.2.1, 223.1.2.2 (hosts), 223.1.2.6 (bottom left router).
    - This is **Subnet 5**.
6. **Bottom Right Host Subnet:** Connects two hosts and one router interface (223.1.3.27).
    - IPs: 223.1.3.1, 223.1.3.2 (hosts), 223.1.3.27 (bottom right router).
    - This is **Subnet 6**.

Each shaded area in the diagram represents a distinct subnet. Counting these, we find **6 subnets** in the diagram.

**Classless InterDomain Routing (CIDR)**
CIDR is a way of IP addressing. Since the subnet portion of IP addresses can be of arbitrary length, we use a subnet mask (the prefix length of /x aka /23 in image part) to determine what parts belong to the subnet and which other bits belong to the host. So in the image below, /23 means the first 23 bits of the IP address define the network and all devices within the network/subnet have the first same 23 bits. Then, the remaining 32-23 = 9 bits make up the unique hosts.
![[Pasted image 20250608150826.png]]

**Dynamic Host Configuration Protocol (DHCP)**
Hosts obtain IP addresses through hard-coded files by system admins. In UNIX this is /etc/rc.config. 

In addition, DHCP allows hosts to dynamically obtain IP addresses from the network server when the host joins the network. DHCP supports the reuse of addresses (only holds address while host is connected), supports mobile users, and can renew its lease on addresses in use.

It works by:
- (optional) host broadcasting a "DHCP discover" msg
- (optional) DHCP server responding "DHCP offer"
- host requesting IP "DHCP request"
- DHCP server sending it back "DHCP ack"
![[Pasted image 20250608151509.png]]

DHCP can also be extended to more than just allocated IPs on the subnet. It can return the address of the first hop router for the client, the name and IP of the DNS server, and the network mask.

**Subnet IP Allocation**
Subnet IPs are allocated through the ISP, as the ISP gives away a portion of its address space. ISPs get their block of addresses through ICANN (Internet Corporation for Assigned Names and Numbers) which is an organization which allocates addresses, manages DNS, assigns domain names and resolves disputes
![[Pasted image 20250608155945.png]]

**Hierarchal Addressing**
Hierarchal addressing allows efficient advertisement of routing information. The way that it works is that ISPs have their own encompassing IP address blocks that get mapped to, then have organizations with their ranges fall under it so that routing tables don't need to store all IP addresses but rather the top level ones to be further evaluated.
![[Pasted image 20250608160104.png]]

**Network Address Translation (NAT)**
From the outside world, a local network just uses one IP address, however inside the local network there can be multiple hosts each with their separate IPs. So how do you let all the hosts access the Internet using just that one public IP? This is where network address translation (NAT) comes in. NAT is a method to remap one IP address space into another by modify network address information in the IP header of packets while they are in transit.
![[Pasted image 20250608161434.png]]

Motivations for NAT:
- Range of addresses not needed from ISP; just one IP for all devices
- Can change addresses of devices in local network without notifying outside world
- Can change ISP without changing addresses of devices in local network
- Devices in local network not explicitly addressable or visibly by the outside world (good for security)

Requirements for NAT:
A NAT router must:
- replace (source IP, port #) of every outgoing datagram to (NAT IP, new port #) for remote clients/servers to respond to using the NAT IP, new port # as the dst address
- remember in the NAT translation table every (source IP, port #) to (NAT IP, new port #) translation pair
- replace (NAT IP, new port #) in dst fields of every incoming datagram with corresponding (source IP, port #) stored in NAT table
![[Pasted image 20250608161848.png]]
If you have hosts that share one public facing IP, the NAT IP will always be that one public IP while ports will differ.

In NAT, there is a 16-bit port-number field, meaning that you can have 60,000 simultaneous connections with a single LAN-side address. NAT is also controversial because routers should only process up to layer 3 and it violates the end-end argument (NAT possibility must be taken into account by app designers). IPv6 could instead also solve the address shortage.

<u>NAT Traversal Problem</u>
There is a server in a local network that shares a single public-facing IP with all the other hosts on the network. An external client wants to talk to the server, but can't use its address of 10.0.0.1 since it's local to the network. However, if it uses its public facing IP, the NAT router won't know which device in the local network to send the request to.
![[Pasted image 20250608162917.png]]

Solution 1:
Statically configure NAT to forward incoming connection requests at given port to server (so like 123.76.29.7, port 2500) always forwarded to 10.0.0.1 port 25000)

Solution 2:
Universal Plug and Play (UPnP) Internet Gateway Device (IGD) Protocol. This allows the NAT'ed host to learn its public IP address and add/remove its own port mappings (with lease times) on the NAT router which basically automates the static NAT port map configuration.

So essentially, the internal device can send a request to the NAT router saying: "Please forward incoming traffic on a specific public port (e.g., TCP port 25000) to my internal IP address and port (e.g., 10.0.0.1:25000)."

Solution 3:
Relaying (used in Skype). Relaying is where the NAT'ed client and external client establish connections to the relay, which then bridges packets between two connections.
![[Pasted image 20250608163159.png]]

**Internet Control Message Protocol (ICMP)**
ICMP is a protocol used by hosts and routers to communicate network level information like error reporting of unreachable hosts/ports/protocols, and echo requests and replies (used by ping). ICMP serves as the network-layer "above" IP (ICMP messages are carried in IP datagrams) and ICMP messages are comprised of a type and code, then first 8 bytes of an IP datagram causing error.
![[Pasted image 20250608163457.png]]

<u>Traceroute</u>
Traceroute is a linux command that tracks the route that a packet takes from source to dst, tracing which routers and hops the packet traversed.

It works by first having the source send a series of UDP segments to the destination, where the first set has TTL=1. Then for each subsequent hop in the path, traceroute increments the TTL value by one (second set has TTL=2 and etc.) which allows the command to discover routers hop by hop. UDP segments are intentionally sent to a dst port number that is chosen to be very high and thus unlikely to be in use. As the packets travel, whent he TTL reaches 0, the router discards the packet and sends source ICMP messages (type 11, code ) which include the name of router and IP address. When the ICMP messages arrive back at the source, the source records the RTTs which gives an indication of the latency of the specific router.

Traceroute stops sending more probes when:
- UDP segment arrives at dst host
- dst returns ICMP "port unreachable" (type 3, code 3)
- source stops
![[Pasted image 20250608163600.png]]

**IPv6**
The motivation between IPv6 is that IPv4 addresses were soon to be completely allocated and that the header format changes improved speed processing and forwarding, and thus quality of service.

<u>Format</u>
Fixed-length 40 byte header with no fragmentation allowed
![[Pasted image 20250608164235.png]]

IPv6 also has other changes from IPv4, namely that the checksum was removed entirely to reduce processing time at each hop, the options field is still allowed but moved outside of header indicated by the "next header" field, and now has ICMPv6 which is the new version of ICMP including additional message types and multicast group management functions.

<u>Transition from IPv4 to IPv6</u>
Not all routers can be upgraded simultaneously, so how will our network operate with a mix of IPv4 and IPv6 routers?

The answer is tunneling, which is a mechanism where an IPv6 datagram is carried as a payload in an IPv4 datagram among IPv4 routers.
![[Pasted image 20250608164534.png]]
![[Pasted image 20250608164625.png]]

**Routing Algorithms**
<u>Graph Abstraction</u>
We define our network comprised of routers and links to be a graph, where nodes are routers and edges are links.
![[Pasted image 20250608164757.png]]

We define the cost of a path to be the sum of the weights of the edges.
![[Pasted image 20250608164910.png]]

Thus, it is in our best interest to find the least-cost path between two routers. This is where routing algorithms come in.

<u>Classification</u>
Static vs. Dynamic:
In static algorithms, the routes change slowly over time while in dynamic algorithms the routes change more quickly through periodic updates and in response to link cost changes.

Global vs. Decentralized:
In a global algorithm, all routers have completely topology and link cost information. These algorithms are referred to as "link state" algorithms.

Meanwhile, in a decentralized algorithm, the router knows physically connected neighbors, link cost to neighbors, and performs an iterative process of computation where their is an exchange of information with neighbors. These are called "distance vector" algorithms.