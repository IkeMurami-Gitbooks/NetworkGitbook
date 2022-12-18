# HTTP / Windows

[HTTP](https://en.wikipedia.org/wiki/Hypertext\_Transfer\_Protocol) (Hypertext Transfer Protocol) is probably the most famous application protocol out there, since it is the protocol of the web. But apart from its major role in Internet, is also commonly used in Active Directory.

HTTP is used as transport protocol by many other application protocols that are present in a Active Directory domain like [WinRM](https://zer1t0.gitlab.io/posts/attacking\_ad/#winrm) (and thus [Powershell Remoting](https://zer1t0.gitlab.io/posts/attacking\_ad/#powershell-remoting)), [RPC](https://zer1t0.gitlab.io/posts/attacking\_ad/#rpc) or [ADWS](https://zer1t0.gitlab.io/posts/attacking\_ad/#adws) (Active Directory Web Services).

In order to be fully integrated with Active Directory, HTTP supports authentication with both NTLM and Kerberos. This is important from a security perspective since it implies that HTTP connections are susceptible of suffering from Kerberos Delegation or [NTLM Relay](https://en.hackndo.com/ntlm-relay/#what-can-be-relayed) attacks.

In the case of NTLM relay is specially important to note that HTTP connections don't required signing, so are very susceptible to NTLM cross relay attacks. In fact, there are many attacks like the [PrivExchange](https://dirkjanm.io/abusing-exchange-one-api-call-away-from-domain-admin/) or some [Kerberos RBCD computer takeover](https://shenaniganslabs.io/2019/01/28/Wagging-the-Dog.html#case-study-1-mssql-rcelpe) that rely in NTLM relay from HTTP to LDAP. If you able to coerce a computer to perform an HTTP request using the computer domain account with NTLM authentication , then you can compromise the computer with a [little of Kerberos RBCD magic](https://shenaniganslabs.io/2019/01/28/Wagging-the-Dog.html#case-study-2-windows-1020162019-lpe).

Related to HTTP, in Windows machines you can install the [IIS](https://www.iis.net/) web server, that is the basis for some technologies like [WebDAV](https://en.wikipedia.org/wiki/WebDAV) or PSWA (Powershell Web Access), that can be enabled in the `/pswa` endpoint.

Moreover, you can create a SOCKS proxy over HTTP in a IIS installation by using [pivotnacci](https://github.com/blackarrowsec/pivotnacci).
