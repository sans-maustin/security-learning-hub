# Tshark

## Objectives

We'll go over some examples on how to use and leverage tshark.

## View Packet Stats

View TCP, UDP, and IP Conversations

```text
tshark -n -r <pcap> -z conv,tcp -q | more
tshark -n -r <pcap> -z conv,udp -q | more
tshark -n -r <pcap> -z conv,ip -q | more
```

Determine protocols in pcap:

```text
tshark -r nmap_sn.pcap -q -z io,phs
```

Calculate HTTP Packet Distribution:

```text
tshark -nnz http,tree -r pcap
```

Calculate HTTP Stats:

```text
tshark -nnz http,stat -r pcap
```

To generate a list of hostnames and associated IPs, use:

```text
tshark -q -r <pcap> -z hosts
```

To get a sense of which IPs generate the most traffic in the capture

```text
tshark -q -r <pcap> -z endpoints,ip
```

## Tshark Basics

Capture 100 packets while writing the output to the screen:

```text
tshark --interface en0 --autostop packets:100
```

Read packet in Local Time and then in UTC

```text
tshark -n -r <pcap> -c 1 -t ad
tshark -n -r <pcap> -c 1 -t ud
```

Display Filter for TCP Port 80 and NOT filter for same:

```text
tshark -Y '(tcp.dstport == 80)'
tshark -Y '!(tcp.dstport == 80)'
```

Follow conversation stream in tshark:

```text
tshark -r ftp.pcap -q -z follow,tcp,ascii,10.0.0.102:33496,10.0.0.105:21
```

Traffic in between hosts:

```text
tshark -r pcap -R "ip.addr==192.168.1.89 && ip.addr ==67.11.3.2"
```

Calculate how many packets/frames have been transfered in 60 seconds:

```text
tshark -a duration:60 -z io,stat,60
```

Display source and destination fields:

```text
tshark -nni eth0 -T fields -e ip.src -e ip.dst
```

Tshark - Identify 6to4 traffic in pcap:

```text
tshark -nr ipv6-4.pcap ip.proto == 6
```

## Tshark HTTP Analysis

HTTP Traffic

```text
tshark -nnr pcap http|grep GET
tshark -r pcap "tcp contains GET"
```

Export HTTP Packets from pcap:

```text
tshark --export-objects http,/tmp/output -q -r <pcap to extract from>
```

Look at HTTP response codes in packet and find the stream and then follow the stream:

```text
tshark -nnr PCAP -Y ""http.response.code"" -T fields -e tcp.stream -e http.response.code
tshark -nnr PCAP -q -z follow,tcp,ascii,7
```

Look for SIP/DIP/HTTP Host and URI:

```text
tshark -nnr <pcap> -Y "http.response_for.uri" -T fields -e tcp.stream -e ip.src -e ip.dst -e http.host -e http.response_for.uri
```

Grab SIP/DIP and User Agent:

```text
tshark -r <PCAP>  -Tfields -e ip.src -e ip.dst -e http.user_agent -Y "http.user_agent"
```

## Tshark DNS Analysis

Export DNS traffic to another file:

```text
tshark -r nmap_host_scan_tcp.pcap -Y '(tcp.port == 53)' -w dns.pcap
```

Find out what DNS servers are in use:

```text
tshark -r dns.pcap -T fields -e ip.src -e ip.dst -e dns.qry.name -e dns.txt | more
```

Identify packets with specific PDF and follow stream:

```text
tshark -nr <pcap> -Y 'frame contains ""pdf641""' -T fields -e tcp.stream
tshark -nr <pcap> -q -z follow,tcp,ascii,632
```

Tshark Export all domains visited via http from pcap to text files and then we compare those domains to known bad domains:

```text
tshark -Y http.request -T fields -e http.host -r <pcap> |sort -u >domains
wget -q http://dns-bh.sagadc.org/justdomains -O mal
grep -f domains mal
```

## Tshark SSH and SMB Analysis

Decode packets in to another protocol - here SSH to SMB \(nbss\):

```text
tshark -r decode-as-ssh.pcap -d tcp.port==22,nbss -c 20
```

Determine SSH Versions in use in pcap:

```text
tshark -r ssh.pcap -T fields -e ssh.protocol | sort | uniq --count | sort --numeric --reverse
```

Look for SMB Path Used:

```text
tshark -r 445.pcap -T fields -e smb.path | sort | uniq
```

Export SMB Packets from pcap:

```text
tshark --export-objects smb,/tmp/output -q -r <pcap>
```

## ICMP Tshark/tcpdump Examples:

ICMP Proto:

```text
ip[9] = 0x01
```

Not ICMP Proto:

```text
ip[9] & 0x01 !=1
```

ICMP Echo Packets

```text
icmp[0]=8
```

ICMP Reply Packets

```text
icmp[0]=0
```

ICMP Address Mask Request

```text
icmp[0]=0x11
```

## TCP Tshark Examples

Calculate HTTP Packet Distribution:

```text
tshark -nnz http,tree -r pcap
```

Calculate HTTP Stats:

```text
tshark -nnz http,stat -r pcap
```

Source IP Address and User Agent:

```text
tshark -nni en0 -Tfields -e ip.src -e http.user_agent -R "http.user_agent"
```

Source IP Address with Port:

```text
tcpdump -r mypcap|cut -f 3 -d" "|head
```

Display source and destination ip addresses and ports:

```text
tshark -nni eth0 -T fields -e ip.src -e tcp.srcport -e ip.dst -e tcp.dstport
```

HTTP Traffic:

```text
tshark -nnr pcap http|grep GET
```

Treat all traffic in a pcap as http running on a specific port:

```text
tshark -d tcp.port==29008,http
```

Look for TCP get requests:

```text
tshark -r pcap "tcp contains GET"
```

Source IP and DNS Query:

```text
tshark -nni en0 -e ip.src -e dns.qry.name -E separator=";" -T fields port 53
```

## Tshark Packet Slicing and Analysis

Sort and count unique http hosts:

```text
tshark -nnr http-cheez101.pcapng -Y "http.host" -T fields -e http.host|sort -rn |uniq -c|sort
```

Sort and count TLS hosts:

```text
tshark -nnr dumpcap-02282020-cap -T fields -e tls.handshake.extensions_server_name -Y "tls.handshake.extensions_server_name" |sort -rn |uniq -c|sort
```

## Tshark Security Uses

Get smb accounts:

```text
tshark –r <pcap> –T fields –e frame.time_relative –e ip.src –e ip.dst –e ntlmssp.auth.domain –e ntlmssp.auth.username –R ntlmssp.auth.username -2
```

Look for SQL injection:

```text
tshark –r <pcap> –T fields –e ip.src –e ip.dst –e frame.time_relative –e data –R ‘frame contains “SELECT”’ -2
```

Search TCP packet with given text

```text
tshark –r <pcap> –T fields –e ip.src –e ip.dst –e frame.time_relative –e data-text-lines –V –R ‘data-text-lines contains “UNION” and data-text-lines contains “SELECT”’ -2
```

Examine DNS queries and responses \(look for C&C covert channel\): see zeek below – this outputs a lot of whitespace

```text
tshark –n –r whatisthis.pcap –T fields –e ip.src –e ip.dst –e dns.qry.name –e dns.resp.name –e dns.resp.addr –E header=y
```

ICMP data \(look for covert channel\)

```text
tshark –n –r whatisthis.pcap –T fields –e ip.src –e ip.dst –e data –Y “icmp”
```

