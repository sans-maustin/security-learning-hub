# Rwfilter

## Objectives

We'll go over some examples on how to use and leverage rwfilter, and other tools to obtain DNS data.

References: [https://tools.netsa.cert.org/silk/silk-quickref.pdf](https://tools.netsa.cert.org/silk/silk-quickref.pdf)

## SiLK Flow Fields:

```text
1 - SIP
2 - DIP
3 - SPORT
4 - DPORT
5 - Proto#
6 - Pkts Count
7 - Bytes Count
8 - Flags
9 - Start Time
10 - Duration
11 - End time
12 - Sensor Name
```

## Convert to SILK:

```text
rwp2yaf2silk --in=attack.pcap --out=cap1.silk
```

## Determine all protocols used in time frame:

```text
rwfilter --type=all --start-date 2019/05/01 --end-date 2019/05/04 --proto=0-255 --pass=stdout | rwstats --count=10 --field=proto
```

## Determine all TCP flow records used in time frame:

```text
rwfilter --type=all --start-date 2019/05/01 --end-date 2019/05/04 --proto=6 --print-stat
```

## List all protocols that were seen:

```text
rwfilter --type=all --start-date 2019/05/01 --end-date 2019/05/04 --proto=0-255 --pass=stdout | rwstats --count=10 --field=proto
```

## Extract all TCP flows, display fields 1-8:

```text
rwfilter cap1.silk --proto=6 --pass=stdout|rwcut -f 1-8
```

## Print out all IPv4 and IPv6 Packets in Silk file:

```text
rwfilter external.silk --ip-version=4 --print-stat
rwfilter external.silk --ip-version=6 --print-stat
```

## Extract all TCP flows, display top 5 destination ports by number of flows:

```text
rwfilter cap1.silk --proto=6 --pass=stdout| restates --fields deport --count=5 --flows
```

## SHOW SINGLE FLOW THAT HAS PROTO VALUE OF 17:

```text
rwfilter cap1.silk --proto=17 --pass=stdout --max-pass=1 |rwcut -f 1-5
```

## Show all flows that have a port value other than UDP:

```text
rwfilter cap1.silk --proto=17 --fail=stdout --max-pass=1 |rwcut -f 1,2,5
```

## Show all flows and TCP based flows on specific time frames

```text
rwfilter --type=all --start-date 2018/10/01 --end-date 2018/10/15 --proto=0-255 --print-stat
rwfilter --type=all --start-date 2018/10/01 --end-date 2018/10/15 --proto=6 --print-stat
```

## Try to ID flows which contain data by using TCP flags:

```text
rwfilter cap1.silk --saddress=192.168.1.1 --proto=6 -- dport=11,102,120 --flags-all=PA/PA,A/A --pass=stdout |rwcut --f 1-8
```

## Client/Server:

```text
rwfilter cap1.silk --proto=6 --max-pass=2 -- pass=stdout |rwcut -f 1-5,8,initialFlags
```

## Top 5 services on the network:

```text
rwfilter cap1.silk --proto=6 --flags-initial=S/S --pass=stdout |rwstats --fields dport --count 5 --flows
```

## Network Performance/Admin top TCP talkers:

```text
rwfilter cap1.silk --flags-initial=S/S --ack-flag=1 --proto=6 --packets=4 --pass-stdout |rwstats --count=5 -- fields=1
```

Top talkers by source IP

```text
rwstats <whatever.silk> --fields=sip –top –bytes –count 10
```

Look for hosts sending more than 100K by source and destination IP

```text
rwstats <whatever.silk> --proto=6 –bytes=100000- --pass=stdout | rwcut –f 1-8
```

Any traffic to/from a host

```text
rwfilter <whatever.silk> --any-address=<the IP> --pass=stdout | rwcut –f 1=7
```

## Extract TCP flows and pass them to rwstat to show the top 5 destination ports by number of flows

```text
rwfilter cap1.silk --proto=6 --pass=stdout |rwstats --dport --count=5 --flows
```

## The following filters will focus on a source address of 10.0.0.15, using TCP with a destination port of 25:

```text
rwfilter cap1.silk --saddress=10.0.0.15 --proto=6 -- dport=25 --pass=stdout | rwcut --f 1-8
```

## Identify the 10 hosts that sent the most data during a capture:

```text
rwstats cap1.silk --fields=sip --top --bytes --count 10
rwfilter --type=all --proto=0-255 --start-date=2019/05/01 --end-date=2019/05/04 --pass=stdout | rwstats --fields=sip --values=bytes --count=10
```

How many IPv6 net flow records were recorded in the backbone that were NOT sent to ff02::1:

```text
rwfilter backbone.silk --ip-version=6 --not-daddress=ff02::01 --print-stat
```

