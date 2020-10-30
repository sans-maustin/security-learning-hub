# Windows CLI

## More Windows Command Line

### Objective

I did not want to keep out other notes I had on Windows CLI. There is obviously so much more which could be included, but this should be a great start for folks. There is some overlap here with other content in the Wiki and this is in no way exhaustive...except to type out! ;-\)

### Windows Non-Organized CLI Stuff

Remote System Shutdown

```text
shutdown -r -m \[computer_name]
```

Software Inventory:

```text
dir /s "c:\Program Files" > inventory.txt
dir /s "c:\Program Files (x86)" >> inventory.txt
```

Search files for password

```text
findstr /si password .txt|.xml|*.xls
```

System Information

```text
systeminfo
```

Remote Process Listing

```text
tasklist /S /v
```

Create Remote Service

```text
sc \ create
```

Copy remote folder

```text
xcopy /s \\dir c:\local
```

Kill calc.exe process:

```text
taskkill /fi "imagename eq calc.exe"
```

How many files are in c:\windows\system32?

```text
dir /b c:\windows\system32 | find /c /v ""
```

See history of commands ran:

```text
doskey /history
```

Wanted to display Hidden Directories, you’d run:

```text
dir /aHD
```

Case insensitive search for a file with a given name:

```text
dir /b /s c:\ | find /i "wmic.exe"
dir /b /s c:\wmic.exe
```

find the word “Admin” in any files inside of c:\windows\system32, you’d run:

```text
findstr /d:c:\windows\system32 "Admin" *
```

Identify \# of notepad applications that are open:

```text
tasklist | find /i /c "notepad.exe"
```

Count Half-Open Connections in Windows:

```text
netstat ­na | find /i "listening"
```

Search for all PDFs:

```text
dir /a /s /b c:*.pdf*
```

To get the smallest and then largest files in the root of the C: drive:

```text
dir c:\ | sort-object length | select ‑first 1
dir c:\ | sort-object length -descending | select ‑first 1
```

To list the EXE's in the C:\Windows folder, sorted on the Last Access Time property:

```text
dir c:\windows*.exe
```

Password guessing for SMB with enum:

```text
enum –D –u [user] –f [file] [targetIP
```

To set the system-wide DEP status to OptOut on Vista/2008 and later:

```text
bcdedit.exe /set nx optout
```

For the list of DLLs loaded by a specific process, we can execute the following:

```text
tasklist /m /fi "pid eq [pid]"
```

Install EMET silently:

```text
msiexec.exe /i c:\sans\tools\emet\emet-setup.msi /qn /norestart
```

View your current EMET settings inside PowerShell:

```text
.\EMET_Conf.exe --list
```

As an example, to compare the settings defined in a security template against the local computer, saving the output to a text log file, while generating a temporary database file:

```text
secedit.exe /analyze /db temp.sdb /cfg SecurityTemplate.inf /log log.txt
```

For the command-line invocation of a process, run the following:

```text
wmic process where processid=[pid] get commandline
```

CLI Tasklist in Verbose Mode:

```text
tasklist /v
```

Tasklist to find services with task:

```text
tasklist /svc
```

Service Query from CLI:

```text
sc query |more
```

Look for files larger than 10MB:

```text
FOR /R C:\ %i in (*) do @if %~zi gtr 10000000 echo %i %~zi
```

Look at HKLM Run Options:

```text
reg query hklm\software\microsoft\windows\currentversion\run
```

Delete Scheduled Task:

```text
schtasks /delete /tn Test
```

Unusual Accounts:

```text
net localgroup administrators
lusrmgr.msc
```

## Windows CLI Network Settings

Set int to use DHCP

```text
netsh interface ip set address local dhcp
```

Set DNS server

```text
netsh interface ip set dns local static
```

Disable Windows Firewall

```text
netsh firewall set opmode disable
```

Disable Windows Firewall

```text
netsh advfirewall set currentprofile state off
```

Add user:

```text
net user <userName> Password1 /add
```

Add to group:

```text
net localgroup administrators <userName> /add
```

List users:

```text
net localgroup administrators
```

List shares:

```text
net share
```

Share folder:

```text
net share shareName=c:\path\to\share /GRANT:<userName>,FULL
```

Unshared:

```text
net share shareName /delete
```

Connect:

```text
net use X: \\<IPaddress>\share [password] /USER:[domain]\<userName>
```

Disconnect:

```text
net use X: /delete
```

Null session:

```text
net use \\<IPaddress>\IPC$ “” /u:””
```

Anonymous Session

```text
net use \192.168.1.11\IPC$ "" /u:""
```

List Local Groups

```text
net user List all local groups
```

List Local Groups

```text
net localgroup
```

View User Details

```text
net user Student
```

