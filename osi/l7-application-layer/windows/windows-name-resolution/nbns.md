# NBNS

Link: очень подробно про NetBIOS и NBNS: [https://zer1t0.gitlab.io/posts/attacking\_ad/#netbios](https://zer1t0.gitlab.io/posts/attacking\_ad/#netbios)

## About

NetBIOS Name Service — устаревший протокол, но где-то еще работает

Работает над UDP (обычно)

src/dst port 137

![](<../../../../.gitbook/assets/изображение (6).png>)

Посмотреть записи на локальной машине:

```
C:\> nbtstat -n

Ethernet 2:
Node IpAddress: [192.168.100.10] Scope Id: []

                NetBIOS Local Name Table

       Name               Type         Status
    ---------------------------------------------
    WS01-10        <20>  UNIQUE      Registered
    WS01-10        <00>  UNIQUE      Registered
    CONTOSO        <00>  GROUP       Registered
```

It must be noted that, in case of a broadcast request, any computer can respond to the query, so it allows to an attacker to impersonate the real computer. This is one of the tactics followed by [responder.py](https://github.com/lgandx/Responder) and [Inveigh](https://github.com/Kevin-Robertson/Inveigh) to collect NTLM hashes.

Also, it must taked into account that NBNS is not used if any other protocol can resolve the name request. The order of preference is the following:

1. DNS
2. mDNS
3. LLMNR
4. NBNS

Furthermore, it is possible to use this capability to perform a NetBIOS scan in a network and discover machines and services. This can be accomplished with [nbtscan](http://www.unixwiz.net/tools/nbtscan.html) or nmap script [nbtstat.nse](https://nmap.org/nsedoc/scripts/nbstat.html), from both Windows or Linux.

```
root@debian10:~# nbtscan 192.168.100.0/24
192.168.100.2   CONTOSO\DC01                    SHARING DC
192.168.100.7   CONTOSO\WS02-7                  SHARING
*timeout (normal end of scan)
```
