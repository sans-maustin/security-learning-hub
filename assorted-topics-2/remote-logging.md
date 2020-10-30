# Remote Logging Methods

## Objectives

We'll go over some examples on how to use and leverage remote logging.

## Linux Apache Web File Locations

Linux Apache/HTTP Administration - Files and location:

```text
/var/www/html/index.html
/etc/httpd/conf/
/etc/apache2/apache2.conf
```

## Reading Local Logs with journalctl

Reading logs with journalctl:

```text
journalctl -u ssh.service
journalctl -u apache2.service
journalctl -u dhcpcd.service
```

## Create Self Signed Cert

Self Signed SSL Cert

```text
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/apache- selfsigned.key -out /etc/ssl/certs/apache-selfsigned.crt
```

## Apache Logging to rsyslog

Self Signed SSL Cert

```text
vi /etc/apache2/sites-enabled/000-default.conf
CustomLog "|/usr/bin/logger -t apache -p local6.info" combined

Then edit /etc/rsyslog.conf so the local6.info files go to remote logging machine:
local6.info @@10.11.12.13:514 #For TCP rsyslog
local6.info @10.11.12.13:514 #For UDP rsyslog
```

## Linux Remote rsyslog UDP

Client Setup

```text
sudo sed -i 's/#*.* @@remote-host:514/*.* @192.168.33.11/g' /etc/rsyslog.conf
sudo systemctl restart rsyslog
```

Server Setup

```text
sudo sed -i 's/#$ModLoad imudp/$ModLoad imudp/g' /etc/rsyslog.conf
sudo sed -i 's/#$UDPServerRun 514/$UDPServerRun 514/g' /etc/rsyslog.conf
sudo systemctl restart rsyslog
```

Testing from Client:

```text
logger -p syslog.info "This is a UDP message."
```

## Linux Remote rsyslog TCP

Client Setup

```text
sudo sed -i 's/*.* @192.168.33.11/*.* @@192.168.33.11/g' /etc/rsyslog.conf
sudo systemctl restart rsyslog
```

Server Setup

```text
sudo sed -i 's/#$ModLoad imtcp/$ModLoad imtcp/g' /etc/rsyslog.conf
sudo sed -i 's/#$InputTCPServerRun 514/$InputTCPServerRun 514/g' /etc/rsyslog.conf
sudo systemctl restart rsyslog
```

Testing from Client:

```text
logger -p syslog.info "This is a TCP message."
sudo systemctl restart systemd-journal-upload
```

