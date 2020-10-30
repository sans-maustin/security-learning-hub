# Tcpdump

## Objectives

TCPdump tips

## Looking at SYN+FIN Packets

Find SYN, FIN: Most Exclusive Select any packet that has both SYN and FIN set, but no other flag bits set

```text
'tcp[13] = 0x03'
```

Find SYN, FIN: Less Exclusive Select any packet that has both SYN & FIN set, and can have any other flag bits set

```text
'tcp[13] & 0x03 = 0x03'
```

Find SYN, FIN: Least Exclusive Select any packet that has either SYN or FIN set; both may be set and can have any other flag bits set

```text
'tcp[13] & 0x03 != 0'
```

## Tcpdump Misc

Extract HTTP User Agents and Host Params:

```text
sudo tcpdump -nn -A -s1500 -l | egrep -i 'User-Agent:|Host:'
```

Top Hosts by Packets:

```text
sudo tcpdump -nnn -t -c 200 | cut -f 1,2,3,4 -d '.' | sort | uniq -c | sort -nr | head -n 20
```

Top Talkers:

```text
tcpdump -tnr PCAPFILE  | awk -F '.' '{print $1"."$2"."$3"."$4}' | sort | uniq -c | sort -n | tail -n 5
sudo tcpdump -nnn -t -c 200 | cut -f 1,2,3,4 -d '.' | sort | uniq -c | sort -nr | head -n 20
```

Unique & count of all IP to MAC packets

```text
tcpdump –en –r <pcap> ‘ip’ | cut –f 2,10 –d ‘ ‘ | cut –f 1-4 –d ‘.’ | sort | uniq –c | sort –k 3
```

Unique sort & count of IP to MAC with port:

```text
tcpdump –en –r <pcap> ‘ip’ | cut –f 2,10 –d ‘ ‘ | sort | uniq –c
```

Traffic for a specific IP and on one of 2 ports:

```text
tcpdump 'src 10.0.0.9 and (dst port 3389 or 22)'
```

Top 10 Destination IP Addresses:

```text
tcpdump -nnt pace.pcap 'tcp or udp' |cut -f 5 -d " "|cut -f 1-4 -d "."|sort|uniq -c|sort -nr|head
```

Exclude more commonplace TLDs:

```text
tcpdump -nnt pace.pcap 'port 53'|grep -Ev '(com|net|org|gov|mil|arpa)'|cut -f 9 -d " "|grep -E '[a-z]'
```

Exclude more commonplace TLDs and filter on names, not ip addresses \(grep -E '\[a-z\]'\):

```text
tcpdump -nnt pace.pcap 'port 53'|grep -Ev '(com|net|org|gov|mil|arpa)'|cut -f 9 -d " "|grep -E '[a-z]'
```

## IP Filtering Examples:

```text
ip[0] & 0x0f - low nibble, header length
ip[1] - should be 5
ip[2:2] - type of service
ip[4:2] - total length
ip[6] & 0x80 - Reserved Bit (possible use for ECN)
ip[6] & 0x40 -DF Bit
ip[6] & 0x20 -MF Bit
ip[6:2] & 0x1fff - fragment offset
ip[8] - fragment offset
ip[9] - protocol
ip[9] == 8    EGP
ip[9] == 9    IGP
ip[9] == 88    EIRGP
ip[9] == 50    ESP
ip[9] == 51    AH
ip[9] == 89    OSPF
ip[9] == 124    ISIS
other, see /etc/protocols
ip[10:2] - header checksum
ip[12:4] - source ip
ip[16:4] - destination ip
```

## IPv6 BPF Examples

TCP

```text
ip6 and (ip6[6] == 0x06)                    IPv6 TCP
ip6 and (ip6[6] == 0x06) and (ip6[53] == 0x02)            IPv6 TCP Syn
ip6 and (ip6[6] == 0x06) and (ip6[53] == 0x10)            IPv6 TCP ACK
ip6 and (ip6[6] == 0x06) and (ip6[53] == 0x12)            IPv6 TCP Syn/ACK
UDP
ip6 and (ip6[6] == 0x11)                    IPv6 TCP
```

