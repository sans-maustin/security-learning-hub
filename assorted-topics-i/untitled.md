# Apache Basics

## Objectives

This page will go over the basics of Apache basics, hardening, SSL, Mod Rewrite, Mod Sec, logging, etc. \(not in any particular order\).

## Web Basics - Codes, Service Care, File Locations

**Method and Description**

```text
GET - Used to retrieve information from the given server using a given URL. GET retrieve data only and don't have other effect on the data.
HEAD - Same as GET, but transfers the status line and header section only without the response body
POST - Used to submit data to be processed to a specified resource
PUT - Replaces all current representations of the target resource with the uploaded content
DELETE - Removes all current representations of the target resource given by a URL
CONNECT - Establishes a tunnel to the server identified by a given URL
OPTIONS - Describes the communication options for the target resource
```

**Basic HTTP Status Codes and Description**

```text
200 - Success, Document Returned correctly
301 - The requested page has moved to a new url
404 - The server cannot find the requested page
500 - The requested was not completed, the server met an unexpected condition
More Codes can be found here: https://httpstatuses.com/
```

**Start Service, Check Status, Start on Startup, and find location where Apache is installed**

```text
systemctl start (httpd|apache2)
systemctl status (httpd|apache2)
systemctl enable (httpd|apache2)
whereis (httpd|apache2)
```

**Popular Files and Locations:**

```text
/var/www/html/index.html
/etc/httpd/httpd.conf
/etc/apache2/apache2.conf
/etc/apache2/site_available/default/
```

**Config Parameters in /etc/httpd/conf/httpd.conf:**

```text
ServerRoot "/etc/httpd" #Where web server is installed and other important files
Listen 80 #All interfaces on the machine will listen on port 80 for traffic
User apache #Username used for running apache
Group apache #Groupname used for running apache
ServerAdmin emailaddress #Email address to send all server errors to
ServerName #Name and port that the server uses to identify itself.
ServerSignature Off #“server signature” option available in the apache configuration file
ServerTokens Prod #tells the web server to return only apache and suppress the OS major and minor version
DocumentRoot "/var/www/html" #Where files and directories are ran from
FileETag None #It allows remote attackers to obtain sensitive information like inode number, multipart MIME boundary, and child process through Etag header.
```

More options, and adjusting them for hardening, are found below.

## Disable directory browser listing

Go to $Web\_Server/conf folder and modify httpd.conf Add the following directives and save the httpd.conf

Search for Directory and change Options directive to None or –Indexes

```text
<Directory /opt/apache/htdocs>
Options -Indexes
</Directory>
Copy

(or)

<Directory /opt/apache/htdocs>
Options None
</Directory>
```

Restart apache

## Run apache from non-priv account

A default installation runs as nobody or daemon. Using a separate non-privileged user for Apache is good.

Create a user and group called apache

```text
groupadd apache
useradd –G apache apache
```

Change apache installation directory ownership to a newly created non-privileged user

```text
chown –R apache:apache /opt/apache
```

Go to $Web\_Server/conf folder and modify httpd.conf

```text
User apache
Group apache
```

Restart apache

## System Settings Protection

In a default installation, users can override apache configuration using .htaccess. If you want to stop users from changing your apache server settings, you can add **AllowOverride** to **None** as shown below.

Go to $Web\_Server/conf folder and modify httpd.conf

```text
<Directory />
Options -Indexes
AllowOverride None
</Directory>
```

Restart apache

## HTTP Request Methods

HTTP 1.1 protocol support many request methods which may not be required and some of them are having potential risk.

Typically you may need GET, HEAD, POST request methods in a web application, which can be configured in the respective Directory directive.

Default configuration support OPTIONS, GET, HEAD, POST, PUT, DELETE, TRACE, CONNECT method in HTTP 1.1 protocol.

Go to $Web\_Server/conf folder and modify httpd.conf

```text
<LimitExcept GET POST HEAD>
deny from all
</LimitExcept>
```

Restart apache

## Disable Trace HTTP Request

By default Trace method is enabled in Apache web server. Having this enabled can allow Cross Site Tracing attack and potentially giving an option to a hacker to steal cookie information.

Go to $Web\_Server/conf folder and modify httpd.conf

```text
TraceEnable off
```

