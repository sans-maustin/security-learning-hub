# Zeek

## Objectives

We'll go over some examples on how to use and leverage zeek/Zeek.

## Zeek Basics

Zeek replay pcap file

```text
Zeek –r sample1.pcap local
```

DNS Logs with human readable timestamp and UTC TZ:

```text
cat dns.log | zeek-cut -d ts query | sort -u cat dns.log | zeek-cut -u ts query | sort -u
cat dns.log |zeek-cut -d ts uid id.orig_h id.orig_p id.resp_h id.resp_p query answers
```

DNS query/response pairs

```text
zeek-cut id.orig_h id.orig_p id.resp_h id.resp_p query answers < dns.log
```

DNS Logs with human readable timestamp and UTC TZ:

```text
cat dns.log | zeek-cut -d ts query | sort -u
cat dns.log | zeek-cut -u ts query | sort -u
```

Zeek Top File Types:

```text
cat files.log | zeek-cut mime_type | sort | uniq -c | sort -rn
```

Top Software by number of hosts:

```text
cat software.log | zeek-cut host name | sort | uniq | awk -F '\t' '{print $2}' | sort | uniq -c | sort -rn
```

## Zeek Misc

Only display timestamp, method, host and URI and convert timestamp to human readable

```text
cat http.log | zeek-cut –d ts method host uri
```

Review SIP/DPORT/DIP/SPORT/Host Details, URI and referrer details

```text
cat http.log | zeek-cut id.orig_h id.orig_p id.resp_h id.resp_p host uri referrer
```

Human readable timestamp, SIP/SPORT/DIP/DPORT, method URI and user\_agent

```text
cat http.log | zeek-cut -d id.orig_h id.orig_p id.resp_h id.resp_p method uri user_agent
```

Use Zeek to read the file and launch the extract.Zeek script.

```text
sudo Zeek -r /pcaps/virut-worm.pcap /opt/Zeek/share/Zeek/fileextraction/extract.Zeek
```

Review DNS Requests:

```text
cat dns.log | zeek-cut query | sort –u
```

View Connection State:

```text
cat conn.log | zeek-cut ts id.orig_h, id.orig_p, id.resp_h, id.resp_p, proto, conn_state
```

View Source/Destination/Host/URI:

```text
less http_eth1.log | zeek-cut id.orig_h id.resp_h host uri | sort | uniq -c | sort -n|more
```

Zeek SSL Review:

```text
less ssl.log | zeek-cut id.orig_h id.resp_h server_name | sort | uniq -c | sort -n|more
```

Zeek Software Log:

```text
less software.log | zeek-cut host software_type name | sort | uniq -c | sort -n|more
```

Zeek Files Downloaded:

```text
less files.log | zeek-cut tx_hosts rx_hosts filename | sort | uniq -c | sort -n|more
```

Zeek Options:

```text
less http_eth1.log | zeek-cut user_agent resp_mime_types | sort | uniq -c | sort -n
less http_eth1.log | zeek-cut host | sort | uniq -c | sort -n|more
```

Grab source host, source port, packets sent from source, and same information for responding host:

```text
cat conn.log | zeek-cut id.orig_h id.orig_p orig_pkts id.resp_h id.resp_p resp_pkts |sort - nr|more
```

Look at SSL Logs for source host, source port, and same information for responding host as well as server name:

```text
cat ssl.log | zeek-cut id.orig_h id.orig_p id.resp_h id.resp_p server_name
```

Similar to above, but exclude all com and .net domains:

```text
cat ssl.log | zeek-cut id.orig_h id.resp_h server_name|sort -u|egrep -iv '.com|.net'
```

Longest connections \(longest is last\)

```text
zeek-cut –f 1-9 < conn.log | sort -t$'\t' -k 9 –n
```

Connections longer than 90s

```text
zeek-cut < conn.log | awk -F$'\t' '$9 > 90'
```

Webservers on nonstandard ports

```text
zeek-cut service id.resp_p id.resp_h < conn.log| awk -F$'\t' '$1 == "http" && ! ($2 == 80 || $2 == 8080) { print $3 }'| sort –u
```

Number of connections by service

```text
zeek-cut service < conn.log | sort | uniq -c | sort –n
```

Top 10 dest ports

```text
zeek-cut id.resp_p < conn.log | sort | uniq -c | sort -rn | head -n 10
```

HTTP unique user agent strings

```text
zeek-cut user_agent < http.log | sort –u
```

Agent strings by IP address & hold the Mozilla, please

```text
zeek-cut user_agent id.orig_h < http.log | sort –u | egrep –v “Mozilla”
```

HTTP downloaded mime types

```text
zeek-cut orig_mime_types id.orig_h id.orig_p < http.log | sort -u
```

10 most common websites

```text
zeek-cut host < http.log | sort | uniq -c | sort -n | tail -n 10
```

Top 10 talkers

```text
cat conn.log | zeek-cut id.orig_h id.resp_h id.resp_p resp_bytes | sort -k 4 -rn | head -10
```

DHCP Log Review:

```text
cat dhcp.log |zeek-cut -d ts id.orig_h id.orig_p id.resp_h id.resp_p mac assigned_ip
```

Look for info in files.log:

```text
cat files.log |zeek-cut -d ts tx_hosts rx_hosts source mime_type extracted
```

SSL Log Analysis:

```text
cat ssl.log |zeek-cut  id.orig_h  server_name|sort|uniq|sort -rn|more
```

See is a file in the pe.log is an executable and for what OS:

```text
cat pe.log |zeek-cut -d ts id subsystem is_exe
```

Query http.log for information:

```text
cat http.log |zeek-cut -d ts uid id.orig_h id.orig_p id.resp_h id.resp_p status_code host uri
```

## Zeek Signatures

Run Zeek Signatures

```text
zeek –Cr inbetween.pcap –s windows-banner.sig
```

Extract filetypes known to zeek \(SMB likely isn’t part of this\)

```text
event file_new(f: fa_file)
{
Files::add_analyzer(f,Files::ANALYZER_EXTRACT);
}
# zeek –C –r <pcap> <script file>
```

Signature for Windows Command Line Using Regular Expression:

```text
signature cmd-exe {   ip-proto == tcp   dst-port == 4444   payload /^Microsoft Windows.*Copyright \(c\).*Microsoft Corporation/   event "Windows Command Line Banner using Metasploit"}
```

SMB Tree Creation

```text
event smb2_tree_connect_request(c: connection, hdr: SMB2:
:Header, path: string) {print path;}
```

## Various other log details

**Possible SYN Scans** Never had a reply \(S0\) - left the connection half-open \(SH, which means a SYN-ACK from the responder was never seen\)

