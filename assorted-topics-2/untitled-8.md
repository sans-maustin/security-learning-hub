# Windows and Linux CLI

## Objectives

This is a list of common and beneficial Linux and Windows Commands

## Combination of Commands

**a\) Obtain many details on the machine:**

Linux

```text
lshw (uname, uname -a, etc.)
cat /etc/issue
cat /etc/* -release
uname -a
```

Windows

```text
systeminfo
systeminfo |findstr /B /C:"OS Name" /C:"OS Version" /C:System Type"
```

**b\) Download File from Website**

Linux

```text
    wget -O filename.zip http://www.domain.com/filename-4.0.1.zip
    curl http://website.org/misc.zip --output myfile.zip
```

Windows

```text
    Invoke-WebRequest -Uri https:// -OutFile
```

**c\) View all processes and display full format listing:**

Linux

```text
    ps -ef
    ps aux
```

Windows

```text
    tasklist
```

**d\) Stop/Kill Process**

Linux

```text
    kill -9
```

Windows

```text
    taskkill /F /IM
```

**e\) List of Started Services:**

Linux

```text
    systemctl list-unit-files
```

Windows

```text
    tasklist /SVC
    wmic service list config
    sc queryex type=service state=all | find /i ""SERVICE_NAME:""
```

**f\) Enumerate Local User:**

Linux/Windows

```text
    whoami
```

**g\) Identify Information on Username:**

Linux

```text
    id
```

Windows

```text
    net user username
```

**h\) Identify Machine Information:**

Linux/Windows

```text
    hostname
```

**i\) IP Address, Routing Table, and Network Stats Details:**

Linux

```text
    ifconfig -a
    ip a
    ss -anp
    netstat -anp
```

Windows

```text
    ipconfig /all
    route print
    netstat -ano
```

**j\) Firewall Settings and Rules:**

Linux

```text
    grep -Hs iptables /etc/*
    cat /etc/iptables-backup
```

Windows

```text
netsh advfirewall show currentprofile
    netsh advfirewall firewall show rule name=all
```

**k\) List installed Applications:**

Linux

```text
    dpkg -l
```

Windows

```text
wmic product get name, version, vendor
```

**l\) Identify Unmounted Drives:**

Linux/Windows

```text
    mount
    cat /etc/fstab"
    Windows: mountvol
```

**m\) Change User Password:**

Linux/Windows

```text
    passwd
    sudo passwd <user account>
    Windows: net user <account> password
```

**n\) Find Files:**

Linux/Windows

```text
    sudo updatedb
    locate sbd.exe
    sudo find / -name sbd*    "
    Windows: dir /b /s c:\ | find /i ""wmic.exe""
    Windows: dir /b /s c:\wmic.exe
    Windows: findstr /s /i Windows *.*
```

**n\) Locate OS Patch Level:**

Windows

```text
    wmic qfe get Caption, Description, HotFixID, Installation
```

**o\) Software Inventory**

Windows

```text
    dir /s ""c:\Program Files"" > inventory.txt
    dir /s ""c:\Program Files (x86)"" >> inventory.txt
```

**p\) Misc**

List all records, with longest record first, excluding the list of names specified:

```text
egrep -v "googleusercontent|ip6|rackcdn|sophos" loganalysis/query.log |sort -u|awk '{print length, $0}'|sort -rn|more
```

Find and copy files to a directory:

```text
find / -iname '*.pcap' -exec mv '{}' /backup/ \;
```

## Linux Service Basics

**Create directory and multiple subdirectories:**

```text
mkdir -p test/{one,two,three}
```

**Create directory with spaces:**

```text
mkdir "section one"
```

**Locate update and find file**

```text
updatedb
locate malware.pcap
```

**Basic IP address details**

```text
ip addr
ifconfig -a
```

**Kali - Start/Stop/Status Apache:**

```text
service apache2 start
sudo systemctl start apache2
sudo systemctl stop apache2
sudo systemctl status apache2
```

Kali - Enable Apache at Boot time:

```text
sudo systemctl enable apache2
```

See a table of all available services:

```text
systemctl list-unit-files
```

Serve up contents of a specific folder:

```text
python -m SimpleHTTPServer 9000
```

Find out the http status code of a URL

```text
curl -s -o /dev/null -w "%{http_code}" https://www.google.com
```

Unshorten a shortended URL

```text
curl -s -o /dev/null -w "%{redirect_url}" https://bit.ly/34EFwWC
```

Recursively remove all "section 1" folders

```text
find . -name "section 1" -exec rm -rf '{}' +
```

Get all files modified in last 30 days in a directory:

```text
find . -type f -mtime -30 -exec ls -l {} \; > last30days.txt
```

List installed packages

```text
apt list --installed
```

List installed packages on Red Hat:

```text
yum list installed
```

## Change Linux Mac Address

```text
ip link set eth0 address 00:00:00:00:00:01

or

ifconfig eth0 down
ifconfig eth0 hw ether 00:00:00:00:00:01
ifconfig eth0 up
```

## Misc Curl Examples

Misc Curl Browser Testing in changing user agents for testing rules:

```text
curl -A "RFRudokop" http://www.google.com
curl -A "User-Agent: AutoHotKey" http://www.google.com
curl -A "User-Agent: 3a" http://www.yahoo.com
curl -A "User-Agent: WHCC/Hmi" http://www.yahoo.com
curl -A "EmailSiphon" http://www.netsec.com
curl -A "Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:59.0) Gecko/20100101 Firefox/59.0" https://www.cyberciti.biz/
curl -I -A "Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:59.0) Gecko/20100101 Firefox/59.0" https://www.cyberciti.biz/faq/
```

