# DNS

Link: [https://zer1t0.gitlab.io/posts/attacking\_ad/#dns](https://zer1t0.gitlab.io/posts/attacking\_ad/#dns)

DNS (Domain Name System) is a system that defines hierarchical names for computer, services and other resources of the network. The DNS protocol is a client/server protocol in which the server listens on ports 53/UDP and 53/TCP.

```
          .---
 DNS ---> | 53/UDP|TCP
          '---
```

DNS in mainly used to resolve the DNS name of a computer to its IP address.

```
    client                     DNS server
    .---.   A hackliza.gal?     .---.
   /   /| ------------------>  /   /|
  .---. |                     .---. |
  |   | '   185.199.111.153   |   | '
  |   |/  <------------------ |   |/ 
  '---'                       '---'
```

## DNS exfiltration

This way, the DNS protocol can be an excellent ally as [exfiltration mechanism](https://blogs.akamai.com/2017/09/introduction-to-dns-data-exfiltration.html). There are certain situations where a server is in an isolated network and has no access to the internet, but it is allowed to perform DNS queries in order to work properly. If the local DNS server is misconfigured and performs recursive DNS requests to other DNS servers in the internet, this can be abused to bypass firewall rules and sending data to the outside.

```
                             local recursive           fake.com authoritative
    client                     DNS server                   DNS server
    .---.                        .---.                        .---.         
   /   /|  websvr01.fake.com?   /   /|  websvr01.fake.com?   /   /|
  .---. | --------local------> .---. | ------internet-----> .---. |
  |   | '                      |   | '                      |   | '
  |   |/    40.113.200.201     |   |/    40.113.200.201     |   |/
  '---'   <------------------- '---'   <------------------- '---'
```

For example, in case of possesing a DNS server for the domain `fake.com`, all the DNS queries for `fake.com` and its subdomains will reach our server. For example, if we want to exfiltrate the name of the isolated server, we can use it as subdomain and querying `websvr01.fake.com`. This query should pass through the local DNS server and reach our DNS server in the internet. To take advantage of this type of technique we can use a tool like [iodine](https://github.com/yarrick/iodine) or [dnscat2](https://github.com/iagox86/dnscat2).

## Fake DNS Server

Moreover, since DNS is so important to manage the resources of a network, it could be very useful to setup a fake DNS server, by using techniques like a rogue DHCP server]].

With a fake DNS server we could redirect the requests of clients to a machines of our control in order to recolect NetNTLM hashes or just sniffing the network waiting for sensitive information that travels unprotected. We can use tools like [dnschef](irc:https://github.com/iphelix/dnschef) or [responder.py](https://github.com/lgandx/Responder) to create a fake DNS server.

```bash
$ dnschef -i 192.168.100.44 --fakeip 192.168.100.44
          _                _          __  
         | | version 0.4  | |        / _| 
       __| |_ __  ___  ___| |__   ___| |_ 
      / _` | '_ \/ __|/ __| '_ \ / _ \  _|
     | (_| | | | \__ \ (__| | | |  __/ |  
      \__,_|_| |_|___/\___|_| |_|\___|_|  
                   iphelix@thesprawl.org  

(12:29:51) [*] DNSChef started on interface: 192.168.100.44
(12:29:51) [*] Using the following nameservers: 8.8.8.8
(12:29:51) [*] Cooking all A replies to point to 192.168.100.44
(12:38:32) [*] 192.168.100.7: proxying the response of type 'PTR' for 44.100.168.192.in-addr.arpa
(12:38:32) [*] 192.168.100.7: cooking the response of type 'A' for aaa.contoso.local to 192.168.100.44
(12:38:32) [*] 192.168.100.7: proxying the response of type 'AAAA' for aaa.contoso.local
```

## DNS Zone Transfer

Other interesting thing related with the zones management are the zone transfers. Zone transfers are used to replicate all the records of a DNS server into another DNS server, thus allow to keep updated both servers. However, in some cases a DNS server is misconfigured and allows to anyone to perform zone transfers.

In case of Active Directory, DNS zone transfers are not required to replicate DNS records between DCs (which are usually the DNS servers). However, they can be enabled in order to allow other DNS servers to replicate the DNS information.

The zone transfers can be configured by zone and DC, which means that maybe just one DC allows to perform the zone transfer whereas the rest of DCs refuse the zone transfer. In case of a misconfigured DC, anyone could perform zone transfers, thus recolecting all the DNS information without require any credentials. The followings commands can be used to carry out a DNS zone transfer:

* Linux: `dig axfr <DNSDomainName> @<DCAddress>`
* Windows: interactive `nslookup` `ls -d <DNSDomainName>`

Zone transfer from DC with dig:

```
root@debian10:~# dig axfr contoso.local @dc01.contoso.local

; <<>> DiG 9.11.5-P4-5.1+deb10u2-Debian <<>> axfr contoso.local @dc01.contoso.local
;; global options: +cmd
contoso.local.		3600	IN	SOA	dc01.contoso.local. hostmaster.contoso.local. 156 900 600 86400 3600
contoso.local.		600	IN	A	192.168.100.3
contoso.local.		600	IN	A	192.168.100.2
contoso.local.		3600	IN	NS	dc01.contoso.local.
contoso.local.		3600	IN	NS	dc02.contoso.local.
_gc._tcp.Default-First-Site-Name._sites.contoso.local. 600 IN SRV 0 100 3268 dc02.contoso.local.
_gc._tcp.Default-First-Site-Name._sites.contoso.local. 600 IN SRV 0 100 3268 dc01.contoso.local.
_kerberos._tcp.Default-First-Site-Name._sites.contoso.local. 600 IN SRV	0 100 88 dc02.contoso.local.
......................stripped output..................
```

Zone transfer from DC with nslookup:

```
PS C:\> nslookup
Default Server:  UnKnown
Address:  192.168.100.2

> server dc01.contoso.local
Default Server:  dc01.contoso.local
Addresses:  192.168.100.2

> ls -d contoso.local
[UnKnown]
 contoso.local.                 SOA    dc01.contoso.local hostmaster.contoso.local. (159 900 600 86400 3600)
 contoso.local.                 A      192.168.100.3
 contoso.local.                 A      192.168.100.2
 contoso.local.                 NS     dc02.contoso.local
 contoso.local.                 NS     dc01.contoso.local
 _gc._tcp.Default-First-Site-Name._sites SRV    priority=0, weight=100, port=3268, dc02.contoso.local
 _gc._tcp.Default-First-Site-Name._sites SRV    priority=0, weight=100, port=3268, dc01.contoso.local
 _kerberos._tcp.Default-First-Site-Name._sites SRV    priority=0, weight=100, port=88, dc02.contoso.local
......................stripped output..................
```

## Dump DNS Records

Furthermore, even if zone transfers are not allowed, due to DNS records are stored in the Active Directory database, they can be read by using LDAP. Thus, any domain user can use the LDAP protocol to dump all the DNS records. For this purpose, the [adidnsdump](https://github.com/dirkjanm/adidnsdump) tool can be used (saves the results in `records.csv`) or the [dns-dump.ps1](https://github.com/mmessano/PowerShell/blob/master/dns-dump.ps1) script.

```
root@debian10:~# adidnsdump -u contoso\\Anakin contoso.local
Password: 
[-] Connecting to host...
[-] Binding to host
[+] Bind OK
[-] Querying zone for records
[+] Found 37 records

root@debian10:~# head records.csv 
type,name,value
A,WS02-7,192.168.100.7
A,ws01-10,192.168.100.6
A,WIN-LBB9AO5FA13,192.168.100.6
A,win-4l1775e9t3u,192.168.100.2
A,ForestDnsZones,192.168.100.3
A,ForestDnsZones,192.168.122.254
A,ForestDnsZones,192.168.100.2
A,ForestDnsZones,192.168.122.111
A,DomainDnsZones,192.168.100.3
```

## ADIDNS

Therefore, DNS is a pretty useful protocol, and of course, is used in Active Directory. The Active Directory implementation is ADIDNS (Active Directory Integrated DNS), where the role of DNS servers is mainly assumed by the DCs (Domain Controllers), since their databases contains the DNS names of the computers in the domain and the rest of DNS records. In Active Directory, the DNS is the preferred method to resolve names. The order of preference of resolving protocols is:

1. DNS
2. mDNS
3. LLMNR
4. NBNS

ADIDNS works similar to any other DNS implementations, but includes some special characteristics.

The main difference with other implementations is that DNS records are maintained in the Active Directory database, instead of using a text file. In this way DNS records are integrated like any other object, and take the advantage of Active Directory Domain Services such as automatic replication without requiring DNS zone transfers.

There DNS records can be stored in one of the following locations in the database:

* DomainDnsZones partition: This partition is replicated in the DCs of the domain. Records can be accessed through LDAP in the route `CN=MicrosoftDNS,DC=DomainDnsZones,DC=<domainpart>,DC=<domainpart>`.
* ForestDnsZones partition: This partition is replicated in all the DCs in the forest. Records can be accessed through LDAP in the route `CN=MicrosoftDNS,DC=DomainDnsZones,DC=<domainpart>,DC=<domainpart>`.
* Domain partition: In legacy systems, the DNS records where stored in this partition that it is replicated in the DCs of the domain. Records can be accessed through LDAP in the route `CN=MicrosoftDNS,CN=System,DC=<domainpart>,DC=<domainpart>`.

For example, to access the DomainDnsZones partition through LDAP in `contoso.local`, the route would be `CN=MicrosoftDNS,DC=DomainDnsZones,DC=contoso,DC=local`.

As one of the special characteristics, apart from the usual DNS records, ADIDNS maintains [special SRV records](https://petri.com/active\_directory\_srv\_records) that allows to find certain resources in the network. This allows us to identify the DCs by querying to one of the following SRV records:

* `_gc._tcp.<DNSSDomainName>`
* `_kerberos._tcp.<DNSSDomainName>`
* `_kerberos._udp.<DNSSDomainName>`
* `_kpasswd._tcp.<DNSSDomainName>`
* `_kpasswd._udp.<DNSSDomainName>`
* `_ldap._tcp.<DNSSDomainName>`
* `_ldap._tcp.dc._msdcs.<DNSDomainName>`

These records points to servers that provides Global Catalog (\_gc), Kerberos (\_kerberos and \_kpasswd) and LDAP (\_ldap) services in Active Directory, which are the Domain Controllers.

For example, you can get the DCs of `contoso.local` from Windows with `nslookup -q=srv _ldap._tcp.dc._msdcs.contoso.local` and from Linux with `dig SRV _ldap._tcp.dc.contoso.local`.

DNS query to identify DCs with nslookup:

```
PS C:\> nslookup -q=srv _ldap._tcp.contoso.local
Server:  ip6-localhost
Address:  ::1

_ldap._tcp.contoso.local        SRV service location:
          priority       = 0
          weight         = 100
          port           = 389
          svr hostname   = dc01.contoso.local
_ldap._tcp.contoso.local        SRV service location:
          priority       = 0
          weight         = 100
          port           = 389
          svr hostname   = dc02.contoso.local
dc01.contoso.local      internet address = 192.168.100.2
dc02.contoso.local      internet address = 192.168.100.6
```

It is also posible to get the IPs of DCs by resolving the domain name `<DNSDomainName>`. Additionally, the primary DC can be discovered by querying to `_ldap._tcp.pdc._msdcs.<DNSDomainName>`.

## DNS dynamic updates



Other interesting mechanism in DNS are the [dynamic updates](https://www.ietf.org/rfc/rfc2136.txt). Dynamic updates allows clients to create/modify/delete DNS records. In Active Directory by default only secured dynamic updates are allowed. This means that DNS records are protected by [ACLs](https://zer1t0.gitlab.io/posts/attacking\_ad/#acls) and only authorized users can modify then.

By default any user can create a new DNS record (the user becomes its owner) and only the owner can update or delete the DNS record. Therefore, access is denied if an user wants to create a DNS record that already exists. To create new DNS records through DNS dynamic updates the script [Invoke-DNSUpdate](https://github.com/Kevin-Robertson/Powermad#invoke-dnsupdate).

DNS update with Invoke-DNSUpdate:

```powershell
PS C:\> Invoke-DNSUpdate -DNSType A -DNSName test -DNSData 192.168.100.100 -Verbose
VERBOSE: [+] Domain Controller = dc01.contoso.local
VERBOSE: [+] Domain = contoso.local
VERBOSE: [+] Kerberos Realm = contoso.local
VERBOSE: [+] DNS Zone = contoso.local
VERBOSE: [+] TKEY name 676-ms-7.1-0967.05293487-9821-11e7-4051-000c296694e0
VERBOSE: [+] Kerberos preauthentication successful
VERBOSE: [+] Kerberos TKEY query successful
[+] DNS update successful
PS C:\> nslookup test
Server:  UnKnown
Address:  192.168.100.2

Name:    test.contoso.local
Address:  192.168.100.100
```

If you are wondering how DNS can allow authenticated requests, this is achieved by using the [TSIG](https://www.ietf.org/rfc/rfc2845.txt) (Transaction Signature) protocol, which requires the messages to be signed with a shared key between server and the client. In the case of Active Directory, this shared key is obtained by using the [Kerberos](https://zer1t0.gitlab.io/posts/attacking\_ad/#kerberos) protocol.

Back to the dynamic updates functionality, one interesting record to register is the wildcard record, `*`. The wildcard record is used to specify a default IP address that is used to resolve those queries that doesn't match any other record. Pretty useful to [perform PitM attacks](https://blog.netspi.com/exploiting-adidns/) if it is used to point to a computer controlled by us. However, dynamic updates doesn't allow to register a wildcard record due to errors in the character handling.

Fortunately, since DNS records are stored in the Active Directory database, they can be created/modify/deleted by using LDAP. We can play with DNS records via LDAP with [Powermad](https://github.com/Kevin-Robertson/Powermad) and [dnstool.py](https://github.com/dirkjanm/krbrelayx#dnstoolpy). This technique can also be employed to recolect NetNTLM hashes with [Inveigh](https://github.com/Kevin-Robertson/Inveigh). However, it is important to remember to delete the registered DNS records when finished in order to avoid causing network problems.

However, there are certain [DNS names that are protected](https://www.netspi.com/blog/technical/network-penetration-testing/adidns-revisited/) by the DNS Global Query Block List (GQBL) from being resolved even if you add a DNS record. By default those are `wpad` and `isatap`.

Get DNS Global Query Block List:

```powershell
PS C:\> Get-DnsServerGlobalQueryBlockList

Enable : True
List   : {wpad, isatap}
```

For more information about dynamic updates and the related attacks, you can check the following resources:

* [Beyond LLMNR/NBNS Spoofing - Exploiting Active Directory-Integrated DNS](https://blog.netspi.com/exploiting-adidns/)
* [ADIDNS Revisited - WPAD, GQBL, and More](https://blog.netspi.com/adidns-revisited/)

## Tools

DNS over HTTPS (DOH)\
[https://github.com/curl/curl/wiki/DNS-over-HTTPS](https://github.com/curl/curl/wiki/DNS-over-HTTPS)

DNS Proxy - Нужен чтоб видеть запросы к DNS серверу:\
Fork DNSChef: [https://github.com/dinosec/dnssecchef.git](https://github.com/dinosec/dnssecchef.git)\
DNSChef: [https://github.com/iphelix/dnschef.git](https://github.com/iphelix/dnschef.git)

DNS Rebinding tools (Go) – поднять свой DNS со своими правилами (онлайн-версия — 1u.ms) [https://github.com/neex/1u.ms](https://github.com/neex/1u.ms)

