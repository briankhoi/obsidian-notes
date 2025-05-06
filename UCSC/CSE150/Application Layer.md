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
These are essentially P2P architectures but require a central server to manage the peers
<u>Skype</u>: Voice-over-IP (VoIP) P2P application
- centralized server: finding address of remote party
- direct client to client connection for calls/messaging

<u>Instant Messaging</u>:
- chatting between two users (can be) P2P
- centralized server: detects client presence and location
	- user registers its IP address with central server when it comes online and contacts it to find the IP addresses of buddies

**File Distribution Time**
<u>Client-Server vs. P2P</u>
Given the following diagram, let's break down how much time we would need to distribute a file of size $F$ from one server to $N$ peers/clients where the peer/client upload/download capacity is a limited resource
![[Pasted image 20250505165834.png]]

Starting with the client-server model, the server must sequentially send (upload) N file copies while each client must download the file copy. We define:
- time for server to send one copy: $F/u_s$ 
- time for server to send N copies: $NF/u_s$
- minimum client download rate: $d_{min}$ 
- minimum client download time: $F/d_{min}$
Lastly, the time to distribute F to N clients using the client-server approach, denoted as $D_{c-s}$ is given as:
$D_{c-s}>=max\{NF/u_s, F/d_{min}\}$
This is basically saying the time is greater than the maximum between the time for the server to upload N copies of the file and the time it takes for a single client to download a copy of the file

Now moving on to the P2P model, the server must upload at least one copy, while each peer must download a file copy. However, in the P2P model, the peers act as both clients and servers, first downloading the copy and then uploading/sending the copy over to other peers who don't have it yet. From the client-server analysis, we know that:
- time for server to send one copy: $F/u_s$
- minimum client download time: $F/d_{min}$
We know that with $N$ peers we must download $NF$ bits overall. However, our download rate is constrained by the upload rate of how fast the peers or the server can upload/send the files over to all the peers, which is given by the server's upload rate $u_s$ and the sum of all the peer's upload rate $\sum u_i$. Thus, the time it would take for the clients to download $NF$ bits is given by: $NF/(u_s + \sum u_i)$
Putting this all together, we have:
$D_{P2P} >= max\{F/u_s, F/d_{min}, NF/(u_s + \sum u_i)\}$

Overall, we can see that P2P is much faster:
![[Pasted image 20250505172327.png]]

<u>BitTorrent</u>:
BitTorrent is a system where files are divided into 256 Kb chunks and a group of peers in the torrent exchange (send/receive) chunks of a file. It utilizes a tracker to track peers participating in the torrent.
![[Pasted image 20250505172529.png]]

A peer joining a torrent initially has no chunks and registers with the tracker to get a list of peers and connects to a subset of peers (neighbors). The peer then asks periodically to the other peers for the list of chunks that they have, then requests the missing chunks from peers, with the peer requesting the chunks that are the least common (rarest) first so they get replicated faster and don't disappear. 

While downloading chunks, the peer also uploads chunks to other peers and can change peers to exchange chunks with. A common mechanism for this is "tit-for-tat" where the peer only sends chunks to the top 4 peers who are sending the initial peer chunks at the highest rate. All the other peers are "choked", meaning that they do not get sent any data from the peer. This top 4 ranking is re-evaluated every 10 seconds, and every 30 seconds, the peer randomly selects another peer outside of the top 4 to start sending chunks to in hopes that they will also send data back and become a top 4 contributor ("optimistically unchoke" them). This practice is to prevent leechers and ensure that peers are actually contributing.

Once they have the entire file they can leave/stay. Peers can also come and go (churn).





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
	- has unreliable data transfer between sending and receiving process
	- does not provide reliability, flow or congestion control, timing, throughput guarantee, security, or connection setup
	- useful because it has much lower overhead than TCP, so it's faster and more efficient for apps where speed is important and some small data loss is fine (streaming audio/video, gaming, VoIP)
![[Pasted image 20250423182657.png]]

**Web and HTTP**
A web page consists of a base HTML file which includes several referenced objects. These objects can be HTML files, JPEG images, Java applets, audio files, etc. and are each addressable by a URL like so:
![[Pasted image 20250423182958.png]]

<u>HTTP</u>
Hypertext Transfer Protocol (HTTP) is the web's application layer protocol which utilizes a client-server model. 

