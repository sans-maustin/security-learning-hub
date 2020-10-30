# Firewall

## Objective

Information on local firewalls of all types

## Firewalld

Basic Commands

```text
sudo firewall-cmd --permanent --add-port=61613/tcp
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=22/tcp --permanent
sudo firewall-cmd --permanent --remove-port=22/tcp
sudo firewall-cmd --reload
sudo semanage port -a -t ssh_port_t -p tcp 61613
```

Firewalld Blacklist:

```text
sudo firewall-cmd --permanent --new-ipset=blacklist --type=hash:net --option=family=inet --option=hashsize=4096 --option=maxelem=200000
```

Create list of IPs and add them to the list:

```text
vim /home/cloud_user/MyIpList
```

Add the IP addresses to the blacklist:

```text
sudo firewall-cmd --permanent --ipset=blacklist --add-entries-from-file=/home/cloud_user/MyIpList
```

Add blacklist to a drop zone:

```text
sudo firewall-cmd --permanent --zone=drop --add-source=ipset:blacklist
```

Reload Firewalld:

```text
sudo firewall-cmd --reload
```

## IP6Tables

Basic Commands

```text
ip6tables -6 -A INPUT -i lo -j ACCEPT
ip6tables -6 -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
ip6tables -6 -A INPUT -p tcp --dport 22 -j ACCEPT
ip6tables -6 -P INPUT DROP
ip6tables -6 -P FORWARD DROP
ip6tables -6 -P OUTPUT ACCEPT
ip6tables -6 -A FORWARD -i eth0 -j ACCEPT
ip6tables -6 -A FORWARD -i eth1 -o eth0 -m state --state RELATED,ESTABLISHED -j ACCEPT
ip6tables -6 -A FORWARD -i eth0 -j ACCEPT
```

## Iptables Basics

Look at rules:

```text
iptables -L -v
```

Clear out the firewall ruleset:

```text
iptables -F
```

Look at default policy:

```text
iptables -L |grep policy
```

Exporting: Save the rules:

```text
sudo /sbin/iptables-save > myfile
```

Restore Rules:

```text
iptables-restore < /etc/iptables/empty.rules
```

Save to a remote server \(Server Config\):

```text
sudo /sbin/iptables-save|nc 10.0.0.0 9090
```

Save to a remote server \(Client Config\):

```text
nc -l -p 9090 > iptables-save
```

Iptables Rules

```text
Don't interfere with loopback traffic:
iptables -A INPUT -i lo -j ACCEPT
iptables -A OUTPUT -o lo -j ACCEPT
```

Write rule to match multiple ports:

```text
iptables -A INPUT -p tcp -m multiport --destination-port 22,53,80,110
```

Inbound Rules: Allow SSH \(22/tcp\) and packets from \# sessions that we have initiated from this system

```text
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
iptables -A INPUT -p tcp -m state --state ESTABLISHED -j ACCEPT
iptables -A INPUT -p udp -m state --state ESTABLISHED -j ACCEPT
iptables -A INPUT -p icmp -m state --state ESTABLISHED -j ACCEPT
```

Stateful traffic allowed:

```text
iptables -A INPUT -p tcp --dport 22 -s 10.10.10.10 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -p tcp --sport 22 -d 10.10.10.10 -m state --state ESTABLISHED -j ACCEPT
```

Outbound Rules: Any TCP, UDP, ICMP is OK

```text
iptables -A OUTPUT -p tcp -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -p udp -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -p icmp -m state --state NEW,ESTABLISHED -j ACCEPT
```

Setup Default Drop Policy and Block Traffic:

```text
iptables -A INPUT -p tcp --dport 22 -s 10.10.10.10 -j DROP
iptables --policy INPUT DROP
iptables --policy OUTPUT DROP
iptables --policy FORWARD DROP
```

## Iptables Logging Traffic

Listing: Log Traffic:

```text
iptables -t filter -A INPUT -p icmp --icmp-type echo-request -j LOG --log-prefix="filter INPUT:"
iptables -t filter -A INPUT -p icmp --icmp-type echo-reply -j LOG --log-prefix="filter INPUT:"
iptables -t filter -A OUTPUT -p icmp --icmp-type echo-request -j LOG --log-prefix="filter OUTPUT:"
iptables -t filter -A OUTPUT -p icmp --icmp-type echo-reply -j LOG --log-prefix="filter OUTPUT:"
```

