# Linux CLI I

## Overview

Linux Networking, cron jobs, kernel hardening \(sysctl.conf\), lsof:

## Network Interface Settings:

```text
ip neigh
arp -a
ip -s neigh
arp -v
ip neigh add 192.168.1.1 lladdr 1:2:3:4:5:6 dev eth1
arp -s 192.168.1.1 1:2:3:4:5:6
ip neigh del 192.168.1.1 dev eth1
arp -i eth1 -d 192.168.1.1
ip addr
ifconfig -a
ip link set eth0 down
ifconfig eth0 down
ip link set eth0 up
ifconfig eth0 up
ip addr add 192.168.1.1/24 dev eth0
ifconfig eth0 192.168.1.1
ip addr add 192.168.1.1/24 dev eth0
ifconfig eth0 netmask 255.255.255.0
ip link set eth0 mtu 9000
ifconfig eth0 mtu 9000
ip addr add 192.168.1.2/24 dev eth0
ifconfig eth0:0 192.168.1.2
ip route add 192.168.1.0/24 dev eth0
route add -net 192.168.1.0 netmask 255.255.255.0 dev eth0
ip route add default via 192.168.1.1
route add default gw 192.168.1.1
```

Set interface to up status and manually setting IP address and set default route:

```text
ip link set dev eth0 up
ip link show eth0
ip addr add dev eth0 10.0.0.1/24
ip addr list eth0
ip route add default via 192.168.0.1
```

## Debian/Ubuntu Systems Network Settings

On older Debian/Ubuntu systems check /etc/network/interfaces file or the /etc/network/interfaces.d for a group of the configurations

```text
Here is an example of a simple DHCP client configuration for an ethernet device:

bash
auto eth0
iface eth0 inet dhcp

Here’s an example for a simple static host for an ethernet device:

bash
auto eth0
iface eth0 inet static
address 10.9.8.7
netmask 255.255.255.0
gateway 10.9.8.1
dns-search mydomain.com
dns-nameservers 8.8.8.8 8.8.4.4

To restart your network interface from the command line.
sudo ifdown eth0 && sudo ifup eth0

Add Route:
ip route add 192.168.1.0/24 via 10.9.8.31 dev eth0
```

## Redhat/CentOS Systems Network Settings

On Redhat and CentOS systems, the network is configured by scripts in a directory called / sysconfig/network-scripts. Ordinarily, each interface has its own configuration file along the lines of ifcfg-ethX.cfg which is very similar in many respects to the /etc/network/interfaces.d files on an Ubuntu or Debian system.

```text
Here is an example of a DHCP configured client:
bash
BOOTPROTO=dhcp
DEVICE=eth0
HWADDR=0a:67:42:8d:24:9e
ONBOOT=yes
TYPE=Ethernet
USERCTL=no
Here is an example of a static client:
bash
BOOTPROTO=none
DEVICE=eht0
HWADDR=0a:67:42:8d:24:9e
ONBOOT=yes
TYPE=Ethernet
IPADDR=10.9.8.7
PREFIX=24
GATEWAY=10.9.8.1
DNS1=10.9.8.53
DNS2=8.8.8.8
DNS3=8.8.4.4
The same warnings apply here as in the Debian/Ubuntu section; go back and read them if you are skipping around.
Restart the network with this:
systemctl restart network

Add Route:
ip route add 192.168.1.0/24 via 10.9.8.31 dev eth0
```

## Crontab

How to View and Edit Crontab Entries?

```text
crontab -l
crontab -e
```

Crontab Examples:

```text
00 11,16 * * * /home/ramesh/bin/incremental-backup#Run command at 11 AM and 4 PM each day
01 * * * * root echo "This command is run at one min past every hour"
17 8 * * * root echo "This command is run daily at 8:17 am"
17 20 * * * root echo "This command is run daily at 8:17 pm"
00 4 * * 0 root echo "This command is run at 4 am every Sunday"
* 4 * * Sun root echo "So is this"
42 4 1 * * root echo "This command is run 4:42 am every 1st of the month"
01 * 19 07 * root echo "This command is run hourly on the 19th of July"
*/10 * * * * /home/ramesh/check-disk-space #Schedule a Background Cron Job For Every 10 Minutes:
*/15 9-17 * * * root connection.test #Will run connection.test every 15 mins between the hours or 9am and 5pm
```

## Kernel Hardening:

```text
/etc/sysctl.conf
```

Avoid a smurf attack

```text
net.ipv4.icmp_echo_ignore_broadcasts = 1
```

Turn on protection for bad icmp error messages

```text
net.ipv4.icmp_ignore_bogus_error_responses = 1
```

Turn on syncookies for SYN flood attack protection

```text
net.ipv4.tcp_syncookies = 1
```

Turn on and log spoofed, source routed, and redirect packets

```text
net.ipv4.conf.all.log_martians = 1
net.ipv4.conf.default.log_martians = 1
```

No source routed packets here