Restart apache

## Set cookie with HttpOnly and Secure flag

You can mitigate most of the common Cross Site Scripting attack using HttpOnly and Secure flag in a cookie. Without having HttpOnly and Secure, it is possible to steal or manipulate web application session and cookies, and it’s dangerous.

Ensure mod\_headers.so is enabled in your httpd.conf

Go to $Web\_Server/conf folder and modify httpd.conf

```text
Header edit Set-Cookie ^(.*)$ $1;HttpOnly;Secure
```

Restart apache

## Clickjacking Attack

Clickjacking is a well-known web application vulnerabilities.

Go to $Web\_Server/conf folder and modify httpd.conf

```text
Header always append X-Frame-Options SAMEORIGIN
```

Restart apache

## Server Side Include

Server Side Include \(SSI\) has a risk of increasing the load on the server. If you have shared the environment and heavy traffic web applications you should consider disabling SSI by adding Includes in Options directive.

Go to $Web\_Server/conf folder and modify httpd.conf

```text
<Directory /opt/apache/htdocs>
Options –Indexes -Includes
Order allow,denyAllow from all
</Directory>
```

Restart apache

## X-XSS Protection

Cross Site Scripting \(XSS\) protection can be bypassed in many browsers. You could apply this protection for a web application if it was disabled by the user.

Go to $Web\_Server/conf folder and modify httpd.conf

```text
Header set X-XSS-Protection "1; mode=block"
```

Restart apache

## Disable HTTP 1.0 Protocol

HTTP 1.0 has security weakness related to session hijacking. We can disable this by using the mod\_rewrite module.

Ensure to load mod\_rewrite module in httpd.conf file Enable RewriteEngine directive as following and add Rewrite condition to allow only HTTP 1.1

```text
RewriteEngine On
RewriteCond %{THE_REQUEST} !HTTP/1.1$
RewriteRule .* - [F]
```

Restart apache

## Timeout value configuration

By default, Apache time-out value is 300 seconds, which can be a victim of Slow Loris attack and DoS. To mitigate this, you can lower the timeout value to maybe 60 seconds.

Go to $Web\_Server/conf folder and modify httpd.conf

```text
Timeout 60
```

Restart apache

## Creating a directory to serve up pages from

Create 2 different websites

```text
cd /var/www/html
mkdir mynewserver mynewserver2
echo "This is mynewserver's index page. Listening on port 8080!" >mynewserver/index.html
echo "This is mynewserver2's index page. Listening on port 8090!" >mynewserver2/index.html
```

cd and configure new files:

```text
cd /etc/httpd/conf.d/
cd /etc/apache2/sites-enabled/

nano mynewserver.conf

Listen 8080
<VirtualHost *:8080>
    ServerName mynewserver.com
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/html/mynewserver
    CustomLog /var/log/apache2/8080_access.log combined
</VirtualHost>

nano mynewserver2.conf

Listen 8090
<VirtualHost 192.168.1.222:8090>
    ServerName mynewserver2.com
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/html/mynewserver2
    CustomLog /var/log/apache2/8090_access.log combined
</VirtualHost>
```

Check connection to website:

```text
curl http://127.0.0.1:8090/ -I
```

## Rsyslog Configuration

Edit the following file first: vi /etc/apache2/sites-enabled/000-default.conf CustomLog "\|/usr/bin/logger -t apache -p local6.info" combined

Then edit /etc/rsyslog.conf so the local6.info files go to remote logging machine: local6.info @10.11.12.13:514 \#For UDP rsyslog local6.info @@10.11.12.13:514 \#For TCP rsyslog

## HTTP Basic Auth for Debian

Create the Password File \(1st option uses -c to create the file - this is not needed after the first time\) sudo htpasswd -c /etc/apache2/.htpasswd user1 sudo htpasswd /etc/apache2/.htpasswd user2

In ''/etc/apache2/sites-enabled/000-default.conf' sudo nano /etc/apache2/sites-enabled/000-default.conf

```text
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
    <Directory "/var/www/html">
        AuthType Basic
        AuthName "Restricted Content"
        AuthUserFile /etc/apache2/.htpasswd
        Require valid-user
    </Directory>
</VirtualHost>
```

## SSL Config on Debian