ICMP

```text
(ip6[6] == 0x3a)    ICMP v6
(ip6[6] == 0x3a) and (ip6[40] == 0x01)    ipv6 and type 1     Dest Unreachable
(ip6[6] == 0x3a) and (ip6[40] == 0x02)    ipv6 and type 2     Packet too big
(ip6[6] == 0x3a) and (ip6[40] == 0x03)    ipv6 and type 3     Time exceeded in transit
(ip6[6] == 0x3a) and (ip6[40] == 0x04)    ipv6 and type 4     Parameter Problem
(ip6[6] == 0x3a) and (ip6[40] == 0x80)    ipv6 and type 128     Echo Request
(ip6[6] == 0x3a) and (ip6[40] == 0x81)    ipv6 and type 129     Echo Reply
(ip6[6] == 0x3a) and (ip6[40] == 0x86)    ipv6 and type 133     Router Solicitation
(ip6[6] == 0x3a) and (ip6[40] == 0x87)    ipv6 and type 134     Router Advertisement
(ip6[6] == 0x3a) and (ip6[40] == 0x88)    ipv6 and type 135     Neighbor Solicitation
(ip6[6] == 0x3a) and (ip6[40] == 0x89)    ipv6 and type 136     Neighbor Advertisement
```

## TCP BPF Examples

SYN and Rst Flags:

```text
tcp[13] = 6
```

SYN/ACK Flags:

```text
tcp[13] = 18
```

SYN or ACK Flags:

```text
tcpdump "tcp[tcpflags] & (tcp-syn)|tcp-ack)!=0"
'tcp[13] == 2 or tcp[13] ==16'
```

FIN

```text
tcp[13] = 0x01
```

SYN

```text
tcp[13] = 0x02
```

SYN+FIN

```text
tcp[13] = 0x03
```

Push+Ack

```text
tcp[13] = 0x18
```

Urgent Packets:

```text
'tcp[13] & &32!=0'
```

Ack Packets:

```text
'tcp[13] & 16!=0'
```

Other TCP Options:

```text
tcp[0:2] - src port
tcp [2:2] -dst port
tcp[4:4] - sequence number
tcp[8:4] -ack number
tcp[12] - header length
tcp[13] - tcp flags
tcp[tcpflags] =0x12 - syn and ack flags set
```

## UDP BPF

udp\[0:2\] - source port udp\[2:2\] - dst port udp\[4:2\] - datagram length udp\[6:2\] - UDP checksum

## SMB Message Types:

Even though there are 19 different SMB message types, there are only 10 that you will usually see in practice:

NegoitiateProtocol is used exclusively to determine which dialect of SMB the two hosts have in common. SessionSetup is used for user authentication negotiation. This will establish the credentials used for every other activity in this session. SessionLogoff is seen only at the very end of the session, immediately before the session closes. You can think of it as a "logoff" request. TreeConnect is used to create a connection to a resource of some kind. This could be \(and is usually\) the IPC$ share, though it might be to a file share, a named pipe, or some other service on the server. TreeDisconnect is a notification to the server that the connection to the associated resource is being released. You could think of it as unmounting a share, though it applies to named pipes and other resources, not only file shares. Create sounds as though it will create a file, but typically this is executed as a "Create or Open." It might be better to think of this as "Create a resource handle to let me access something" rather than as "Create a file." Close is analogous to the TreeDisconnect in that it release the file or other resource that was being accessed. Read will, not surprisingly, read data from a resource. Write will, as expected, write data to a resource. IOCtl executes a file system or device control call across the network. This would be something along the lines of peeking into a file or device, sending a copy request asking for a file to be copied from one directory to another on a file server, for example, reading file attribute information, etc.

