# ngrep

## Objectives

ngrep tips and tricks

## ngrep basics

Adding Timestamps

```text
ngrep -qt 'HTTP'
```

Reading from pcap

```text
ngrep -I network_capture.pcap -qt 'HTTP'
```

Matches all headers containing the string 'HTTP' sent to or from the ip address starting with 192.168:

```text
ngrep -q 'HTTP' 'host 192.168'
```

Match a destination host

```text
ngrep -q 'HTTP' 'dst host 192.168'
```

Match a source host

```text
ngrep -q 'HTTP' 'src host 192.168'
```

Ngrep for Protocols:

```text
ngrep -q 'HTTP' 'tcp'
ngrep -q 'HTTP' 'udp'
ngrep -q 'HTTP' 'icmp'
```

Ngrep for Port \#:

```text
ngrep -q 'HTTP' 'port 80'
```

Example: Basic Packet Sniffing

```text
ngrep -d any port 25
ngrep -d any 'error' port syslog
ngrep -wi -d any 'user|pass' port 21
```

Example: Debugging HTTP interactions

```text
ngrep port 80
ngrep -W byline port 80
```

Example: Processing PCAP dump files, looking for patterns

```text
ngrep -O /tmp/dns.dump -d any -T port domain
ngrep -w 'm' -I /tmp/dns.dump
ngrep -tD ns3 -I /tmp/dns.dump
ngrep -I /tmp/dns.dump port 80
```

Monitor all traffic not going over port 22 \(i.e., SSH\):

```text
ngrep not port 22 | strings 8
```

Monitor all traffic coming from a certain host:

```text
ngrep host 192.168.0.103
```

Capture network traffic incoming/outgoing to/from eth0 interface and show parameters following HTTP \(TCP/80\) GET or POST methods

```text
ngrep -l -q -d eth0 -i "^GET |^POST " tcp and port 80
```

Capture network traffic incoming/outgoing to/from eth0 interface and show the HTTP \(TCP/80\) User-Agent string

```text
ngrep -l -q -d eth0 -i "User-Agent: " tcp and port 80
```

Capture network traffic incoming/outgoing to/from eth0 interface and show the DNS \(UDP/53\) querys and responses

```text
ngrep -l -q -d eth0 -i "" udp and port 53
```

Show the query and results of SELECT queries going to your MySQL server:

```text
ngrep -d eth0 -i 'select' port 3306
```

