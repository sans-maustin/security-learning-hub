# Wireshark

## Objectives

Wireshark tips

## Basic Stuff

Some Basics:

```text
Timespan of pcap            Statistics -> Summary
Protocols used in a pcap            Statistics -> Protocol Hierarchy
Top talkers:                Statistics -> Conversations
Top talkers (better):            Statistics -> Endpoints, IPv4 tab, click ‘Tx Packets’ column (or Tx bytes)
Case-sensitive string search:        frame|tcp|http contains “HTTP”
Case-insensitive string search:        frame|tcp|http matches “(?i)http”
Hex search:                frame|tcp|http contains 90:90:90:90
low > low comms:     tcp.srcport < 1025 && tcp.dstport < 1024
high > high comms:    tcp srcport > 1023 && tcp.dstport > 1023
Unusual TCP flags:    tcp && tcp.flags != 2 && tcp.flags != 16 && tcp.flags != 18 && tcp.flags != 24
Strip TCP,UDP,ARP:    !ip.proto == 6 && !ip.proto == 17 && !arp
‘Nmap’ in packet:    frame matches “(?i)nmap”
‘Nikto’ in packet:    frame matches “(?i)nikto”
metreperter http:    Mozilla/4.0 (compatible; MSIE 6.1; Windows NT)
noop sled (plain):    frame|tcp|http contains 90:90:90:90
HTTP errors:        frame matches “404”
Frags (wireshark):    ip.flags.mf == 1 or ip.frag_offset != 0
```

IPv6:

```text
ip6
```

ICMPv6

```text
icmpv6
```

Eliminate icmpv6, tcp, udp next headers in Wireshark:

```text
!(ipv6.nxt == 58 || ipv6.nxt == 17 || ipv6.nxt == 6)
```

Router Lifetime different from default of 1800:

```text
icmpv6.nd.ra.router_lifetime > 1700
```

## Searching for data in Packets

Frame Matches:

```text
frame matches "[Ll]uke"
```

Finding the words cat or dog in upper case or lower case anywhere in a trace file, we can use the filter:

```text
frame matches "(?i)(cat|dog)"
```

Wireshark Display Filters [https://packetlife.net/media/library/13/Wireshark\_Display\_Filters.pdf](https://packetlife.net/media/library/13/Wireshark_Display_Filters.pdf)