View details about a specific group:

```text
net localgroup users
```

To dump the details of every connection security rule \(IPSec\):

```text
netsh.exe advfirewall consec show rule name = all
```

To see how to create a connection security rule \(IPSec\):

```text
netsh.exe advfirewall consec add rule /?
```

To dump the details of every firewall rule:

```text
netsh.exe advfirewall firewall show rule name = all
```

To see how to create a firewall rule from the command line:

```text
netsh.exe advfirewall firewall set rule /?
```

On a Windows box, show all ports allowed through the built-in firewall:

```text
netsh firewall show portopening
```

Show all programs allowed to communicate through the built-in firewall:

```text
netsh firewall show allowedprogram
```

Show all configuration options of built-in firewall:

```text
netsh firewall show config
```

To disable the built-in Windows firewall, you can run:

```text
netsh firewall set opmode disable
```

To disable the built-in Windows firewall, you can run:

```text
netsh advfirewall set allprofiles state off (For Windows 8+)
```

Look for just listening ports, you can type

```text
netstat –na | find “LISTENING”
```

Find half-open and established connections on Windows:

```text
netstat -an|find "445" | find /i /c "syn_received"
```

Find half-open and established connections on Windows:

```text
netstat -an|find "445" | find /i /c "established”
```

We’ll put fred into the local administrators group using this command:

```text
net localgroup administrators fred /add
```

### Windows WMIC

List all patches

```text
wmic qfe
```

Terminate process

```text
wmic process where name="process" call terminate
```

List all Startup Items

```text
wmic startup list full
```

Bootup time

```text
wmic os get lastbootuptime
```

List processes every second

```text
wmic process list brief /every:1
```

Service Information

```text
wmic service list config
```

Network Details

```text
wmic nicconfig get description,Ipaddress,macaddress
```

Get system names and security acct. id

```text
wmic useraccount get name,sid
```

Flush DNS settings

```text
wmic nicconfig call flushdns
```

Change TTL via Command Line

```text
wmic nicconfig call setdefaultttl 192
```

To see account info, including SIDs, type:

```text
wmic useraccount list brief
```

List all local users:

```text
wmic useraccount get name
```

List all local groups:

```text
wmic group get name
```

View details about a specific user:

```text
wmic useraccount get | Select-String Student
```

View details about a specific group:

```text
wmic group get | Select-String Guests
```

Finally, to get more details associated with each running process, use the wmic command:

```text
wmic process list full
```

If you want, take a moment and run this command on your computer to get familiar with its output:

```text
wmic computersystem get username
```

Get parent process id and path or process:

```text
wmic process get processid,parentprocessid,executablepath|find "2936"
```

We can get some interesting information from the wmic command, pulling the computer system context from a variety of machines listed one per line in a systems.txt file:

```text
wmic /node:@systems.txt computersystem get username /format:csv
```

Delete process:

```text
wmic process [pid] delete
```

Details on running processes with WMIC:

```text
wmic process list full
```

Get all info on a running process:

```text
wmic process list full
```

Wmic Kill Processes with specific name:

```text
wmic process where name="notepad.exe" delete
```

Monitor for the start of a program:

```text
wmic process where name="nc.exe" list brief /every:1
```

### More Powershell

Remote into your own computer:

```text
enter-pssession -computername $env:computername
```

Show share information in a table with only the Name and Path properties:

```text
get-smbshare | format-table -property Name,Path -autosize
```

List all properties of shared folders:

```text
get-smbshare | format-list *
```

Export shared folder information to a comma-delimited file here:

```text
C:\SANS\shares.csv get-smbshare | export-csv -path C:\SANS\shares.csv
```

To only show unique event ID numbers from the Application event log:

```text
get-eventlog Application | select-object EventID -unique
```

To select the last 10 events from the System event log:

```text
get-eventlog -logname system | select-object -last 10
```

To select the first 5 services:

```text
get-service | select-object -first 5
```

Get the contents of the log file and search it with a regular expression pattern:

```text
get-content log.txt | select-string -pattern 'mismatch'
```

Get the contents of the log file and search it with a regular expression pattern:

```text
get-content log.txt | select-string -pattern 'mismatch'
```

Powershell Scanning:

```text
1..60 | % {echo $; ping -n 1 -w 100 10.0.0.$ | select-string ttl}
```

If port is open, test connection:

```text
70..90 | % {echo $; echo ((new-object Net.Sockets.TcpClient).Connect("10.0.0.1",$)) "Port $_ is open" } 2>$null
```

To fetch a web page from 10.0.0.1, we could use the wget alias on Windows 8 and later systems:

```text
PS wget http://10.0.0.1 -outfile c:\file.html; gc c:\file.html; del c:\file.html
```

