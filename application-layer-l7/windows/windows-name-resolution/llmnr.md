# LLMNR

## About

![](<../../../.gitbook/assets/изображение (5).png>)

[LLMNR](https://en.wikipedia.org/wiki/Link-Local\_Multicast\_Name\_Resolution) (Link-Local Multicast Name Resolution) is a descentralized application protocol similar to DNS that allows to resolve hostnames in the same local network, which means that its packets are not forwarded by routers and are only transmited in their network segment. It is included in Windows since Windows Vista, and is the third preferred method to resolve names. The order of preference is the following:

1. DNS
2. mDNS
3. LLMNR
4. NBNS

In a Windows network, the computers are listening into the port 5355/UDP and to resolve a name, the client sends a LLMNR query to the [multicast address](https://en.wikipedia.org/wiki/Multicast\_address) 224.0.0.252 (FF02:0:0:0:0:0:1:3 in IPv6). The queries follow the DNS format and can be use to ask not only for names, but also any other question supported by DNS.

```
            .---
 LLMNR ---> | 5355/UDP
            '---
```

The common case is use LLMNR to resolve names in local link by sending A DNS queries. In this case, the computer that has the queried name should response. But, of course, the query can be responded by anyone, even by an attacker to perform a PitM attack. This is one of the tactics used by [responder.py](https://github.com/lgandx/Responder) and [Inveigh](https://github.com/Kevin-Robertson/Inveigh) to recollect NTLM hashes in networks with Windows machines (LLMNR/NBT-NS Poisoning).
