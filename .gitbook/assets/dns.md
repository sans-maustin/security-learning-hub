# <h1 id="topheading">DNS Stuff</h1>

## Objectives

We'll go over some examples on how to use and leverage dig/host/nslookup, and other tools to obtain DNS data.

## Examples to Practice

**a)**	Use dig to perform a regular A name lookup

    dig A ipv6.he.net

**b)**	Use dig to perform a regular an IPv6 name lookup

    dig AAAA ipv6.he.net

**c)**	Use dig to perform a shortened lookup

    dig AAAA ipv6.he.net +short

**d)**	Host method to obtain ipv6 details

    host -t AAAA ipv6.he.net

**e)**	Look for any DNS records - dig, host, and nslookup

    dig zonetransfer.me -t any
    host -t any zonetransfer.me
    nslookup -type=any zonetransfer.me

**f)**	Look for Name Server - dig, host, and nslookup

    dig zonetransfer.me -t ns
    host -t ns zonetransfer.me
    nslookup -type=ns zonetransfer.me

**f)**	Zone transfer - dig and host

    dig axfr @nsztm1.digi.ninja zonetransfer.me
    host -l zonetransfer.me nsztm1.digi.ninja

**f)**	Other Tools - DNSEnum and DNSRecon Zone Transfer

    dnsenum zonetransfer.me
    dnsrecon.py -d zonetransfer.me -t axfr
    dnsrecon.py -a -d zonetransfer.me
    nmap --script=dns-zone-transfer zonetransfer.me
    nmap --script=dns-brute zonetransfer.me

**g)**	DNSRecon reverse lookup enumeration use:
```
dnsrecon.py -r
```

**h)**	DNSRecon Domain Name Brute-Force we need to type:
```
dnsrecon.py -d -D -t brt
dnsrecon -d megacorpone.com -D ~/list.txt -t brt
```

**i)**	DNSRecon DNS cache snooping:
```
dnsrecon.py -t snoop -n Sever -D
```

**j)**	Brute Force with Nmap:
```
nmap --script dns-brute www.foo.com
```

## Sublist3r

Use Sublist3r to enumerate sub domains:
```
sublist3r -d ec-council.com
```

## Gobuster

Example showing content length:
```
gobuster dir -u https://buffered.io -w ~/wordlists/shortlist.txt -l
gobuster dir -u http://10.168.1.1 -w /usr/share/spiderfoot/dicts/subdomains.txt -t 40 -x .html,.php --wildcard
```

Subdomain Check:
```
gobuster dns -d <domain-to-look-at> -w /usr/share/spiderfoot/dicts/subdomains.txt -i
```

Wildcard DNS is also detected properly:
```
gobuster dns -d 0.0.1.xip.io -w ~/wordlists/subdomains.txt
```

If the user wants to force processing of a domain that has wildcard entries, use --wildcard:
```
gobuster dns -d 0.0.1.xip.io -w ~/wordlists/subdomains.txt --wildcard
```

GoBuster nNormal vhost scan example:
```
gobuster vhost -u https://mysite.com -w common-vhosts.txt
```
