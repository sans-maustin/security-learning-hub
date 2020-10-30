# Packet Header Overview

## Overview

This will provide an overview of Ethernet/IPv4/IPv6/ICMP/TCP/UDP Packet Headers

## Ethernet Header Overview

Ethernet Frame Header:

```text
Dst MAC: 00-05 - eth.dst - 6-bytes
Source MAC: 06-11 - eth.src - 6-bytes
Type: 12-13 - eth.type - 2-bytes
```

Ethernet Frames: Majority of networks will be Ethernet II - 14-byte header + variable size payload + 4-byte CRC •Header = 6-byte destination MAC address + 6-byte source MAC address + 2-byte Ethernet type •Payload = Data that follows that specific Ether Type: maximum = 1500 bytes •CRC = 4 bytes

Dst MAC address —&gt; Src MAC address —&gt; Ether Type —&gt; Payload —&gt; CRC

ARP request, signified by the opcode of 0x0001

Ether Types:

```text
IPv4: 0x0800
IPv6: 0x86DD
ARP:  0x0806
VLAN: 0x8100
```

## IPv4 Packet Header Overview

Sample Packet:

```text
0x0000:  4500 0054 7fbc 0000 4001 7752 c0a8 011e  E..T....@.wR....
0x0010:  c0a8 012c 0800 296a 6e51 0000 5df6 a711  ...,..)jnQ..]...
0x0020:  0002 7037 0809 0a0b 0c0d 0e0f 1011 1213  ..p7............
0x0030:  1415 1617 1819 1a1b 1c1d 1e1f 2021 2223  .............!"#
0x0040:  2425 2627 2829 2a2b 2c2d 2e2f 3031 3233  $%&'()*+,-./0123
0x0050:  3435 3637                                4567
```

IP Header Name: Offset \(Wireshark Value\) - value - length:

```text
Version: 0 (Wireshark: ip.version) - 4 - 1-byte
Header Length: 0 (Wireshark: ip.hdr_len) - 5 - 1-byte
Diff Services Field: 1 (Wireshark: ip.dsfield) - 00  - 1-byte
Total Length: 2-3 (Wireshark: ip.len) - 0054  - 2-byte
Identification: 4-5 (Wireshark: ip.id) - 7fbc  - 2-byte
Flags: 6-7 (Wireshark: ip.flags) - 0000  - 2-byte
TTL: 8 (Wireshark: ip.ttl) - 40  - 1-byte
Protocol: 9 (Wireshark: ip.proto) - 01  - 1-byte
Header Checksum: 10-11 (Wireshark: ip.checksum) -7752  - 1-byte
Source IP: 12-15 (Wireshark: ip.src) - c0a8 011e  - 4-byte
Destination IP: 16-19 (Wireshark: ip.dst) -c0a8 012c  - 4-byte
```

Fragmentation:

```text
All related fragments should have the same source and destination IP addresses, protocols, and IP ID values
The flags [+] in tcpdump means more fragments follow
```

IP Options:

```text
The code is 0x83 for loose source routing, and 0x89 for strict source routing
Wireshark:
ip.opt.type == 0x83
ip.opt.type == 0x89
```

Popular Next Header Protocols

```text
ICMP Proto: ip[9] = 0x01
TCP Proto: ip[9] = 0x06
UDP Proto: ip[9] = 0x11
```

|  | IPv4 | IPv6 |
| :--- | :--- | :--- |
| **Loopback** | 127.0.0.1 | ::1/128 |
| **Link Local** | 169.254.0.0/16 | fe80::/10 |
| **Global Unicast** | Any Routable IP Address | 2000::/3 |
| **IPv4-Mapped** | N/A | ::ffff/96 |
| **IPv6 to IPv4** | N/A | 2002::/16 |
| **Documentation** | 198.18.0.0/15 | 2001:0db8::/32 |
| **Multicast** | 224.0.0.0/4 | ff00::/8 |
| **Teredo** | N/A | 2001:0000::/32 |
| **Private** | 10.0.0.0/8 192.168.0.0/16 172.16.0.0/12 | fc00::/7 fd00::/8 |