```text
net.ipv4.conf.all.accept_source_route = 0
net.ipv4.conf.default.accept_source_route = 0
```

Turn on reverse path filtering

```text
net.ipv4.conf.all.rp_filter = 1
net.ipv4.conf.default.rp_filter = 1
```

Make sure no one can alter the routing tables

```text
net.ipv4.conf.all.accept_redirects = 0
net.ipv4.conf.default.accept_redirects = 0
net.ipv4.conf.all.secure_redirects = 0
net.ipv4.conf.default.secure_redirects = 0
```

Don't act as a router

```text
net.ipv4.ip_forward = 0
net.ipv4.conf.all.send_redirects = 0
net.ipv4.conf.default.send_redirects = 0
```

Turn on execshield for reducing worm or other automated remote attacks

```text
kernel.exec-shield = 1
kernel.randomize_va_space = 1
```

Tune IPv6

```text
net.ipv6.conf.default.router_solicitations = 0
net.ipv6.conf.default.accept_ra_rtr_pref = 0
net.ipv6.conf.default.accept_ra_pinfo = 0
net.ipv6.conf.default.accept_ra_defrtr = 0
net.ipv6.conf.default.autoconf = 0
net.ipv6.conf.default.dad_transmits = 0
net.ipv6.conf.default.max_addresses = 1
```

Increase system file descriptor limit

```text
fs.file-max = 65535
```

Allow for more PIDs \(Prevention of fork\(\) failure error message\)

```text
kernel.pid_max = 65536
```

Increase system IP port limits

```text
net.ipv4.ip_local_port_range = 2000 65000
```

Tuning Linux network stack to increase TCP buffer size. Set the max OS send buffer size \(wmem\) and receive buffer size \(rmem\) to 12 MB for queues on all protocols.

```text
net.core.rmem_max = 8388608
net.core.wmem_max = 8388608
```

set minimum size, initial size and max size

```text
net.ipv4.tcp_rmem = 10240 87380 12582912
net.ipv4.tcp_wmem = 10240 87380 12582912
```

Value to set for queue on the INPUT side when incoming packets are faster then the kernel process on them.

```text
net.core.netdev_max_backlog = 5000
```

For increasing transfer window, enable window scaling

```text
net.ipv4.tcp_window_scaling = 1
```

Turning on native Kernel IPv4 protection ¶ The Linux kernel provides some basic protections against manipulated IP packets. A configuration could be:

```text
echo 1 > /proc/sys/net/ipv4/tcp_syncookies # enable syn cookies (prevent against the common 'syn flood attack')
echo 0 > /proc/sys/net/ipv4/ip_forward # disable Packet forwarning between interfaces
echo 1 > /proc/sys/net/ipv4/icmp_echo_ignore_broadcasts # ignore all ICMP ECHO and TIMESTAMP requests sent to it via broadcast/multicast
echo 1 > /proc/sys/net/ipv4/conf/all/log_martians # log packets with impossible addresses to kernel log
echo 1 > /proc/sys/net/ipv4/icmp_ignore_bogus_error_responses # disable logging of bogus responses to broadcast frames
echo 1 > /proc/sys/net/ipv4/conf/all/rp_filter # do source validation by reversed path (Recommended option for single homed hosts)
echo 0 > /proc/sys/net/ipv4/conf/all/send_redirects # don't send redirects
echo 0 > /proc/sys/net/ipv4/conf/all/accept_source_route # don't accept packets with SRR option
echo 1 > /proc/sys/net/ipv6/conf/<interface>/forwarding # Do not accept ipv6 router advertisements
echo 1 > /proc/sys/net/ipv6/conf/<interface>/accept_ra # Do not accept ipv6 router advertisements
```

## Lsof Usage

Show all connections:

```text
lsof -i
```

Get only IPv6 traffic with -i 6:

```text
lsof -i 6
```

Show only TCP connections \(works the same for UDP\):

```text
lsof -iTCP
```

Search by active ports:

```text
lsof -ni :22
```

This is quite useful when you’re looking into whether you have open connections with a given host on the network or on the internet.

```text
lsof -i@172.16.12.5
```

Find ports that are awaiting connections.\(to show ESTABLISHED connections, replace listen with ESTABLISHED\)

```text
lsof -i -sTCP:LISTEN
lsof -i | grep -i LISTEN
```

Show what a given user has open using -u:

```text
lsof -u daniel
```

Show what all users are doing except a certain user using -u ^user:

```text
lsof -u ^daniel
```

Kill everything a given user is doing:

```text
kill -9 lsof -t -u daniel
```

See what files and network connections a named command is using with -c

```text
lsof -c syslog-ng
```

See what a given process ID has open using -p

```text
lsof -p 10075
```

The -t option returns just a PID

```text
lsof -t -c Mail
```

Show everything interacting with a given directory

```text
lsof /var/log/messages/
```

Show me everything daniel is doing connected to 10.0.0.10

```text
lsof -u daniel -i @10.0.0.10
```