The client initiates a TCP connection (creates socket) to the server on port 80 (web server), and the server accepts the connection. They then exchange HTTP messages (application-layer protocol messages) between the browser (HTTP client) and the Web server (HTTP server), then close the TCP connection once done.

- Non-persistent HTTP
	- at most one object sent over TCP connection, so downloading multiple objects requires multiple connections
	- Round Trip Time (RTT): time for a small packet to travel from client to server and back
	- non-persistent HTTP response time = 2 \* RTT  + file transmission time
		- because one RTT to initiate connection and one RTT for http request and first few bytes of HTTP response to return + file transmission time
	 ![[Pasted image 20250423185314.png]]
	- issues:
		- requires 2 RTTs per object
		- OS overhead for each TCP connection
		- browsers often open parallel TCP connections to fetch referenced objects
- Persistent HTTP 
	- multiple objects can be sent over a single TCP connection
	- server leaves connection open after sending response, then subsequent HTTP messages between same/client server are sent over the open connection, with clients sending requests as soon as it encounters a referenced object
	- persistent http response time = as little as one RTT for all the references objects

<u>Message Types</u>
There are two types of HTTP messages: request and response
![[Pasted image 20250423191345.png]]

The header lines are delimited from the data section through the header line that starts with the carriage return \r\n.

The status code appears in the first line, with some samples:
- 200 OK - request succeeded and the object is later in the message
- 301 Moved Permanently - requested object moved, new location specified later in the message
- 400 Bad Request - request not understood by server
- 404 Not Found - requested document not found on server
- 505 HTTP Version Not Supported

**Web Caches (proxy server)**
The goal of a proxy server is to satisfy a client's request without involving the origin server. The browser instead sends all its HTTP requests to a cache. If the object requested is in the cache, it returns it, if not it requests from origin server then returns the object to the client.

So in this case, the cache acts as both a client (to the origin server) and a server (to the client). Caches are typically installed by an ISP and are utilized for reducing response time for client requests and reducing traffic on a certain access link. However, caches aren't good when each client of the ISP requests different content, which wastes time on visiting the cache server.

Caching example:
Given an average object size of 100K bits, an average request rate from browser to origin server 15 requests/sec, RTT from institutional router to any origin server 2s, access link rate 1.54Mbps, what is the average data rate to browsers, access link utilization, and total delay?
![[Pasted image 20250424001921.png]]
First starting off with the average data rate to browsers, if we know the avg object size is 100K bits and the average request rate is 15 reqs/sec, then the average data rate is those two multiplied = 100K bytes * 15 reqs/sec = 1500 K bytes/sec = 1.5 Mbps.

Then, the <u>access link utilization</u> is given by **our data rate / access link rate** = 1.50 Mbps / 1.54 Mbps = 99%.

Lastly, the <u>total delay</u> is given by Internet delay + access delay + LAN delay. In our case the Internet delay = RTT so it's 2 seconds, and the access delay and LAN delay aren't given, but access delay is in the minutes range and LAN delay is microseconds.

In addition, note that if the link access rate was 15.4Mbps instead, the access link utilization would be 9.9% (denominator * 10 so this drops by a factor of 10), and the access delay would also be milliseconds now instead of minutes. However, achieving this needs increased access link speed which isn't too cheap.

Now, imagine we instead install a local cache which is rather cheap. Keeping the same given information from earlier, how do we calculate the access link utilization or total delay? Assume the cache hit rate is 0.4.
![[Pasted image 20250424001834.png]]Since we know the cache hit rate is 0.4, then that means 40% of requests are in the cache while 60% must be requested from the server by the cache. Thus, if 60% of requests use the access link and the data rate to browsers over access link is given at 1.5Mbps, then we have 0.6 * 1.5 Mbps = 0.9Mbps. Then, we do 0.9Mbps / 1.5 Mbps = 0.58 = 58% access link utilization.

As for the total delay, we calculate that by 0.6 * (delay from origin servers) + 0.4 * (delay when satisfied at cache) = 0.6(2.01) + 0.4(several milliseconds) = roughly 1.2 seconds, which is less than if we had a 154 Mbps link and cheaper too!
- i have no idea where 2.01 comes from, cause RTT is 2s, ask qian

**Electronic mail (email)**
Three major components:
- user agents: responsible for reading, composing, and editing mail messages 
	- like outlook or the iphone mail client
