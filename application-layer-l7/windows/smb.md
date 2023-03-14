# SMB

## Shares

Shares are like folders that a machine shares in order to be accessed by other computers/users in the network. You can list the shares by using the [net view](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh875576\(v=ws.11\)) command, the [Get-SmbShare](https://docs.microsoft.com/en-us/powershell/module/smbshare/get-smbshare?view=windowsserver2019-ps) Powershell Cmdlet, or [smbclient.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/smbclient.py).

```
C:\> net view \\dc01.contoso.local /all
Shared resources at \\dc01.contoso.local

Share name  Type  Used as  Comment

-------------------------------------------------------------------------------
ADMIN$      Disk           Remote Admin
C$          Disk           Default share
IPC$        IPC            Remote IPC
NETLOGON    Disk           Logon server share
SYSVOL      Disk           Logon server share
The command completed successfully.
```

For accessing a share, you can use the a UNC path like `\\dc01.contoso.local\SYSVOL\` or map the remote share to a local device by using [net use](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/gg651155\(v=ws.11\)) command.

To refer to the target computer in the UNC path, you can use its dns name or its NetBIOS name. For example `net view \\dc01.contoso.local` or `net view \\dc01`.

```
C:\> dir \\dc01\sysvol
 Volume in drive \\dc01\sysvol has no label.
 Volume Serial Number is 609D-528B

 Directory of \\dc01\sysvol

28/11/2020  11:02    <DIR>          .
28/11/2020  11:02    <DIR>          ..
28/11/2020  11:02    <JUNCTION>     contoso.local [C:\Windows\SYSVOL\domain]
               0 File(s)              0 bytes
               3 Dir(s)  20,050,214,912 bytes free
```

Shares are very useful for users in order to access to files of other machines without really need to worry about using an special program or something like that. Hence, they are also very practical for attackers to move files from one computer to another in order to exfiltrate them.

```
net share Temp=C:\Temp /grant:everyone,FULL
```

### Default shares

You may notice previously that there are some shares that finished with `$`. These shares are `C$`, `ADMIN$` and `IPC$` and they are present by default in any Windows computer.

In order to access to `C$` and `ADMIN$` you are required to have Administrator privileges in the target computer. With these shares (specially `C$`) you can inspect all the computer files. Actually, these shares are used by several tools. For example, [PsExec](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec) uses `ADMIN$` to deploy a binary on charge of executing the given command.

The `IPC$` shared is an special shared used to create [named pipes](https://zer1t0.gitlab.io/posts/attacking\_ad/#named-pipes).

### Default domain shares

Apart from the common shares, in a domain, the Domain Controllers also publish the `SYSVOL` and the `NETLOGON` shares that are available for any user/computer in the domain. They are used to store files that need to be accessed by all the machines (at least Windows machines) of the domain.

The `SYSVOL` share is commonly used to store the Group Policy templates used by the computers to read the Group Policies deployed in the domain. [Sometimes these policies contains passwords](https://adsecurity.org/?p=2288). You can access to the SYSVOL share with the `\\<domain>\SYSVOL` UNC path.

The `\\<domain>\\SYSVOL\<domain>\scripts` policy is an alias for the `NETLOGON` share. The NETLOGON share is used to store the logon scripts that need to be executed for the computers of the domain.

### Named pipes

The `IPC$` share is not a directory, but it is used to create [named pipes](https://docs.microsoft.com/en-us/windows/win32/ipc/named-pipes), that allow processes of different computers interact between them with mechanisms like [RPC](https://zer1t0.gitlab.io/posts/attacking\_ad/#rpc) (Remote Procedure Calls).

Named pipes can be seen as TCP ports that allows machines communicate between them, but inside of the SMB protocol. They are used to do RPC calls, allowing a lot of protocols to communicate over SMB.

Usually the protocols that work over the RPC/SMB stack defines a known named pipe that can be used to contact with the remote service (same idea as TCP/UDP ports). For example, RPC uses the `\pipe\netlogon` named pipe to exchange the messages of the Netlogon protocol.

## Links & Papers

[https://www.hackingarticles.in/smb-penetration-testing-port-445/](https://www.hackingarticles.in/smb-penetration-testing-port-445/)

SMB Enum & Exploitation & Hardening:

{% file src="../../.gitbook/assets/smb cheetsheet.pdf" %}
