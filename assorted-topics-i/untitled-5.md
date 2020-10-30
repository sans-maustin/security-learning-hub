# Nmap and Masscan

## Masscan and Nmap Scanning Techniques

### Objectives

Various examples of scanning with masscan and nmap.

## More Nmap Examples

#### Objectives

More examples of scanning with nmap.

### Nmap Targeting

| Task | Command |
| :--- | :--- |
| Scan a single IP | nmap 10.168.1.1 |
| Scan a hostname | nmap www.domain.com |
| Scan an IP range | nmap 10.168.1.1-100 |
| Scan a subnet | nmap 10.168.1.0/24 |
| Scan from a predefined list | nmap -iL list.txt |

### Nmap Port Specification

| Task | Command |
| :--- | :--- |
| Scan a single port | nmap 10.168.1.1 -p 22 |
| Scan a range of ports | nmap 10.0.0.1 -p 1-20 |
| Scan multiple ports | nmap -p 22,80,443 10.168.1.1 |
| Scan Mixed TCP/UDP ports | nmap -p U:53,T:22 10.168.1.1 |
| Scan 100 common ports | nmap -F 10.168.1.1 |
| Scan top \# ports | nmap -top-ports 300 10.168.1.1 |
| Scan all ports | nmap -p- 10.168.1.1 |
| TCP Connect Scan | nmap -sT 10.168.1.1 |
| TCP SYN scan \(Silent scan\) | nmap -sS 10.168.1.1 |
| UDP scan | nmap -sU -p 137,139 10.168.1.1 |
| No ping scan | nmap -Pn 10.168.1.1 |
| Host Discovery \(no ports\) | nmap -sn 10.168.1.1 |
| IPv6 scan with OS, Version, script scanning on ports 53, 80, 443 | nmap -6 -p53,80,443 fe80::20c:29ff:fe03:6865 -A |

### OS and Service Discovery

| Task | Command |
| :--- | :--- |
| Version Scan | nmap -sV 10.168.1.1 |
| OS Detection | nmap -o 10.168.1.1 |
| OS and Services | nmap -A 10.168.1.1 |
| Standard service discovery | nmap -sV 10.168.1.1 |
| Aggressive service discovery | nmap -sV -version-intensity 5 10.168.1.1 |
| Light banner grabbing | nmap -sV -version-intensity 0 10.168.1.1 |

### Timing Scans

| Task | Command |
| :--- | :--- |
| Paranoid -  Very slow | nmap -t0 10.168.1.1 |
| Sneaky - Slow | nmap -t1 10.168.1.1 |
| Polite: Slows down | nmap -t2 10.168.1.1 |
| Normal: Default | nmap -t3 10.168.1.1 |
| Aggressive: Fast and reliable | nmap -t4 10.168.1.1 |
| Insane: Very aggressive | nmap -t5 10.168.1.1 |
| Parallel host scan group sizes&lt; | --min-hostgroup/max-hostgroup |
| Probe parallelization | --min-parallelism/max-parallelism |
| Specifies probe round trip time | --min-rtt-timeout/max-rtt-timeout/initial-rtt-timeout |
| Caps number of port scan probe retransmissions | --max-retries |
| Give up on target after time | --host-timeout |

### Output Formats

| Task | Command |
| :--- | :--- |
| Standard Nmap output | nmap -oN output.txt 10.168.1.1 |
| XML format | nmap -oX output.txt 10.168.1.1 |
| Greppable format | map -oG output.txt 10.168.1.1 |
| All formats output | nmap -oA output.txt 10.168.1.1 |

### NSE Scripts

| Task | Command |
| :--- | :--- |
| Default scripts | nmap -sV -sC 10.168.1.1 |
| Script help | nmap --script-help=ssl-heartbleed |
| NSE script scan | nmap -sV --script=ssl-heartbleed -p 443 10.168.1.1 |
| Scan with scripts sets | nmap -sV --script=smb\* 10.168.1.1 |
| Run all the scripts except for the ones in the exploit category | nmap -sV --script "not exploit" |
| Default and Safe Categories | nmap --script "default and safe" 192.168.1.1 |
| All HTTP Scripts | nmap --script "http-\*" |
| Brute Force Cassandra | nmap --script cassandra-brute -p 9160  |
| Brute Force Ldap | nmap --script ldap-brute -p 389  |
| Update Nmap Scripts | nmap --script-updatedb |