## IPv6 Packet Header Overview

IPv6 Packet Overview:

Sample IPv6 Packet:

```text
0x0000:  600b ce2d 02a5 11ff fe80 0000 0000 0000  `..-............
0x0010:  006d 64f1 1b36 033a ff02 0000 0000 0000  .md..6.:........
0x0020:  0000 0000 0000 00fb 14e9 14e9 02a5 fa7a  ...............z
0x0030:  0000 0000 0012 0008 0000 0001 085f 6169  ............._ai
0x0040:  7270 6f72 7404 5f74 6370 056c 6f63 616c  rport._tcp.local
0x0050:  0000 0c00 0107 5f72 646c 696e 6bc0 1500  ......_rdlink...
0x0060:  0c00 0106 5f75 7363 616e c015 000c 0001  ...._uscan......
0x0070:  075f 7573 6361 6e73 c015 000c 0001 075f  ._uscans......._
0x0080:  6970 7075 7362 c015 000c 0001 045f 6970  ippusb......._ip
```

IPv6 Header Name: \(Wireshark Value\) - value - length:

```text
Version: (Wireshark: ip6.version) - 6  - 1-byte
Traffic Class: (Wireshark: ipv6.tclass) - 00b - 4-bytes
Payload Length: (Wireshark:ipv6.plen) - ce2d - 2-bytes
Next Header: (Wireshark:ipv6.plen) - 11 - 1-byte
Hop Limit: (Wireshark: ipv6.hlim) - ff - 1-byte
Source IP: (Wireshark: ipv6.src) - fe80 0000 0000 0000 006d 64f1 1b36 033a - 16-bytes
Destination IP: (Wireshark: ipv6.dst) - ff02 0000 0000 0000 0000 0000 0000 00fb - 16-bytes
```

Popular IPv6 Addresses

```text
::/128     Unspecified (0.0.0.0, RFC3513)
::1/128     Loopback (127.0.0.1, RFC3513)
::ffff:0:0/96     IPv4 mapped addresses (RFC4291)
64:ff9b::/96     DNS64 mapping (RFC6052)
2001:db8::/32     For examples only (RFC3849)
fe80::/10     Link Local (RFC4291)
fc00:/7     Unicast Local (RFC4193) usually seen as fd00::/8
ff00::/8     Multicast (RFC3513)
ff05::         Site-Local (one location)
2000::/3     Global Unicast (RFC3587)
2002::/16     6-to-4 (RFC3056)
2001:0000::/32     Teredo (RFC4380)
3ffe::/16, 5f00::/8     6bone (obsolete)
```

Popular IPv6 Multicast Addresses \(all IP6 addresses beginning FF are multicast\)

```text
ff00::/12 – Reserved
ff01::/12 – Interface-local
ff02::/12 – Link-local
FF02::1 – All Nodes; Replaces broadcast
ff02::4-A                Specific Routing Protocols
FF02::2 – All Routers
FF02::2 – All link-local routers
FF02::16 – MLDv2 capable routers
FF02::FB – mDNS ("All DNS Servers")     
FF02::1:2 – DHCP Server & Relays FF05::1:3 – DHCP Servers
ff05::/12    -    Site-local
ff08:: Organization-Local (one organization)     
ff0e:: Global scope
ff10::/12 - Transient addresses
```

ICMPv6 Packet Details and Misc

```text
1 Unreachable (IPv4 = 3)
2    Packet too big
3    Time Exceeded
4    Parameter Problem
128 Echo Request  (IPv4 = 8)
129    Echo Reply (IPv4 = 0)
133    Router Solicitation (for auto configuration)
134    Router Advertisement (for auto configuration)
135    Neighbor Solicitation (used to be done via ARP)(like ARP request)
136    Neighbor Advertisement (used to be done via ARP)(like ARP reply)
Types 1–127 represent ICMPv6 error messages and should not be blocked.  
Types 128–255 are informational messages and may be considered for blocking.
```

## IPv6 Neighbor Solicitation and Advertisement Process

Suppose IPv6 host fe80::224:8cff:fe2b:d64e needs to ping fe80::784b:4d2c:14a7:9ad5, yet has no entry in its neighbor cache where it stores IPv6 address and link address pairings. It must send a Neighbor Solicitation \(NS\) using an ICMPv6 message with type 135 to discover the destination host's MAC address. If the target host is listening, it returns a Neighbor Advertisement message using ICMPv6 type 136. Also, for some added efficiency, all neighbors that see the NS message cache the source IP and MAC address pairing for the sender of the advertisement and maintain it in their neighbor cache for a limited time. The MAC address is returned in another ICMPv6 option: The target link layer address.

Exchange looks like: Neighbor Solicitation - ICMPv6 Type 135 Neighbor Advertisement - ICMPv6 Type 136 Echo - ICMPv6 Type 128 Eco Response - ICMPv6 Type 129

## IPv6 Router Solicitation and Advertisement:

Let's examine another exchange when a host joins a network. When host fe80::21b:63ff:fe94:b10e is first connected to the network or newly rebooted, it must know its first hop routers. It finds them by issuing a Router Solicitation message, ICMPv6 type 133, to the multicast address of ff02::2 that represents the "all routers" address. The router with IP address of fe80::21b:90ff:fe2d:e43 responds with a Router Advertisement, ICMPv6 type 134, to destination address of ff02::1, the multicast address of all hosts. This is sent as a multicast, not unicast message so that any listening hosts may learn this same information if they do not already know it.

Exchange looks like: Source --&gt; FF02::2 --&gt; Router Solicitation: ICMPv6 Type 133 Router Responds --&gt; FF02::1 --&gt; Router Advertisement ICMPv6 Type 134

## IPv6 Neighbor Discovery Attacks:

Neighbor Solicitation: Spoof NA with wrong link address DoS: Respond to NS with nonexistent link address Neighbor Unreachable Detection: Spoof response to NS with address not reachable Duplicate Address Detection: Spoof NS response with address already taken Router Solicitation: Spoof router advertisement to indicate attacker's IP address is the next hop router

## IPv6 General Information

The minimum supported MSS in IPv4 is 536 bytes, while it is 1220 in IPv6. Checksum: Optional in IPv4, mandated in IPv6 ICMP type 128 code 0 is now an echo request and ICMP type 129 code 0 is an echo reply. Recall that in IPv4, an echo request was type 8 code 0 and an echo reply was type 0 code 0. Instead of an ARP "who-has", you will see a "Neighbor Solicitation" message. The ICMP type 135, Code 0 is used. This is multicast to the "All Nodes" multicast address of FF02::1 . Neighbor Advertisement messages are sent in response to Neighbor Solicitation messages. The ICMPv6 type 134 and code 0 is used: Flags:  
R: Router flag. Set if sender is a router  
S: Solicited flag. Set if this is a response to a Neighbor  
Solicitation Message  
O: Overwrite flag. Advertisement overwrites existing cache  
Special multicast addresses mentioned:  
All Routers Addresses:

6to4 Tunnel  
Used to connect IPv6 networks via IPv4 RFC 3056 Addresses: 2002:IPv4 Address::/48 192.88.99.0/24: 6to4 relay router anycast address  
A global unicast address is one that can be connected to the Internet and is routable. Link local is limited to a physical link, not necessarily to a logical network address block.

## IPv6 Address Types:

Link-local, Unique Local Addresses \(ULA\), and Global Unicast addresses

```text
Link-local
Used on the local subnet only, network prefix begins with "fe80"
Every IPv6 host MUST have one
Not routable
Used for local communications (e.g. discovering neighbors ...)

