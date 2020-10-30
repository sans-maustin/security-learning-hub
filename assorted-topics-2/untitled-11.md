# Simple Oneliners

## Some simple scripts - more will be added here

Print the 1st 10 IP addresses in the 10.11.1 subnet:

```text
for ip in $(seq 1 10); do echo 10.11.1.$ip; done
```

Ping Sweep:

```text
for x in {1..254..1};do ping -c 1 10.0.0.$x |grep "64 b" |cut -d" " -f4 >> ips.txt;done
```

DNS Reverse Lookup Script:

```text
for ip in {1..254..1};do dig -x 1 10.0.0.$ip |grep $ip >> dns.txt; done;
```

NC Port Forwarding \[forward every request on port 8080 to port 80\]:

```text
while `nc -lp 8080 -c 'nc localhost 80'`;do;done
```

Netcat for 'Service is Dead' Notification:

```text
while `nc –vv –z –w3 [target_IP] [target_port] > /dev/null` ; do echo "Service is ok"; sleep 1; done; echo "Service is dead"; echo –e "\x07"
```

Heartbeat with Netcat:

```text
while (true); do nc –vv –z –w3 [target_IP] [target_port] > /dev/null && echo –e "\x07"; sleep 1; done
```

Netcat Port Scanning:

```text
echo ""|nc -v -z -n -w1 192.168.11.150 1-10000
```

To run tcpdump against all the pcap files in the directory:

```text
for capfile in $(ls *.pcap); do tcpdump -nnr $capfile 'dst port 53'|head -5;done;
```

Run hping on all addresses I n10.10.10.0/24 network:

```text
for i in `seq 1 255`; do hping --count 1 10.10.10.$i; done
```

Persistent NC Listener:

```text
while [ 1 ]; do echo “Started”; nc –l –p [port] –e /bin/sh; done
```

Automate a search with the list:

```text
for ip in $(cat list.txt); do host $ip.megacorpone.com; done
```

Using Bash to brute force reverse DNS names:

```text
for ip in $(seq 50 100); do host 38.100.193.$ip; done | grep -v "not found"
```

Ping Sweep Bash Script

```text
for i in `seq 1 255`; do ping -c 1 10.10.10.$i | tr \\n ' ' | awk '/1 received/ {print $2}';
done
```

Ping Sweep PowerShell Script

```text
1..20 | % {"192.168.1.$($_): $(Test-Connection -count 1 -comp 192.168.1.$($_) -quiet)"}
```

Check is website is Up:

```text
if curl -ski --head --request GET https://192.168.1.44 | grep "200 OK" > /dev/null; then
   echo "192.168.1.44 is UP"
else
   echo "192.168.1.44 is DOWN"
fi
```

Ping Loop

```text
nano pingloop.sh
for ip in $seq 200 210);do
ping -c 1 192.168.31.$ip | grep "bytes from"|cut -d" " -f 4|cut -d":" -f1 &
done
chmod 755 pingloop.sh
```

Forward Name Lookup Script

```text
nano fwdlookup.sh
#!/bin/bash
for name in ($cat list.txt);do
host $name.sans.org|grep "has address"|cut -d" "  -f1,4
done
```

Reverse Name Lookup Script

```text
nano revlookup.sh
#!/bin/bash
for ip in $(seq 72 91);do
host 38.100.193.$ip|grep "sans"|cut -d" " -f1,5
done
```

Zone Transfer Script

```text
nano zxfr.sh
#!/bin/bash
if [-z "$1" ]; then
    echo "[*] Simple Zone Transfer Script"
    echo "[*] Usage : $0 <domain name>"
    exit 0
fi

for server in $(host -t ns $1|cut -d" " -f4);do
    host -l $1 $server|grep "has address"
done
```

Mail Verify Users:

```text
for user in $(cat user.txt);do echo VRFY $user|ncat -nc -w 1 192.168.1.1 25 2>/dev/null|grep ^"250";done
```