### HTTP Service Discovery

| Task | Command |
| :--- | :--- |
| Get page title | nmap --script=http-title 10.168.1.0/24 |
| Get HTTP header | nmap --script=http-headers 10.168.1.0/24 |
| Find web apps | nmap --script=http-enum 10.168.1.0/24 |

### SMB OS discovery

| Task | Command |
| :--- | :--- |
| SMB Discovery | nmap -p 139,445 –script smb-os-discovery  |
| SMB signing check | nmap -p137,139,445 –script smb-security-mode  |
| IIS web server name disclosure | nmap -p 80 –script http-iis-short-name-brute   |
| MS08-067 \(netapi\) vulnerability check | nmap -p 445 –script smb-vuln-ms08-067  |
| Checking all smb vulnerability scripts | nmap -p 445 –script smb-vuln-\* 10.168.1.12 |
| Netbios and MAC address lookup | nmap -sU -p137 –script nbstat 10.168.1.12 |
| Enumerating user accounts | nmap -p 139,445 –script smb-enum-users 10.168.1.12 |
| Enumerating window shares | nmap -p 139,445 –script smb-enum-shares –script-args smbusername=vagrant,smbpassword=vagrant 10.168.1.12 |
| SMB loging brute force | nmap -p 445 –script smb-brute –script-args userdb=unamelist.txt,passdb=testlist.txt 10.168.1.12 |

### Other Scans

| Task | Command |
| :--- | :--- |
| Finding domain controllers | nmap -p 389 -sV  |
| Detecting shadow brokers double pulsar smb | nmap -p 445 –script smb-double-pulsar-backdoor |
| MySQL listing databases | nmap -p 3306 –script mysql-databases –script-args mysqluser=root 10.168.1.12 |
| MySQL empty password | nmap -p3306 –script mysql-empty-password,mysql-databases 10.168.1.12 |
| MySQL user listing | nmap -p3306 –script mysql-empty-password,mysql-users 10.168.1.12 |
| Very Fast Enable OS detection, version detection, script scanning, and traceroute and verbose | nmap -T4 -A -v 192.168.1.\* |
| Stealthy SYN Scan with Version Detection and OS Detection | nmap -sSV -O 10.10.10.12 -oN Enumeration.txt |
| Xmas Scan | nmap -sX -T4 10.10.10.12 |
| Nmap Top 12 Ports and state reason | nmap 192.168.1.1 --top-ports 12 --reason |
| Brute Force SNMP | nmap -sU -p 161 --script=snmp-brute 10.10.10.12 |
| Nmap Idle Scan | nmap -Pn -p 80 -sI 10.10.10.16 10.10.10.12 |
| Nmap DNS Brute | nmap --script dns-brute |
| XSS Testing | nmap -p80 --script http-unsafe-output-escaping |
| XSS Testing | nmap -p80 --script http-xssed 192.168.1.222 |
| SQLI Testing | nmap -p80 --script http-sql-injection 192.168.1.222 |

### Masscan Port Scan Examples

| Task | Command |
| :--- | :--- |
| Masscan Port 443 Scan | masscan 10.11.0.0/16 -p443 |
| Masscan Port 80 and 443 Scan | masscan 10.11.0.0/16 -p80,443 |
| Masscan Ports 22 through 25 Scan | masscan 10.11.0.0/16 -p22-25 |
| Masscan scan nmap's top 100 ports | masscan 10.11.0.0/16 ‐‐top-ports 100 |
| Masscan Scan and Exclude hosts | masscan 10.11.0.0/16 ‐‐top-ports 100 ‐‐excludefile exclude.txt |
| Masscan scan Nmaps Top 100 Ports and export to XML | masscan 10.11.0.0/16 ‐‐top-ports 100 -oX xml.file |
| Masscan scan Nmaps Top 100 Ports and export to Grep | masscan 10.11.0.0/16 ‐‐top-ports 100 -oG grep.file |
| Masscan scan Nmaps Top 100 Ports and export to JSON | masscan 10.11.0.0/16 ‐‐top-ports 100 -oJ json.file |
| Masscan the Internet for all ports | masscan 0.0.0.0/0 -p0-65535 ––rate 10000000 |

