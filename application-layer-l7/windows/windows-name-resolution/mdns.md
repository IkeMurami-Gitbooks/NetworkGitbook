# mDNS

[mDNS](https://en.wikipedia.org/wiki/Multicast\_DNS) (multicast DNS) is a descentralized application protocol, similar to LLMNR, based on DNS that allows to resolve names in local networks, which means that its packets are not forwarded by routers and are only transmited in their network segment. It is included in Windows 10, and is the second preferred method to resolve names after [DNS](https://zer1t0.gitlab.io/posts/attacking\_ad/#dns).

1. DNS
2. mDNS
3. LLMNR
4. NBNS

In a Windows network, the computers are listening into the port 5353/UDP and to resolve a name, the client sends a mDNS query to the [multicast address](https://en.wikipedia.org/wiki/Multicast\_address) 224.0.0.251 (FF02::FB in IPv6). The queries follow the DNS format and can be use to ask not only for names, but also any other question supported by DNS.

```
            .---
 mDNS ---> | 5353/UDP
            '---
```

The common case is use mDNS to resolve names in local link by sending A DNS queries. In this case, the computer that has the queried name should response by sending the answer to the multicast address 224.0.0.251, this way, any computer in the network can obtain the answer and cache it. But, of course, the query can be responded by anyone, even an attacker to perform a MITM attack. This is one of the tactics used by [responder.py](https://github.com/lgandx/Responder) and [Inveigh](https://github.com/Kevin-Robertson/Inveigh) to recollect NetNTLM hashes in networks with Windows machines.
