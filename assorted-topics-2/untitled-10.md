# Snort

## Overview

Snort, tcpflow and tcpreplay

## Snort Basics

| Task | Command |
| :--- | :--- |
| Dumps Verbose output: | snort -v |
| Packet payloads \(application data\) TCP/UDP/ICMP headers: | snort -vd |
| Dumps full packet information \(TCP/UDP/ICMP/IP/MAC headers and payload\): | snort -vde |
| Operates @ layers 3,4: | snort -v -K ascii -l ./log |
| Operates @ layers 2,3,4 in OSI model: | snort -ve -K ascii -l ./log |
| Operates @ layers 2,3,4,5,6,7: | snort -vde -K ascii -l ./log |
| Snort to run and log relative to local network: | snort -dev -l ./log -h 192.168.1.0/24 |
| Snort log to the default facility in /var/log/snort and send alerts to a fast\(other options are: unsock, none, console, cmg\) alert file: | snort -c snort.conf -A fast -h 192.168.1.0/24 |
| Enable IDS mode of snort : | sudo snort -A console -i ens33 -c /etc/snort/snort.conf |
| Running Snort against pcap and using config: | snort -c /etc/snort/snort.conf -r snort.pcap -A full |
| Execute Snort in Logger mode \(Creates a TCPDump-compliant file in: /var/log/snort\) | snort -b |
| Execute Snort in Sniffer mode | 'snort -v -i eth0' - default sniff to STDOUT \(Console\) |
| Running Snort as a Daemon | /usr/local/bin/snort -d -h 192.168.1.0/24 -l /var/log/snortlogs -c /usr/local/etc/snort.conf -s -D |
| Execute Snort in Reader mode | snort -vd -O -r filename - obfuscates IP subnets Note: This is a safe, sanitized way to share TCPDumps with others |
| Obfuscate IP subnets | snort -vd -B 10.10.10.0/24 -r filename |

## Snort Payload Detection Rule Options

This has been obtained from Cisco Live and Joel Elser Links, below:

1.Content 2.nocase 3.Offset 4.Depth 5.Distance 6.Within 7.fast\_pattern 8.pcre

1.Content The content match finds a static pattern in network data.

```text
content:"ABCD";
```

Content matches can be restricted to a payload location, such as the HTTP URI. Match "ABC" within the HTTP URI:

```text
content:"ABC"; http_uri;
```

POST /index.php HTTP/1.1 Host: example.com Content-Length: 28 Content-Type: application/x-www-form-urlencoded Cookie: this\_is\_a\_cookie=this\_is\_its\_value firstparam=one&secondparam=two

Buffers: http\_method http\_uri http\_header http\_cookie http\_client\_body http\_method - POST http\_uri - /index.php http\_header - Host: example.com http\_header - Content-Length: 28 http\_header - Content-Type: application/x-www-form-urlencoded http\_header - Cookie: this\_is\_a\_cookie=this\_is\_its\_value http\_cookie - this\_is\_a\_cookie=this\_is\_its\_value http\_client\_body - firstparam=one&secondparam=two

2.nocase Nocase makes a content match case insensitive. content matches are case sensitive by default.

```text
content:"ABCD"; nocase;
```

3.Offset The offset keyword allows the rule writer to specify where to start searching for a pattern within a packet. Meaning, start at the beginning of the data payload of the packet \(offset:0;\) and find the word GET. Offset starts counting at "0" bytes.

```text
content:"GET"; offset:0;
```

4.Depth If I want to tell Snort where to stop looking for a content match, I have to use something like depth. So for the above example, if I want to match on "GET" but only at the beginning of the data portion of the payload, use the following:

```text
content:"GET"; depth:3;
```

This will match "GET" within the first 3 bytes of the payload.

5.Distance Distance specifies how far into a payload Snort should ignore before starting to search for the specified pattern relative to the end of the previous pattern match.

```text
content:"GET"; content:"ABC"; distance:2;
```

6.Within Within makes sure that at most N bytes are between pattern matches. find "EFG" within 10 bytes of "ABC". Next is an example of negating EFG.

```text
content:"ABC"; content:"EFG"; within:10;
content:"ABC"; content:!"EFG"; within:10;
```

7.fast\_pattern Fast\_pattern explicitly specifies the content match within a rule to be used with the fast pattern matcher. The fast\_pattern serves as the “entrance” condition for rule evaluation.

```text
content:"ABC"; fast_pattern;
```

8.pcre Pcre declares a Perl compatible regular expression for matching on payload data. Flags can be specified after the slash. e.g. /i for case insensitivity

```text
pcre:"/A[BC]D/i"; \
```