Unique Local Addresses (ULA)
May be used on privately owned networks, network prefix begins with "fd00" o They are not routed publicly
Some organizations do not use these addresses (see notes)

Global Unicast Addresses o Routed publicly
Systems may have multiple Unique Local and Global Unicast Addresses
```

## IPv6 Extension Headers

```text
Hop-by-Hop (0) --> Options that need to be examined by all devices on the path.
Destination Option Header (60) --> Options that need to be examined only by the destination of the packet.
Routing (43) --> Methods to specify the route for a datagram (used with Mobile IPv6).
Fragmentation (44) --> Contains parameters for fragmentation of datagrams.
Authentication (51) --> Contains information used to verify the authenticity of most parts of the packet.
Encapsulating Security Payload (50) --> Carries encrypted data for secure communication.
No Next Header (59)
```

## ICMPv4 Packet Header Overview

Sample ICMP Packet:

```text
0x0000:  4500 0054 7fbc 0000 4001 7752 c0a8 011e  E..T....@.wR....
0x0010:  c0a8 012c 0800 296a 6e51 0000 5df6 a711  ...,..)jnQ..]...
0x0020:  0002 7037 0809 0a0b 0c0d 0e0f 1011 1213  ..p7............
0x0030:  1415 1617 1819 1a1b 1c1d 1e1f 2021 2223  .............!"#
0x0040:  2425 2627 2829 2a2b 2c2d 2e2f 3031 3233  $%&'()*+,-./0123
0x0050:  3435 3637                                4567
```

ICMP Header Name: Offset \(Wireshark Value\) - value - length:

```text
Type: 0 (Wireshark: icmp.type) - 08 -1-byte
Code: 1 (Wireshark: icmp.code) - 00 - 1-byte
Checksum: 2-3  (Wireshark: icmp.checksum) -296a - 2-bytes
Identifier: 4-5 (Wireshark: icmp.ident) - 6e51 -2-bytes
Sequence Number: 6-7 (Wireshark: icmp.seq) - 0000 - 2-bytes
Timestamp from icmp data (Wireshark: icmp.data_time): 8-15 - 5df6 a711 0002 7037 - 8-bytes
Data: The rest
```

ICMP Type/Code Messages:

```text
0000 Echo Reply
0300 Destination Unreachable
0300 Net Unreachable
0301 Host Unreachable
0302 Protocol Unreachable
0303 Port Unreachable
0304 Fragmentation Needed and Don't Fragment was Set
0305 Source Route Failed
0306 Destination Network Unknown
0307 Destination Host Unknown
0308 Source Host Isolated
0309 Communication with Destination Network is Administratively Prohibited
0310 Communication with Destination Host is Administratively Prohibited
0311 Destination Network Unreachable for Type of Service
0312 Destination Host Unreachable for Type of Service
0313 Communication Administratively Prohibited
0314 Host Precedence Violation
0315 Precedence cutoff in effect
0400 Source Quench (Destination is unable to process traffic - slow down)
0500 Redirect Datagram for the Network (or subnet)
0501 Redirect Datagram for the Host
0502 Redirect Datagram for the Type of Service and Network
0503 Redirect Datagram for the Type of Service and Host
1100 Time to Live exceeded in Transit
1101 Fragment Reassembly Time Exceeded
1200 Pointer indicates the error
1201 Missing a Required Option
1202 Bad Length
```

## General Notes on ICMP

There should never be an ICMP error message returned when traffic is sent to the broadcast address 255.255.255.255 There should never be an ICMP error message in response to an ICMP error message. A tracert consists of three ICMP echo requests with a TTL of 1, followed by three ICMP "time to live exceeded" messages. This cycle is repeated with a TTL value one more than the previous set until an echo reply is returned. Remember that an ICMP error message carries information about the packet that caused the issue.

## TCP Packet Header Overview

Sample TCP Packet:

```text
0x0000:  4500 006f 0000 4000 4006 ad78 c0a8 011e  E..o..@.@..x....
0x0010:  287a a2d0 cacc 01bb bddd 2606 cf5e 6fb6  (z........&..^o.
0x0020:  8018 1000 5de5 0000 0101 080a b816 b8c0  ....]...........
0x0030:  1da8 d928 1703 0300 36f4 b84d 8d9e 5606  ...(....6..M..V.
0x0040:  22c7 d527 6da2 c38b 9941 16a4 3371 113b  "..'m....A..3q.;
0x0050:  7997 b30f 74d1 bb93 ff1c 65fa 28ed f6bc  y...t.....e.(...
0x0060:  a414 8135 6f1e 1c0e 29e7 6caa 45eb ab    4567
```

TCP Header Name: Offset \(Wireshark Value\) - value - length:

```text
TCP Source Port: 0-1 (Wireshark: tcp.srcport) - cacc - 2-bytes
TCP Dst Port: 2-3  (Wireshark: tcp.dstport) - 01bb - 2-bytes
Sequence Number: 4-7 (Wireshark: tcp.seq) -bddd 2606 - 4-bytes
Ack Number: 8-11 (Wireshark: tcp.ack) - cf5e 6fb6 - 4-bytes
Header Length: 12 (tcp.hdr_len) - 80 - 1-byte
TCP Flags: 13-14 (tcp.flags) - 1810  - 2-bytes
TCP Window Size: 15-16 (tcp.window_size_value) - 005d - 2-bytes
Checksum: 17-18 (tcp.checksum) - e500 - 2-bytes
Urgent Pointer: 19-20 (tcp.urgent_pointer) - 0001 - 2-bytes
TCP Options: 20+ (tcp.options) - 12-bytes
```

## TCP Abnormalities:

```text
1) The source and destination ports are both 0; this port number is not used in normal traffic. 2) All the non-ECN flag bits are set (FSRP, ACK, and URG)—again, abnormal behavior.
3) The maximum segment size found in the TCP options is 0 [mss 0]. This is not a valid size to represent the maximum amount of TCP payload data that can be sent.
4) Another problem is a window size of 0; an initial connection (one with a SYN) would not have a zero-sized window. Truthfully, it is hard to categorize this as an initial connection with all the weird flags.
5) Both the sequence and acknowledgment number values are 0 (although Wireshark indicates this has a relative value of 1); again, this is not normal. If there is a valid connection of some sort, either or both of these will be non-zero.
6) Finally, look at the urgent pointer value after URG; it is 8768. This means it is pointing to the 8768th byte of the payload; however, there is no payload at that byte.

