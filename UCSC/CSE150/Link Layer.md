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
Compared to parity checking, CRC is a more powerful error-detection mechanism. It is widely used in practice (Ethernet, 802.11 WiFi, ATM), and works by


![[Pasted image 20250610190727.png]]