[https://blog.joelesler.net/2010/03/offset-depth-distance-and-within.html](https://blog.joelesler.net/2010/03/offset-depth-distance-and-within.html) [https://www.ciscolive.com/c/dam/r/ciscolive/emea/docs/2016/pdf/DevNet-1693.pdf](https://www.ciscolive.com/c/dam/r/ciscolive/emea/docs/2016/pdf/DevNet-1693.pdf) [https://www.ciscolive.com/c/dam/r/ciscolive/emea/docs/2016/pdf/DevNet-1126.pdf](https://www.ciscolive.com/c/dam/r/ciscolive/emea/docs/2016/pdf/DevNet-1126.pdf)

## Security Onion Tips:

**Much more can be discovered here on Security Onion:**  
[https://docs.securityonion.net/](https://docs.securityonion.net/)

**More on restarting services in Security Onion:** [https://github.com/Security-Onion-Solutions/security-onion/wiki/Services](https://github.com/Security-Onion-Solutions/security-onion/wiki/Services)

Update all packages:

```text
sudo soup
```

Verify services are running:

```text
sudo so-status
```

If any services are not running, try starting them:

```text
sudo so-start
```

Restart the sensor processes:

```text
sudo so-sensor-restart
```

Restart Zeek:

```text
sudo so-zeek-restart
```

Add new Sguil user accounts with the following:

```text
sudo so-user-add
```

Update rules

```text
sudo rule-update
```

Restart Kibana:

```text
sudo so-kibana-restart
```

Monitor Kibana log file for errors:

```text
tail -f /var/log/kibana/kibana.log
```

## Snort Rule Examples:

**Phishing Attack Example Rule - Connection to specific uri:**

```text
alert tcp $HOME_NET any -> $EXTERNAL_NET $HTTP_PORTS (msg:"Probable successful phishing attack."; flow:established,to_server; content:"GET"; http_method; content:"/1dkfJu.php?"; http_uri; classtype:trojan-activity; sid:10000001; rev:1;)
```

**SSH Scan Example**

```text
alert tcp $HOME_NET any -> $HOME_NET 22 (msg:"Potential internal SSH scan detected."; flags:S; threshold: type both, track by_src, count 5, seconds 120; reference:url,ics-cert.us-cert.gov/tips/CSAR-10-114-01; classtype:attempted-recon; sid:99999991; rev:1;)
```

**Social Security Number Detection Example**

```text
alert tcp $HOME_NET any -> any 20 (msg:"Social Security Number detected in plaintext over FTP."; pcre:"/([0-6]\d\d|7[0-256]\d|73[0-3]|77[0-2])-\d{2}-\d{4}/"; reference:url,en.wikipedia.org/wiki/File_Transfer_protocol; classtype:policy-violation; sid:99999992; rev:1;)
```

**HTTP Traffic Infection Example**

```text
alert tcp $HOME_NET any -> $EXTERNAL_NET $HTTP_PORTS (msg:"Probable post-infection - Boleto-themed malicious spam. First indicator."; flow:established,to_server; content:"GET"; http_method; content:"/bibi/"; http_uri; pcre:"/(\.txt|\.tiff|\.zip|\.dll|\.exe)/U"; classtype:trojan-activity; sid:10000002; rev:1;)
```

**Snort DLP - IDS/IPS rules can look for specific content**

```text
alert tcp any any -> any any (pcre:"/4\d{3}(\s|- )?\d{4}(\s|-)?\d{4}(\s|-)?\d{4}/"; msg:"DLP PCI Visa number in clear text"; sid:1053000; rev:1;)
```

**Detecting IPv6 over IPv4 Tunnels - testing for protocol 41:**

```text
alert ip $EXTERNAL_NET any -> $HOME_NET any (msg: "POLICY IPv6 in IPv4"; ip_proto: 41; sid:8446;)
```

**Detecting Teredo**

```text
Check for UDP port 3544 traffic or look for Teredo prefix: 2001::/32
alert udp any any <> any 3544 (content: "|20 01 00 00|"; offset 8; depth 4;)
alert udp any any <> any 3544 (content: "|20 01 00 00|"; offset 16; depth 4;)
```

## Snort Read Multiple PCAPs

Snort Read pcaps from a file \(Note that Snort will not try to determine whether the files under that directory are really pcap files or not.\)

```text
cat foo.txt
foo1.pcap
foo2.pcap
/home/foo/pcaps
snort --pcap-file=foo.txt
```

Snort read pcaps from a command line list:

```text
snort --pcap-list="foo1.pcap foo2.pcap foo3.pcap"
```

Snort read pcaps under a directory

```text
snort --pcap-dir="/home/foo/pcaps"
```

## OpenappID:

Run openappid against the file:

```text
sudo u2openappid /var/log/snort/appstats-unified.log.XXXXXXXXX
```

See the most frequent apps used in the environment:

```text
sudo u2openappid /var/log/snort/appstats-unified.log.XXXXXXXXXX | cut -f2 -d"," | sort | uniq -c | sort -nr | head -n 10
sudo u2openappid /var/log/snort/appstats-unified.log.1398512880 | cut -f2 -d"," | sort | egrep -vi '"http"|"https"|"dns"|"internet explorer"' | uniq -c | sort -nr
```

Now, we will create two Snort rules to attempt to detect the use of mail.ru or yandex. First, open the local.rules file, which is where we will place our new rules.:

```text
sudo leafpad /etc/snort/rules/local.rules
alert tcp any any -> any any (msg: "Mail.ru Detected"; appid: mail.ru; sid: 1000001; rev: 1;)
alert tcp any any -> any any (msg: "Yandex Detected"; appid: yandex; sid: 1000002; rev: 1;)
```

Re-run Snort against directory:

```text
sudo snort -c /etc/snort/snort.conf --pcap-dir=/pcap-links -k none
```

## Snort Rule Testing - Dumbpig

Install and Configure Dumbpig for Rule Testing:

```text
sudo git clone https://github.com/leonward/dumbpig.git
```

Once Downloaded, do the following:

```text
sudo cpan Parse::Snort #Hit Yes, when prompted, to autoconfig as much as possible
cd dumbpig/
sudo perl dumbpig.pl -r /path/to/rules.to.test
```

Example Output from running against:

```text
alert tcp any any -> any any (msg:"Bad rule 1."; content:"google"; nocase; sid:10000006; rev:1;)
```

```text
Issue 1
3 Problem(s) found with rule on line 6 of /etc/nsm/rules/local.rules

alert tcp any any -> any any  ( \
    msg:"Bad rule 1."; \
    content:"google"; \
    nocase; \
    sid:10000006; \
    rev:1; \
)
- No classification specified - Please add a classtype to add correct priority rating
- TCP, without flow. Consider adding flow to provide better state tracking on this TCP based rule
- <ip.addr> ANY -> <ip.addr> ANY rule.
  You should really add port numbers into your rule. You are likely wasting huge chunks of processing effort on the wrong packets

Rule source sid: 10000006
alert tcp any any -> any any (msg:"Bad rule 1."; content:"google"; nocase; sid:10000006; rev:1;)
=============================================================================
--------------------------------------
Total: 1 fails over 6 rules (21 lines) in /etc/nsm/rules/local.rules
- Contact leon.ward@sourcefire.com
```

## tcpreplay

Tcpreplay - Replay pcap 20 times on interface:

```text
tcpreplay -l 20 -i eth0 -t <pcap-to-run>
```

Replay TCP packet out eth0 interface:

```text
tcpreplay -i eth0 sample.pcap
```

Play back at a specific speed, add the --mbps option:

```text
tcpreplay -i eth0 --mbps=510.5 smallFlows.pcap
```

Replay as with zero time between packets:

```text
tcpreplay -i eth0 --topspeed smallFlows.pcap
```

Replay as fast as possible:

```text
tcpreplay -v -e eth0 -t file.pcap
```

Replay @ 1/2 speed:

```text
tcpreplay -p 0.5 -I eth0 file.pcap
```

## TCPFlow

View Scanner Options of tcpflow:

```text
tpflow -H
```

Read packets from a pcap:

```text
tcpflow -f file.pcap
```

TCPFlow Tip to Extract - basic file extraction:

```text
tcpflow -e netviz -r
```

Capture tcpflow details on port 80:

```text
tcpflow port 80
```

Capture on a specific interface:

```text
tcpflow -i eth0 port 80
```

Specify a target host:

```text
tcpflow -c host 192.68.43.1
```

Specify a target host:

```text
tcpflow -c host www.sans.org
```

## Snort Resources

Snort Users Manual: [http://manual-snort-org.s3-website-us-east-1.amazonaws.com/](http://manual-snort-org.s3-website-us-east-1.amazonaws.com/)

Snort Rule Writing Manual: [http://manual-snort-org.s3-website-us-east-1.amazonaws.com/node27.html](http://manual-snort-org.s3-website-us-east-1.amazonaws.com/node27.html)

Infosec Institute Snort Rule Writing Overview: [http://resources.infosecinstitute.com/snort-rules-workshop-part-one/\#gref](http://resources.infosecinstitute.com/snort-rules-workshop-part-one/#gref)

Emerging Threats Snort Rules: [https://rules.emergingthreats.net/open/snort-2.9.0/rules/](https://rules.emergingthreats.net/open/snort-2.9.0/rules/)

Snort Community and Blog Network: [https://snort.org/community](https://snort.org/community)

Security Onion Google Group: [https://groups.google.com/forum/\#!forum/security-onion](https://groups.google.com/forum/#!forum/security-onion)

Joel Elser Overview of Offset, Depth, distance, and within keywords: [https://blog.joelesler.net/2010/03/offset-depth-distance-and-within.html](https://blog.joelesler.net/2010/03/offset-depth-distance-and-within.html)

CiscoLive Intro to Writing Snort Rules: [https://www.ciscolive.com/c/dam/r/ciscolive/emea/docs/2016/pdf/DevNet-1693.pdf](https://www.ciscolive.com/c/dam/r/ciscolive/emea/docs/2016/pdf/DevNet-1693.pdf)

CiscoLive Detection Strategies: [https://www.ciscolive.com/c/dam/r/ciscolive/emea/docs/2016/pdf/DevNet-1126.pdf](https://www.ciscolive.com/c/dam/r/ciscolive/emea/docs/2016/pdf/DevNet-1126.pdf)

Security Onion CheatSheet: [https://github.com/Security-Onion-Solutions/security-onion/wiki/Cheat-Sheet](https://github.com/Security-Onion-Solutions/security-onion/wiki/Cheat-Sheet)