Remember that a receiving host silently discards packets with invalid checksums. Any IDS/IPS that does not do TCP checksum validation can be evaded.

A session termination has either or both the FIN or RST flags set. Yet, it may also have other flags set as well.
Session termination is accomplished either by a graceful FIN close where the ACK flag is also set or an immediate close where the RST aborts a session. It still has the ACK flag set, but it is the last record of the session.
```

## UDP Packet Header Overview

Sample Packet:

```text
Sample Packet:
0x0000:  4500 003f c9ac 4000 4011 ec84 c0a8 012d  E..?..@.@......-
0x0010:  c0a8 01ff ac18 3cf0 002b 49db 5345 4152  ......<..+I.SEAR
0x0020:  4348 2042 5344 502f 302e 310a 4445 5649  CH.BSDP/0.1.DEVI
0x0030:  4345 3d30 0a53 4552 5649 4345 3d31 0a    CE=0.SERVICE=1.
```

UDP Header Name: \(Wireshark Value\) - value - length:

```text
UDP Source Port: 0-1 (Wireshark: udp.srcport) - ac18 (44506) - 2-bytes
UDP Dst Port: 2-3 (Wireshark: udp.dstport) - 3cf0 (15600) - 2-bytes
Length: 4-5 (Wireshark: (udp.length) -002b (43) - 2-bytes
Checksum: 6-7 (Wireshark: udp.checksum.status) - 49db (18907) - 2-bytes
Data: 8-Onward (The rest)
```

Brief Packet Overview Paper: [https://www.cs.ryerson.ca/~zereneh/linux/PacketReading.pdf](https://www.cs.ryerson.ca/~zereneh/linux/PacketReading.pdf)

