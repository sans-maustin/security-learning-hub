# SSH

## Objectives

We'll go over some examples on how to use and leverage ssh.

## SSH - Starting and Stopping

Take a look at the service status:

```text
systemctl cat sshd.service
systemctl status sshd.service
service --status-all
sudo service sshd status
sudo /etc/init.d/sshd status
```

Start and Stop the Service

```text
sudo service sshd stop
sudo service sshd start
chkconfig --list sshd
sudo chkconfig sshd off
sudo chkconfig sshd on
chkconfig --list sshd
```

## Basic SSH Commands

SSH to non default port

```text
ssh -p 2222 user@hostname
```

Analyze traffic remotely over ssh w/ wireshark:

```text
ssh root@server.com ‘tshark -f “port !22″ -w -‘ | wireshark -k -i
```

SSH connection through host in the middle

```text
ssh -t reachable_host ssh unreachable_host
```

Mount folder/filesystem through SSH

```text
sshfs name@server:/path/to/folder /path/to/mount/point
```

Copy ssh keys to user@host to enable password-less ssh logins.

```text
ssh-copy-id user@host
```

Copy keys to remote systems:

```text
ssh-copy-id -i ~/.ssh/id_rsa.pub linuxcbt@192.168.75.150
Recursively copy directory and contents to remote directory:
scp -r OffSec Matt@10.0.0.89:/rem_dir/OffSec
```

Causes X11/X.org programs on remote host to display locally

```text
ssh -X linuxcbtmedia1
```

Binds to specific local address and connects to remote host:

```text
ssh -b bind_address user@host: ssh -b 127.0.0.1 root@10.0.0.219
```

Runs ls -al on remote machine

```text
ssh -v root@10.0.0.219 'ls -al'
```

## SCP and SFTP Usage

Multiple files can be specified separated by a space like this:

```text
scp foo.txt bar.txt username@remotehost:/path/directory/
```

Launches SSH w/IPv6 to target:

```text
sftp -6 2002:4687:db25:2:211:11ff:fe5b:7053
```

To copy an entire directory from one host to another use the r switch and specify the directory

```text
scp -v -r ~/Downloads root@192.168.1.3:/root/Downloads
```

Copy file from Remote to Local Host:

```text
scp jsmith@remotehost.example.com:/home/jsmith/remotehostfile.txt remotehostfile.txt
```

Copy file from Local to Remote Host:

```text
scp localhostfile.txt jsmith@remotehost.example.com:/home/jsmith/localhostfile.txt
scp -rP 3838 /the/source/directory username@destinationhost.com:/destination/folder
sftp -vv root@192.168.75.50 - connects with 'debug2 & debug1' levels
sftp -o Port=2222 -o Protocol=2 192.168.75.50 - Indicate multiple options on the command-line
sftp -v 192.168.75.150:/ - connects to root of file system on target
sftp 192.168.75.150:10mill.1.txt - downloads non-interactively
sftp 192.168.75.150:10mil* - downloads non-interactively with wildcard
sftp host:file[1-4].txt - downloads the file(s) matching the mask
sftp host:f*[1-4].txt - downloads the file(s) matching the mask
```

## Misc SSH - Alt port, Banners, Key Gen

Enable SSH server on TCP:2222 - then connect using SFTP

```text
a. 'nano /etc/ssh/sshd_config' - 'Port 22, Port 2222'
b. '/etc/init.d/sshd restart' OR 'service sshd restart'
c. 'sftp -v -o Port=222 user@host' - connects to server using TCP:2222
```

SSH Banners: There are two way to display messages one is using issue.net file and second one is using MOTD file.

```text
issue.net : Display a banner message before the password login prompt.
motd : Display a banner message after the user has logged in.

vi /etc/issue.net
vi /etc/motd
```

Banner /etc/issue.net

SSH Basics and copying public key:

To generate a key-pair: ssh-keygen

This will create two files. A private key file which should be kept to yourself as you would a password. This file is called, by default, /.ssh/id\_rsa. There is also a matching public file, which is similar to a username in that servers you will be logging in to will all have a copy, and it will be associated with your account. This file is called, by default /.ssh/id\_rsa.pub.

