# Analysis Misc

## Objectives

Miscellaneous Notes from classes and online resources. Helpful for packet analysis.

## OS Version Identification from Clients

Microsoft uses the following "NT" release versions indicate OS versions - this is found in User Agent strings:

```text
Windows NT 10.0: Windows 10/Server 2016
Windows NT 6.3: Windows 8.1/Server 2012 R2
Windows NT 6.2: Windows 8/Server 2012
Windows NT 6.1: Windows 7/Server 2008 R2
Windows NT 6.0: Windows Vista/Server 2008
Windows NT 5.2: Windows Server 2003 R2
Windows NT 5.1: Windows XP/Server 2003
```

## Alternate Data Stream Zone Identifier

Alternate Data Stream zone identifier that indicates the network “zone” :

```text
Local Computer—Zone.Identifier:$Data == 0
Local Network—Zone.Identifier:$Data == 1
Trusted—Zone.Identifier:$Data == 2
Internet—Zone.Identifier:$Data == 3
Restricted—Zone.Identifier:$Data == 4
```

## Validate Startup Registry Entries:

```text
reg query HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run
reg query HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Runonce
reg query HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\RunonceEx
```

To find all files with the Zone.Identifier ADS

```text
C:\> dir /R /s | find "Zone.Identifier"
```

## Packet Analysis Tips

Look for GET or POST Requests with strings and tshark:

```text
strings /pcaps/zeus-gameover-loader.pcap | egrep 'GET|POST'
tshark -nr /pcaps/zeus-gameover-loader.pcap -Y "http.request.method==GET"
```

Strings to look for User-Agent and then shortest user agents:

```text
strings /pcaps/<pcap> | grep "User-Agent:"
strings /pcaps/tijcont.pcap | grep User-Agent| sort -u| awk '{print length, $0;}' | sort –nr
```

View X.509 Issuer Fields:

```text
zeek -C -r /pcaps/normal/https/alexa-top-500.pcap
cat ssl.log | zeek-cut issuer| sort | uniq -c | sort -rn | less
tshark -r /pcaps/normal/https/alexa-top-500.pcap -T fields -R "ssl.handshake.certificate" -e x509sat.printableString
```

Shellshock User Agent:

```text
User-Agent: () { 42;};echo;/bin/cat /etc/passwd
```

Ptunnel Setup:

```text
Client is 192.168.1.34
Proxy is 192.168.1.44
Server is 192.168.1.30
```

A ptunnel session is started on host proxy as follows:

```text
./ptunnel
```

On Client do the following to connect to remote machine:

```text
./ptunnel -p 192.168.1.44 -lp 8000 -da 192.168.1.30 -dp 22 &
ssh -p 8000 localhost
```

