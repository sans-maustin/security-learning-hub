# PCAP Analysis Learning

## Objectives

Some general things to look for and log when looking at PCAPS - not exhaustive

## General things to look for:

Document the date, start time, end time of the pcap in UTC? Document the IP addresses of 3 hosts in the pcap. Document the MAC addresses of 3-4 hosts in the pcap. Document the type of computer \(Windows, MAC, Android, etc.\) for each of the hosts.

Which websites are being connected to? How many SYN packets? Any packets with data on the syn? How many rst packets? Any ICMP packets?

WHat unique ports are being used? What TLS ciphers are used?

Any interesting DNS traffic? What kind of dns queries?

Who was the top talker? How many bytes were transferred?

Look at [https://www.malware-traffic-analysis.net/](https://www.malware-traffic-analysis.net/) for more ideas