Copy your public key file to the server you wish to log in to without typing a password: ssh-copy-id you@otherserver

Sometimes, albeit rarely, this might not work. In those cases, you’ll have to transfer it manually.

Here’s a handy bit of script to help with that: cat ~/.ssh/id\_rsa.pub \| ssh you@otherserver “mkdir -p ~/.ssh && cat &gt;&gt; ~/.ssh/authorized\_keys”

OR USING SCP:

1. Connect to your server via SSH with the user you would like to add your key to: ssh your\_username@192.0.2.0
2. Create the ~/.ssh directory and authorized\_keys file if they don’t already exist: mkdir -p ~/.ssh && touch ~/.ssh/authorized\_keys
3. Give the ~/.ssh directory and authorized\_keys files appropriate file permissions: chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized\_keys
4. In another terminal on your local machine, use scp to copy the contents of your SSH public key \(id\_rsa.pub\) into the authorized\_keys file on your server. Substitute in your own username and your server’s IP address: scp ~/.ssh/id\_rsa.pub your\_username@192.0.2.0:~/.ssh/authorized\_keys
5. Verify that you can log in to the server with your key.

OR Manually Copy an SSH Key:

1. Begin by copying the contents of your public SSH key on your local computer. You can use the following command to output the contents of the file:

   cat ~/.ssh/id\_rsa.pub

You should see output similar to the following: ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCyVGaw1PuEl98f4/7Kq3O9ZIvDw2OFOSXAFVqilSFNkHlefm1iMtPeqsIBp2t9cbGUf55xNDULz/bD/4BCV43yZ5lh0cUYuXALg9NI29ui7PEGReXjSpNwUD6ceN/78YOK41KAcecq+SS0bJ4b4amKZIJG3JWmDKljtv1dmSBCrTmEAQaOorxqGGBYmZS7NQumRe4lav5r6wOs99ACMYNANE:\)jkeZsGFzJFNqvr5DuHdDL5FAudW23me3BDmrM9ifUzzjl1Jwku3bnRaCcjaxH8oTumt1a00mWci/1qUlaVFft085yvVq7KZbF2OPPbl+erDW91+EZ2FgEi+v1/CSJ5 your\_username@hostname

Note that the public key begins with ssh-rsa and ends with your\_username@hostname.

1. Once you have copied that text, connect to your server via SSH with the user you would like to add your key to: ssh your\_username@192.0.2.0
2. Create the ~/.ssh directory and authorized\_keys file if they don’t already exist: mkdir -p ~/.ssh && touch ~/.ssh/authorized\_keys
3. Give the ~/.ssh directory and authorized\_keys files appropriate file permissions: chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized\_keys
4. Open the authorized\_keys file with the text editor of your choice \(nano, for example\). Then, paste the contents of your public key that you copied in step one on a new line at the end of the file.
5. Save and close the file.

Note: If you initially logged into the server as root but edited the authorized\_keys file of another user, then the .ssh/ folder and authorized\_keys file of that user may be owned by root. Set that other user as the files’ owner: chown -R your\_username:your\_username /home/your\_username/.ssh

1. Verify that you can log in to the server with your key.

In sshd\_config: SET: PubkeyAuthentication yes PermitRootLogin no PasswordAuthentication no

Run remote SSH command: ssh pi@192.168.1.24 uptime ssh pi@192.168.1.24 'sudo ifconfig -a' ssh pi@192.168.1.24 'ls -lactr /etc' \| tee 192.168.1.24-la-lactr-20191218

Run remote SSH command and output feedback to local file: ssh pi@192.168.1.24 'sudo ifconfig -a' &gt;192.168.1.24-ifconfig

## SSH Port Forwarding Examples

**SSH Local Port Forwarding**

