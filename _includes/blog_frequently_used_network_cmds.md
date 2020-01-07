# Frequently Used Network Commands
Reference:

[8 Linux Nslookup Commands](https://www.tecmint.com/8-linux-nslookup-commands-to-troubleshoot-dns-domain-name-server/)

## NSLookUp
Nslookup is a command-line tool to obtain domain names or IP addresses mapping, or other specific DNS Records. The name "nslookup" means "name server lookup".

### Find the A record of а domain
The ip address for **yahoo.com** is **98.137.246.8**. You are querying the DNS server **2607:f720:100:100::230**. If you can't load a website by typing its ip, look at [this article](https://superuser.com/questions/1361680/cant-access-website-by-ip-but-i-can-access-by-fully-qualified-domain)
```bash
$ nslookup -type=a yahoo.com
Server:		2607:f720:100:100::230
Address:	2607:f720:100:100::230#53

Non-authoritative answer:
Name:	yahoo.com
Address: 98.137.246.8
Name:	yahoo.com
Address: 72.30.35.10
Name:	yahoo.com
Address: 98.138.219.231
Name:	yahoo.com
Address: 98.138.219.232
Name:	yahoo.com
Address: 72.30.35.9
Name:	yahoo.com
Address: 98.137.246.7
```

### Reverse DNS Lookup
Reverse DNS lookups for IPv4 addresses use the special domain **in-addr.arpa**.
```bash
$ nslookup 98.137.246.8
Server:		2607:f720:100:100::230
Address:	2607:f720:100:100::230#53

Non-authoritative answer:
8.246.137.98.in-addr.arpa	name = media-router-fp2.prod1.media.vip.gq1.yahoo.com.

Authoritative answers can be found from:
246.137.98.in-addr.arpa	nameserver = ns2.yahoo.com.
246.137.98.in-addr.arpa	nameserver = ns3.yahoo.com.
246.137.98.in-addr.arpa	nameserver = ns1.yahoo.com.
246.137.98.in-addr.arpa	nameserver = ns5.yahoo.com.
246.137.98.in-addr.arpa	nameserver = ns4.yahoo.com.
ns1.yahoo.com	has AAAA address 2001:4998:130::1001
ns2.yahoo.com	has AAAA address 2001:4998:140::1002
ns3.yahoo.com	has AAAA address 2406:8600:f03f:1f8::1003
ns5.yahoo.com	has AAAA address 2406:2000:ff60::53
ns1.yahoo.com	internet address = 68.180.131.16
ns2.yahoo.com	internet address = 68.142.255.16
ns3.yahoo.com	internet address = 27.123.42.42
ns4.yahoo.com	internet address = 98.138.11.157
ns5.yahoo.com	internet address = 202.165.97.53
```


### Find the NS records of a domain
The name server address for **yahoo.com** is **ns3.yahoo.com**. You are querying the DNS server **2607:f720:100:100::230**.
```bash
$ nslookup -type=ns yahoo.com
Server:		2607:f720:100:100::230
Address:	2607:f720:100:100::230#53

Non-authoritative answer:
yahoo.com	nameserver = ns3.yahoo.com.
yahoo.com	nameserver = ns5.yahoo.com.
yahoo.com	nameserver = ns4.yahoo.com.
yahoo.com	nameserver = ns1.yahoo.com.
yahoo.com	nameserver = ns2.yahoo.com.

Authoritative answers can be found from:
ns1.yahoo.com	has AAAA address 2001:4998:130::1001
ns2.yahoo.com	has AAAA address 2001:4998:140::1002
ns3.yahoo.com	has AAAA address 2406:8600:f03f:1f8::1003
ns5.yahoo.com	has AAAA address 2406:2000:ff60::53
ns1.yahoo.com	internet address = 68.180.131.16
ns2.yahoo.com	internet address = 68.142.255.16
ns3.yahoo.com	internet address = 27.123.42.42
ns4.yahoo.com	internet address = 98.138.11.157
ns5.yahoo.com	internet address = 202.165.97.53
```

## Traceroute
traceroute command prints the route that a packet takes to reach the host. This command is useful when you want to know about the route and about all the hops that a packet takes. If seeing **\*\*\*** in traceroute, it means that a packet is not acknowledged within the expected timeout.
### Basic Usage
```
$ traceroute googl.com
traceroute to googl.com (172.217.11.164), 64 hops max, 52 byte packets
 1  100.81.32.2 (100.81.32.2)  2.545 ms  4.359 ms  2.308 ms
 2  preuss-6509-nodeb-dist-6509-ge.ucsd.edu (132.239.255.114)  2.099 ms  2.057 ms  2.194 ms
 3  mx0--nodem-core-30ge.ucsd.edu (132.239.254.162)  1.996 ms  2.933 ms  2.057 ms
 4  dc-sdg-agg4--ucsd-100ge.cenic.net (137.164.23.176)  2.567 ms  3.171 ms  3.231 ms
 5  tus-agg8--sdg-agg4-3x100ge.cenic.net (137.164.11.50)  5.169 ms  5.294 ms  5.395 ms
 6  lax-agg6--tus-agg8-100g-3.cenic.net (137.164.11.60)  4.850 ms
    dc-lax-agg6--tus-agg8-100ge-1.cenic.net (137.164.11.22)  11.178 ms  4.658 ms
 7  74.125.49.165 (74.125.49.165)  4.803 ms  5.075 ms  5.444 ms
 8  108.170.247.161 (108.170.247.161)  6.112 ms  5.003 ms  5.069 ms
 9  108.170.225.75 (108.170.225.75)  4.707 ms
    108.170.225.69 (108.170.225.69)  4.704 ms
    108.170.225.75 (108.170.225.75)  4.988 ms
10  lax28s15-in-f4.1e100.net (172.217.11.164)  4.700 ms  4.645 ms  4.568 ms
```

## Whois
WHOIS returns information about the registered Domain Names, an IP address block, Name Servers and a much wider range of information services.
### How to Find IP Address Information
```
$ whois 216.58.206.46

#
# ARIN WHOIS data and services are subject to the Terms of Use
# available at: https://www.arin.net/resources/registry/whois/tou/
#
# If you see inaccuracies in the results, please report at
# https://www.arin.net/resources/registry/whois/inaccuracy_reporting/
#
# Copyright 1997-2020, American Registry for Internet Numbers, Ltd.
#


NetRange:       216.58.192.0 - 216.58.223.255
CIDR:           216.58.192.0/19
NetName:        GOOGLE
NetHandle:      NET-216-58-192-0-1
Parent:         NET216 (NET-216-0-0-0-0)
NetType:        Direct Allocation
OriginAS:       AS15169
Organization:   Google LLC (GOGL)
RegDate:        2012-01-27
Updated:        2012-01-27
Ref:            https://rdap.arin.net/registry/ip/216.58.192.0



OrgName:        Google LLC
OrgId:          GOGL
Address:        1600 Amphitheatre Parkway
City:           Mountain View
StateProv:      CA
PostalCode:     94043
Country:        US
RegDate:        2000-03-30
Updated:        2019-10-31
Comment:        Please note that the recommended way to file abuse complaints are located in the following links. 
Comment:        
Comment:        To report abuse and illegal activity: https://www.google.com/contact/
Comment:        
Comment:        For legal requests: http://support.google.com/legal 
Comment:        
Comment:        Regards, 
Comment:        The Google Team
Ref:            https://rdap.arin.net/registry/entity/GOGL


OrgTechHandle: ZG39-ARIN
OrgTechName:   Google LLC
OrgTechPhone:  +1-650-253-0000 
OrgTechEmail:  arin-contact@google.com
OrgTechRef:    https://rdap.arin.net/registry/entity/ZG39-ARIN

OrgAbuseHandle: ABUSE5250-ARIN
OrgAbuseName:   Abuse
OrgAbusePhone:  +1-650-253-0000 
OrgAbuseEmail:  network-abuse@google.com
OrgAbuseRef:    https://rdap.arin.net/registry/entity/ABUSE5250-ARIN
```

### How to Find Domain Information
```
~$ whois google.com
   Domain Name: GOOGLE.COM
   Registry Domain ID: 2138514_DOMAIN_COM-VRSN
   Registrar WHOIS Server: whois.markmonitor.com
   Registrar URL: http://www.markmonitor.com
   Updated Date: 2019-09-09T15:39:04Z
   Creation Date: 1997-09-15T04:00:00Z
   Registry Expiry Date: 2028-09-14T04:00:00Z
   Registrar: MarkMonitor Inc.
   Registrar IANA ID: 292
   Registrar Abuse Contact Email: abusecomplaints@markmonitor.com
   Registrar Abuse Contact Phone: +1.2083895740
   Domain Status: clientDeleteProhibited https://icann.org/epp#clientDeleteProhibited
   Domain Status: clientTransferProhibited https://icann.org/epp#clientTransferProhibited
   Domain Status: clientUpdateProhibited https://icann.org/epp#clientUpdateProhibited
   Domain Status: serverDeleteProhibited https://icann.org/epp#serverDeleteProhibited
   Domain Status: serverTransferProhibited https://icann.org/epp#serverTransferProhibited
   Domain Status: serverUpdateProhibited https://icann.org/epp#serverUpdateProhibited
   Name Server: NS1.GOOGLE.COM
   Name Server: NS2.GOOGLE.COM
   Name Server: NS3.GOOGLE.COM
   Name Server: NS4.GOOGLE.COM
   DNSSEC: unsigned
   URL of the ICANN Whois Inaccuracy Complaint Form: https://www.icann.org/wicf/
   ...
   ...
```