Step 1: Use OpenSSL to generate CSR with 2048 bit as below:

```text
openssl req -out raspberrypi.csr -newkey rsa:2048 -nodes -keyout raspberrypi.key
```

Step 2: Generate Certificate Signing Request \(CSR\)

```text
openssl req -new -key raspberrypi.key -out raspberrypi.csr
```

Step 3: Step 3\) Create SSL Certificate

```text
openssl x509 -req -days 365 -in raspberrypi.csr -signkey raspberrypi.key -out raspberrypi.crt
```

in /etc/httpd/conf.d directory add:

```text
Include conf.d/*.conf
LoadModule ssl_module modules/mod_ssl.so
SSLEngine on
SSLCertificateFile /var/www/html/raspberrypi.crt
SSLCertificateKeyFile /var/www/html/raspberrypi.key
SSLCipherSuite ALL:!aNULL:!ADH:!eNULL:!LOW:!EXP:RC4+RSA:+HIGH:+MEDIUM
SSLProtocol -ALL +TLSv1.2
```

Step 4: Restart Apache

```text
systemctl restart apache2.service
```

## SSL Configuration

Generate Key w/OpenSSL:

```text
openssl req -out webserver.csr -newkey rsa:2048 -nodes -keyout webserver.key
```

Once you receive the signed certificate file, you can add them in httpd-ssl.conf file:

```text
SSLCertificateFile #Certificate signed by authority
SSLCertificateChainFile #Certificate signer given by authority
SSLCertificateKeyFile #Key file which you generated above
```

## SSL Cipher

It’s based on your web server SSL Cipher configuration the data encryption will take place. So it’s important to configure SSL Cipher, which is stronger and not vulnerable.

Go to $Web\_Server/conf folder and modify httpd-ssl.conf

Modify SSLCipherSuite directive in httpd-ssl.conf as below to accept only higher encryption algorithms

```text
SSLCipherSuite HIGH:!MEDIUM:!aNULL:!MD5:!RC4
```

Restart apache

## Disable SSL v2 & v3

Any SSL v2/v3 communication may be vulnerable to a Man-in-The-Middle attack that could allow data tampering or disclosure. Let’s implement apache web server to accept only latest TLS and reject SSL v2/v3 connection request.

Go to $Web\_Server/conf folder and modify httpd-ssl.conf

```text
SSLProtocol –ALL +TLSv1.2
```

Restart apache

## Mod Security

To use Mod security feature with Apache, we have to load mod security module in httpd.conf. The mod\_unique\_id module is pre-requisite for Mod Security.

Add following a line to load module for Mod Security in httpd.conf and save the configuration file

```text
LoadModule unique_id_module modules/mod_unique_id.so
LoadModule security2_module modules/mod_security2.so
```

Restart apache web server

Install ModSecurity in Debian or Ubuntu: sudo apt-get install libapache-mod-security

Install ModSecurity core rule set \(CRS\): cd /etc/apache2 cp –R /usr/share/doc/mod-security-common/examples/rules ./

Edit /etc/apache2/conf.d/security to include ModSecurity:

Include /etc/apache2/rules/.conf Include /etc/apache2/rules/base\_rules/.conf

Enable the ModSecurity module and restart Apache: a2enmod mod-security systemctl restart apache2.service

ModSecurity Testing

Disable mod\_security module & restart Apache a2dismod mod-security systemctl restart apache2.service