Example: The key here is -L which says we’re doing local port forwarding. Then it says we’re forwarding our local port 9000 to remote-website.com:443, which is the default port for HTTPS. Now open your browser and go to [http://localhost:9000](http://localhost:9000).

```text
ssh -L 9000:remote-website.com:443 user@pivotsystem.com

ssh -L 8888:webserver:80 user@pivotsystem
localhost:8888 --> Remote:80
```

Another Example:

```text
ssh -N -f -L 9000:192.168.1.34:23 root@192.168.1.34 -p 8080
```

\[Local Connection: ncat localhost 9000\] -N - No remote command -f Background the connection -L Fwd a local port to a remote port Connect to local port 9000 \(ncat localhost 9000\) 192.168.1.34 - Computer you want to remote in to :23 - Port that is blocked \(Remote machine could have ncat -l -p 23\) root@192.168.1.34 - username, ip of remote machine -p 8080 - port you are connecting to on remote machine \(if using port 22 for default SSH, do not need to specify\)

**SSH Reverse Port Forwarding**

Example: We’ll tell SSH to make a tunnel that opens up a new port on the server, and connects it to a local port on your machine. First you need to specify the port on which th remote server will listen, which in this case is 9000, and next follows localhost for your local machine, and the local port, which in this case is 3000.

```text
ssh -R 9000:localhost:3000 user@example.com

ssh -R :8000:localhost:80 user@pivotsystem
localhost:80 <-- Remote:8000
```

SSH doesn’t by default allow remote hosts to forwarded ports. To enable this open /etc/ssh/sshd\_config and add the following line somewhere in that config file.

```text
/etc/ssh/sshd_config
GatewayPorts yes
```

Another Example:

```text
ssh -R 7900:127.0.0.1:9000 root@192.168.1.34 -p 8080
```

\[Locally - in new terminal, run nc -l -p 9000\] \[On Remote 192.168.1.34 Host run: nc localhost 7900\]

**SSH Dynamic Port Forwarding**

Dynamic Port Forwarding \(SOCKS v4/v5\): \(Need to configure browser to point to localhost and port to proxy traffic\)

```text
ssh -D 9000 user@pivotsystem: localhost:9000
Remote:Any Port : Can use SOCKS Proxy Chains
```

```text
ssh -D 8181 -N -f root@192.168.1.44 -p 8080
```

\[Web Browser on Local Host: point to 8181\] \[Netcat Relay to a remote system listening on port 2000:ncat localhost 127.0.0.1 8181\|ncat 192.168.1.44 20000 &gt; /tmp/massscan\] -D dynamic forwarding of traffic All traffic locally pointing to port 8181 will be tunneled to 192.168.1.44 on port 8080

Dynamic SSH Tunnel and Proxy Scan

```text
ssh -f -N -D 1081 root@192.168.1.34 -p 8080
sudo nmap -sV -Pn -n --proxies socks4://127.0.0.1:1081 8.8.8.8
```

**Gateway Port Forwarding** Features: 1. Works with local and remote/reverse port forwarding 2. Shares forwarded ports with connected subnet\(s\)

Note: Ensure that 'GatewayPorts' is set to 'yes' in /etc/ssh/sshd\_config

```text
ssh -g -L 3389:10.10.10.2:3389 root@linuxcbtsuse2
ssh -g -L 3389:linuxcbt2k3:3389 root@linuxcbtsuse2
```

**SSH Proxy Jump**

```text
ssh -J root@192.168.1.34:8080 root@192.168.1.44
```

Global config etc/ssh/sshd\_config file for the SSHD server: LogLevel VERBOSE \#Enable Verbose Logging: DEBUG-3 can also be used but may violate privacy PermitRootLogin no \#Disable Root Login Protocol 2 \#Only allow Protocol 2 DenyUsers \#Deny Users to remotely login AllowUsers jim \#Only allow Jim to login remotely PermitRootLogin no \#Disallow root login

AllowTcpForwarding No \#Disallow SSH port forwarding - is enabled by default on most systems X11Forwarding no \#Specifies whether X11 forwarding is permitted - default is no ListenAddress \#Specifies the local addresses sshd should listen on Port \#Specifies the port number that sshd\(8\) listens on PasswordAuthentication\#Specifies whether password authentication is allowed. The default is ''yes''. LogLevel VERBOSE \#The key fingerprint for any SSH key used for login is logged.\)

Example URLs: [https://blog.trackets.com/2014/05/17/ssh-tunnel-local-and-remote-port-forwarding-explained-with-examples.html](https://blog.trackets.com/2014/05/17/ssh-tunnel-local-and-remote-port-forwarding-explained-with-examples.html)

