# WinRM

Apart from RPC, there is also possible to use WinRM (Windows Remote Management) to communicate and execute operations in other machines. WinRM is the Microsoft implementation of the [WS-Management](https://en.wikipedia.org/wiki/WS-Management) (Web Services-Management) specification that defines a protocol for managing computers by using SOAP over HTTP.

WinRM uses some extensions that are defined in [WSMAN](https://docs.microsoft.com/en-us/openspecs/windows\_protocols/ms-wsman/70912fec-c815-44ef-97c7-fc7f2ec7cda5) and [WSMV](https://docs.microsoft.com/en-us/openspecs/windows\_protocols/ms-wsmv/055dc36b-db2a-41ae-a47b-82cbfa0b4a92) for accessing CIM objects in remote machines. These CIM objects are like an update to WMI objects. You can access to CIM objects in local and remote machines with the [CIM Cmdlets](https://devblogs.microsoft.com/powershell/introduction-to-cim-cmdlets/) such as [Get-CimInstance](https://docs.microsoft.com/en-us/powershell/module/cimcmdlets/get-ciminstance?view=powershell-7.1). Additionally, you can use also use [winrs](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/winrs) to perform actions in remote computers by using WinRM.

By default, WinRM service listen on port 5985 for HTTP connections and port 5986 for HTTPS connections. By default, HTTP is used, since the WinRM messages are encrypted in a top layer. However, WinRM can be configured to [use the regular HTTP ports](https://adamtheautomator.com/winrm-port/#h-setting-winrm-compatibility-ports) 80 and 443 for HTTP and HTTPS connections respectively.

## Powershell Remoting

One great utility to manage systems is Powershell remoting, that allows the client to establish a Powershell session on remote computers and perform all kind of tasks with [Powershell](https://docs.microsoft.com/en-us/powershell/). By default, Powershell remoting is enabled by default in Windows server versions (not client like Windows 10) [since Windows Server 2012 R2](https://www.dtonias.com/enable-powershell-remoting-check-enabled/).

```powershell
PS C:\> $pw = ConvertTo-SecureString -AsPlainText -Force -String "Admin1234!"
PS C:\> $cred = New-Object -typename System.Management.Automation.PSCredential -argumentlist "contoso\Administrator",$pw
PS C:\> 
PS C:\> $session = New-PSSession -ComputerName dc01 -Credential $cred
PS C:\> Invoke-Command -Session $session -ScriptBlock {hostname}
dc01
PS C:\> Enter-PSSession -Session $session
[dc01]: PS C:\Users\Administrator\Documents>
```

Originally, Powershell remoting was built on top of [WinRM](https://zer1t0.gitlab.io/posts/attacking\_ad/#winrm) protocol. However, it was expected to be used in Linux machines so it also supports [SSH](https://zer1t0.gitlab.io/posts/attacking\_ad/#ssh) as transport protocol.

In order to use Powershell remoting, you can use several [PSSession CmdLets to use to execute commands on remote machines](https://www.netspi.com/blog/technical/network-penetration-testing/powershell-remoting-cheatsheet/). Also, from Linux you can [install Powershell](https://docs.microsoft.com/en-us/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-7.1) or using a tool like [evil-winrm](https://github.com/Hackplayers/evil-winrm).

Apart from being useful for [lateral movement](https://www.ired.team/offensive-security/lateral-movement/t1028-winrm-for-lateral-movement), you could also use [JEA endpoints](https://docs.microsoft.com/en-us/powershell/scripting/learn/remoting/jea/overview?view=powershell-7.1) (only available over WinRM) as a [persistence mechanism](https://www.labofapenetrationtester.com/2019/08/race.html).

However, be careful in a pentest since [Powershell has many logging features](https://es.slideshare.net/nikhil\_mittal/hacked-pray-that-the-attacker-used-powershell).

**Trusted Hosts**

Apart from being enabled to use it, Powershell required also that the TrustedHost variable is correctly set **in the client**.

By default, Powershell remoting allows you to connect to all machines in the domain, by using Kerberos. However, in case you want to connect a machine of a different domain, you need to add that IP to the TrustedHost value (or use '\*' for any machine). In that case, you have to **configure TrustedHost in the client**, not in the server (as you may think since from a security perspective would be the logical idea).

```powershell
PS C:\> Set-Item wsman:localhost\client\TrustedHosts -Value * -Force
```