Launch Firefox and use test link [http://localhost/?abc=../../](http://localhost/?abc=../../) Result: 200 Forbidden

Restart mod\_security a2enmod mod-security /etc/init.d/apache2 restart

Use test link again [http://localhost/?abc=../../](http://localhost/?abc=../../) Result: 403 Forbidden

ModSecurity Logging vs. Blocking Logging vs. Blocking SecRuleEngine On/DetectionOnly/Off Start by logging and not blocking \(DetectionOnly\)

Enable ModSecurity, attaching it to every transaction for detection, not block

```text
SecRuleEngine DetectionOnly
```

Enable ModSecurity, attaching it to every transaction for block

```text
SecRuleEngine On
```

ModSecurity Rule:

```text
SecRule REQUEST_METHOD "!@rx ^(?:GET|HEAD|POST|OPTIONS)$" "phase:1,t:none,block,msg:'Method not allowed',logdata:%{REQUEST_METHOD}"
```

This rule restricts methods to GET, HEAD, POST, & OPTIONS.

```text
Variables identify which part of the HTTP request to analyze
REQUEST_METHOD
Operators specify a regex to look for in the variable
"!@rx ^(?:GET|HEAD|POST|OPTIONS)$"
Transformations reformat the variable before analysis (not used in example)
Actions specify what should be done if the rule matches
Blocking Action
phase:1,t:none,block,msg:'Method not allowed'
Logging Action
logdata:%{REQUEST_METHOD}
```

SSN Detection:

```text
SecRule ARGS "@verifySSN \d{3}-?\d{2}-?\d{4}" "phase:2,nolog,pass,msg:'Potential social security number',sanitiseMatched"
```

## Mod Rewrite

Turn on the rewrite engine

```text
RewriteEngine On
```

Redirect /news/123 to /news.cfm?id=123

```text
RewriteRule ^/news/([0-9]+)$ /news.cfm?id=$1 [PT,L]
```

Redirect www.example.com to example.com

```text
RewriteCond %{HTTP_HOST} ^www\.example\.com$ [NC]
RewriteRule ^(.*)$ http://example.com$1 [R=301,L]
```

## Mod SSL

Install and use MOD SSL - \(RHEL/CentOS\):

A. Install `mod_ssl` on the host `webserver`.

```text
yum install mod_ssl
```

B. Generate and sign the private key for `shop.example.com` using `openssl`. a. Create a new encrypted private key.

```text
openssl genrsa -aes128 -out /etc/pki/tls/private/httpdkey.pem
```

b. Enter httpd at the passphrase prompt. c. Generate a self-signed certificate using the key.

```text
openssl req -new -x509 -key /etc/pki/tls/private/httpdkey.pem -out /etc/pki/tls/certs/httpdcert.pem -days 365
```

d. Enter httpd at the passphrase prompt. e. At the prompts, enter the field information provided in the instructions \(use defaults where not specified\).

C. Update the default Apache virtual host to accept connections on `shop.example.com` using the new keypair, and allow HTTPS traffic through the firewall.

a. Make the following changes to /etc/httpd/conf.d/ssl.conf:

b. At the end of the  section, add the following on a new line: ServerName shop.example.com:443

c. Locate the line SSLCertificateFile /etc/pki/tls/certs/localhost.crt, and change it to the following:

```text
SSLCertificateFile /etc/pki/tls/certs/httpdcert.pem
```

d. Locate the line SSLCertificateKeyFile /etc/pki/tls/private/localhost.key, and change it to the following:

```text
SSLCertificateKeyFile /etc/pki/tls/private/httpdkey.pem
```

e. Restart httpd to put the changes into effect.

```text
systemctl restart httpd
```

f. Enter httpd at the passphrase prompt. g. Open port 443 on the OS firewall.

```text
sudo firewall-cmd --add-service=https --permanent
sudo firewall-cmd --reload
```

D. Verify the configuration with `openssl` from the host `workstation`. On the workstation machine, run the following command:

```text
openssl s_client -connect shop.example.com:443 > /home/cloud_user/httpd_output
```

## Access Control Examples

Various httpd.conf/apache2.conf config settings to Secure Apache installation:

```text
<Directory "/var/www/restrictedsite/">

<RequireAll>
    Require all granted
    Require not ip 10.252.46.165
    Require not host gov
</RequireAll>

</Directory>
```

Save the file and restart the web server:

```text
systemctl restart httpd (RH/CentOS) or
systemctl restart apache2 (Debian/Ubuntu)
```

## Validate Apache has SSL enabled

1.Generate a certificate signing request \(CSR\). 2.Generate a self-signed certificate. 3.Copy the certificate and keys we've generated. 4.Tell Apache about the certificate by modifying the VirtualHosts to use the certificate. 5.Restart Apache and test.

a. Let’s start with making sure that SSL is enabled by using the a2enmod utility to enable the SSL module: sudo a2enmod ssl

b. Generate the CSR sudo openssl req -new &gt; new.ssl.csr

c. Generate the Certificate sudo openssl rsa -in privkey.pem -out new.cert.key sudo openssl x509 -in new.ssl.csr -out new.cert.cert -req -signkey new.cert.key -days 1000 sudo cp new.cert.cert /etc/ssl/certs/server.crt sudo cp new.cert.key /etc/ssl/private/server.key

d. Configure Apache nano /etc/apache2/sites-available/

Add following:

```text
<VirtualHost *:443>
    ServerAdmin
    ServerName 192.168.1.44
    ServerAlias 192.168.1.44
    DocumentRoot /var/www/html/ssl

      ErrorLog /var/log/ssl/error.log
      CustomLog /var/log/ssl/access.log combined

      SSLEngine on
      SSLProtocol -all +TLSv1.2 +TLSv1.3
      SSLOptions +StrictRequire
      SSLCertificateFile /etc/ssl/certs/server.crt
      SSLCertificateKeyFile /etc/ssl/private/server.key
</VirtualHost>
```

e. Restart Apache and test. sudo a2ensite

See what modules are installed: a2query -m

## Change Apache Port and configure SELinux to monitor the new port:

1. Change Apache port from 80 to 61297 sudo vim /etc/httpd/conf/httpd.conf Find "Port" and change the value from 80 to 61297
2. Configure SELinux and start Apache sudo semanage port -a -t http\_port\_t -p tcp 61297 sudo systemctl start httpd
3. Close Port 80 with Firewalld and open the new port with Firewalld sudo firewall-cmd --permanent --remove-port=80/tcp sudo firewall-cmd --permanent --add-port=61297/tcp sudo firewall-cmd --reload

## Self signed cert for apache and define password protected directory:

**Make secure directory and generate key:** mkdir -m 700 /var/www/certs cd /var/www/certs openssl genrsa -out server.key 4096

**Now to generate a Certificate Signing Request \(CSR\):** openssl req -new -key server.key -out server.csr

**Now to sign it ourselves \(if you were going to create a commercial key, you would send the CSR file in to get signed\):** openssl x509 -req -days 730 -in server.csr -signkey server.key -out server.crt chmod 400 \*

**Starting the Web Server with SSL Enabled:** systemctl start httpd

**Edit ssl.conf file and add path to cert files:** vi /etc/httpd/conf.d/ssl.conf SSLCertificateFile /var/www/certs/server.crt SSLCertificateKeyFile /var/www/certs/server.key

**Password protected directory:** Make new directory: mkdir /var/www/html-ssl-pw-protect echo Under construction &gt;/var/www/html-ssl/index.txt

**Next, create an empty /etc/httpd/conf/htpasswd file with appropriate permissions and use the htpasswd command to add a user:** touch /etc/httpd/conf/htpasswd chown root:apache /etc/httpd/conf/htpasswd chmod 640 /etc/httpd/conf/htpasswd htpasswd -B /etc/httpd/conf/htpasswd testuser

**Now define this in ssl.conf file:**

vi /etc/httpd/conf.d/ssl.conf

```text
<VirtualHost _default_:443>

DocumentRoot "/var/www/html-ssl-pw-protect"

<Directory "/var/www/html-ssl-pw-protect">
    Order deny,allow
    AllowOverride None
    AuthType Basic
    AuthName "Secret Plans"
    AuthUserFile "/etc/httpd/conf/htpasswd"
    Require valid-user
</Directory>
```

## Apache Web Server Hardening References:

[https://www.digitalocean.com/community/tutorials/how-to-set-up-password-authentication-with-apache-on-ubuntu-14-04](https://www.digitalocean.com/community/tutorials/how-to-set-up-password-authentication-with-apache-on-ubuntu-14-04) [https://geekflare.com/apache-web-server-hardening-security/](https://geekflare.com/apache-web-server-hardening-security/) [https://www.loggly.com/ultimate-guide/centralizing-apache-logs/](https://www.loggly.com/ultimate-guide/centralizing-apache-logs/) [https://tecadmin.net/install-apache-macos-homebrew/](https://tecadmin.net/install-apache-macos-homebrew/) [http://rafaelsteil.com/apache-remote-logging-with-rsyslog/](http://rafaelsteil.com/apache-remote-logging-with-rsyslog/) [https://geekflare.com/apache-web-server-hardening-security/](https://geekflare.com/apache-web-server-hardening-security/)