- mail servers
	- has a mailbox which contains incoming messages for users
	- has a message queue of outgoing mail messages
	- SMTP protocol between mail servers to send email messages
		- SMTP uses TCP to reliably transfer email messages from client to server (port 25)
		- direct transfer: sending server to receiving server
		- three phases of transfer: handshaking (greeting), transfer of messages, closure
		- uses persistent connections and requires message (header & body) to be in 7-bit ASCII
		- different with HTTP because HTTP is normally a pull model (client requests data from server) while SMTP is a push model (server sends data to another server without an initial request from the receiving server)
![[Pasted image 20250424003931.png]]

Example:
![[Pasted image 20250424004150.png]]

Mail access protocols:
- SMTP: delivery/storage to receiver's server
- mail access protocol: retrieval from server
	- POP: post office protocol - authorization, download
		- POP3 (version of POP) has two possible modes it can center itself on. "download and delete" mode downloads the mail to a client and deletes the message from the server, meaning that if you change client you can't see the new mail - mail is only local to one client. meanwhile "download and keep" mode keeps copies of messages on different clients. overall, POP3 is stateless across sessions meaning the server doesn't retain any information or context about a specific client's previous interactions
	- IMAP: Internet mail access protocol - more features including manipulation of stored messages on server
		- keeps all messages in the server
		- allows user to organize messages in folders
		- keeps user state across sessions (name of folders and mappings between message IDs and folder name)
	- HTTP - used in Gmail, Hotmail, etc.
![[Pasted image 20250424004424.png]]

**Domain Name System (DNS)**
Hosts use IP addresses (32 bit) for addressing datagrams (a unit of data transmitted over a network), but names like yahoo.com are used by humans instead. How do we map IP addresses to names and vice versa?

Domain Name System (DNS) is a system involving a distributed database and an application-layer protocol which addresses this issue.

Hosts and name servers communicate over DNS's application-layer protocol to resolve names (address/name translation).

The process of doing this translation is done within DNS' distributed, hierarchal database, where you go down the tree to get what you need.
![[Pasted image 20250424015138.png]]
For example, if you want the IP for amazon, you:
- query root DNS server to get the .com DNS server
- query .com DNS server to get amazon.com DNS server
- query amazon.com DNS server to get IP for amazon.com

Also note that DNS services on top of translation hostname to IP address, also have load distribution where you have replicated web servers resulting in many IP addresses corresponding to one name. This avoidance of centralization helps eliminate risk for high traffic, single point of failure, a distance centralized DB, and maintenance which don't scale well

<u>DNS root name servers</u>
- contacted by a local name server that cannot resolve the name of an IP address
- contacts the authoritative name server if name mapping not known, then gets it and returns it to the local name server
- 13 copies around the world including NASA ARC!!

<u>DNS top level domain (TLD) servers</u>
- responsible for com, org, net, edu, aero, jobs, musuems, and all top-level country domains like uk, fra, ca, jp

<u>Authoritative DNS servers</u>
- organization's own DNS servers, providing authoritative hostname to IP mapping for the organization's specific named hosts
- can be maintained by either organization or service provider

<u>Local DNS name server</u>
When the host initially makes a DNS query, it is sent to its local DNS server where there is a local cache of recent name-to-address translation pairs (can be out of date). If the local DNS doesn't have the query answer in its cache, it acts as a proxy and forwards the query into the DNS hierarchy of root name servers, TLD servers, and then authoritative servers.
- Each ISP (residential ISP, company, university) has one
- also called "default name server"

<u>DNS Name Resolution</u>;
Scenario: Host at cis.poly.edu wants IP address for gaia.cs.umass.edu

Iterated query: each server contacted replies to host with the name of the next server to contact to get information (working down the DNS hierarchy) until the authoritative DNS server returns the IP to the host
![[Pasted image 20250505162257.png]]

Recursive query: Each contacted server contacts the next server in the hierarchy instead of the host (so the burden of name resolution is on the contacted name server) which can create a heavy load at upper levels of hierarchy
![[Pasted image 20250505162420.png]]

<u>DNS Caching</u>
While the most impactful type of caching occurs in the local DNS name server, any name server can actually cache the name to IP address mapping after it learns it.
- cached entries timeout (disappear) after some time, denoted by its TTL (time to live). they can also be out of date (best effort name to address translation) if the name host changes IP address and might only be updated internet-wide once all TTLs for that entry expire
- TLD servers are typically cached in local name servers, so that root name servers are not often visited
