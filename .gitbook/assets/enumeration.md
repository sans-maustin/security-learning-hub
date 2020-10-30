# <h1 id="topheading">Enumeration Methods</h1>

## Overview

This provides some details on various email and harvesting methods. This is not exhaustive and some of the tips that are not here are in other pages - for instance, dns enumeration is located under DNS. I did not want to create a page for each method, so I am placing the generic content here which I don't have a place for right now.

## theHarvester to enumerate email addresses:

Use Google to enumerate 100 email addresses for whitehouse.gov domain via google:
```
theHarvester -d ec-council.com -l 100 -b google
```

More Email Harvesting:
```
theharvester -d <domain-to-enumerate> -b <set-data-source-to-search>
theharvester -d ec-council.com -b google
```
## Enum Windows Machines and More

Enum Windows Machine:
```
enum4linux -u martin -p apple -U 10.10.10.12
```
Enum Windows Password Policy:
```
enum4linux -u martin -p apple -P 10.10.10.12
```
Enum Windows Groups:
```
enum4linux -u martin -p apple -G 10.10.10.12
```
Enum Windows Share Policy:
```
enum4linux -u martin -p apple -S 10.10.10.12
```

Username Enumeration Resources:
https://tools.kali.org/information-gathering/smtp-user-enum
https://threat.tevora.com/email-enumeration-with-prowl/
https://pentestlab.blog/2012/11/20/smtp-user-enumeration/
https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/03-Identity_Management_Testing/04-Testing_for_Account_Enumeration_and_Guessable_User_Account
https://guides.codepath.com/websecurity/Username-Enumeration
