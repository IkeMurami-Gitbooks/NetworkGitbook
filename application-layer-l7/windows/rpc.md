# RPC

[RPC](https://en.wikipedia.org/wiki/Remote\_procedure\_call) (Remote Procedure Call) is a protocol that allows programs from different machines communicate between them by calling functions over the network. Microsoft have developed a RPC protocol called [MSRPC](https://en.wikipedia.org/wiki/Microsoft\_RPC), that is a modified version of [DCE/RPC](https://en.wikipedia.org/wiki/DCE/RPC) with some extensions (defined in [RPCE](https://docs.microsoft.com/en-us/openspecs/windows\_protocols/ms-rpce/290c38b1-92fe-4229-91e6-4fc376610c15)).

MSRPC can use different [protocols for transport](https://docs.microsoft.com/en-us/openspecs/windows\_protocols/ms-rpce/472083a9-56f1-4d81-a208-d18aef68c101), like:

* TCP, by using the port 135 for the Endpoint Mapper and ports from 49152 to 65535 as endpoints
* SMB by using the named pipes
* NetBIOS
* HTTP, by using the port 593 for the Endpoint Mapper and ports from 49152 to 65535 as endpoints

Depending on the interface, different transport protocols can be used. You can use the impacket [rpcdump.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/rpcdump.py) and [rpcmap.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/rpcmap.py) utilities to discover the RPC endpoints (and their protocols) that can be used for connecting to a given service in a remote machine. Additionally, you can explore the RPC endpoints in your local machine by using [RpcView](https://www.rpcview.org).

```bash
$ python rpcdump.py 'contoso.local/Han:Solo1234!@192.168.100.2' | grep LSAT -A 20 | grep -v ncalrpc
Protocol: [MS-LSAT]: Local Security Authority (Translation Methods) Remote 
Provider: lsasrv.dll 
UUID    : 12345778-1234-ABCD-EF00-0123456789AB v0.0 
Bindings: 
          ncacn_np:\\DC01[\pipe\lsass]
          ncacn_ip_tcp:192.168.100.2[49667]
          ncacn_http:192.168.100.2[49669]
          ncacn_np:\\DC01[\pipe\cb4e7232b43a99b8]
```

To give you an idea of what can be done with RPC, here are the descriptions of some of the most used interfaces. I have divided the interfaces by transport protocols in order to allow you to know what can be accomplished when different ports of the machine are open.