Active FTP

```text
iptables -A INPUT -p tcp -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -A INPUT -p udp -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -A INPUT -p icmp -m state --state ESTABLISHED,RELATED -j ACCEPT
```

On Linux, list all iptables firewall rules in all chains:

```text
iptables -t nat -nL
iptables -t mangle -nL
iptables -t filter -nL
iptables -t raw -nL
```

## Sample IP Tables configuration script

nano iptables4.ruleset.sh

```text
#!/bin/sh

IPTABLES=/sbin/iptables

#Flush previous rules
$IPTABLES -F

#Set "default deny" policy
$IPTABLES -P INPUT DROP
$IPTABLES -P OUTPUT DROP
$IPTABLES -P FORWARD DROP

#All other network 127 traffic should be dropped
$IPTABLES -A INPUT -i lo -j ACCEPT
$IPTABLES -A OUTPUT -o lo -j ACCEPT
$IPTABLES -A INPUT -s 127.0.0.0/8 -j DROP

#Allow inbound SSH connections
$IPTABLES -A INPUT -p tcp --dport 22 -m state --state NEW -j ACCEPT

#Allow other inbound traffic that's part of connections we've started
$IPTABLES -A INPUT -p tcp -m state --state ESTABLISHED -j ACCEPT
$IPTABLES -A INPUT -p udp -m state --state ESTABLISHED -j ACCEPT
$IPTABLES -A INPUT -p icmp -m state --state ESTABLISHED -j ACCEPT

#Allow all outbound traffic
$IPTABLES -A OUTPUT -p tcp -m state --state NEW,ESTABLISHED -j ACCEPT
$IPTABLES -A OUTPUT -p udp -m state --state NEW,ESTABLISHED -j ACCEPT
$IPTABLES -A OUTPUT -p icmp -m state --state NEW,ESTABLISHED -j ACCEPT

#Log any other traffic before it gets whacked by default policy
$IPTABLES -A INPUT -j LOG
$IPTABLES -A OUTPUT -j LOG
$IPTABLES -A FORWARD -j LOG
```

## UFW Firewall Examples:

Simple port examples and status check:

```text
sudo ufw allow ssh
sudo ufw allow http
sudo ufw allow https
sudo ufw status
```

Specify Networks and multiple ports:

```text
sudo ufw allow from 10.0.0.0/24 to any port 22
sudo ufw allow proto tcp from any to any port 80,44
```

Specify an Interface:

```text
sudo ufw allow in on eth1 to any port 3306
```

Deny Traffic

```text
sudo ufw deny out 25
```

Check Logging Level:

```text
sudo ufw status verbose
```

Turn UFW Logging On:

```text
sudo ufw logging on
```

To set the defaults used by UFW, use these commands:

```text
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

Allow SSH and Port 2222:

```text
sudo ufw allow ssh
sudo ufw allow 2222
```

Allow X11 connections, which use ports 6000-6007, use these commands:

```text
sudo ufw allow 6000:6007/tcp
sudo ufw allow 6000:6007/udp
```

Allow specific host to SSH in: sudo ufw allow from 15.15.15.51 to any port 22 Allow in on specific interface: sudo ufw allow in on eth1 to any port 3306 Configure Firewall in settings: sudo nano /etc/default/ufw

Comment on a rule:

```text
sudo ufw allow proto tcp from any to any port 80,443 comment 'my web app ports'
sudo ufw allow proto tcp from any to 10.8.0.1 port 22 'SSHD port 22 for private lan’
```

Reject Traffic:

```text
sudo ufw reject in smtp
sudo ufw reject out smtp
sudo sudo ufw reject 1194 comment 'No more vpn traffic'
sudo ufw reject 23 comment 'Unencrypted port not allowed’
```

When you edit UFW' configuration file, you need to run reload command. For example, you can edit /etc/ufw/before.rules, enter:

```text
sudo nano /etc/ufw/before.rules
sudo vi /etc/ufw/before.rules
```

By default all UFW entries are logged into /var/log/ufw.log file:

```text
sudo more /var/log/ufw.log
sudo tail -f /var/log/ufw.log
```

The raw report shows the complete firewall, while the others show a subset of what is in the raw report:

```text
sudo ufw show raw
sudo ufw show raw | more
```

Check Status and then delete a rule:

```text
sudo ufw status numbered
sudo ufw delete 2
```

