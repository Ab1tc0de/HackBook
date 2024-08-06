# SID
SID 也就是安全标识符（Security Identifiers），是标识用户、组和计算机帐户的唯一的号码。在第一次创建该帐户时，将给网络上的每一个帐户发布一个唯一的 SID。Windows 2000 中的内部进程将引用帐户的 SID 而不是帐户的用户或组名。如果创建帐户，再删除帐户，然后使用相同的用户名创建另一个帐户，则新帐户将不具有授权给前一个帐户的权力或权限，原因是该帐户具有不同的 SID 号。安全标识符也被称为安全 ID 或 SID。


## SID格式
用户通过验证后，登陆进程会给用户一个访问令牌，该令牌相当于用户访问系统资源的票证，当用户试图访问系统资源时，将访问令牌提供给 Windows NT，然后 Windows NT 检查用户试图访问对象上的访问控制列表。如果用户被允许访问该对象，Windows NT将会分配给用户适当的访问权限。  
访问令牌是用户在通过验证的时候有登陆进程所提供的，所以改变用户的权限需要注销后重新登陆，重新获取访问令牌
一个完整的 SID 包括：  
• 用户和组的安全描述  
• 48-bit的ID authority  
• 修订版本  
• 可变的验证值Variable sub-authority values  
例：`S-1-5-21-310440588-250036847-580389505-500`  
我们来先分析这个重要的 SID。第一项 S 表示该字符串是 SID；第二项是 SID 的版本号，对于2000来说，这个就是1；然后是标志符的颁发机构（identifier authority），对于2000内的帐户，颁发机构就是 NT，值是5。然后表示一系列的子颁发机构，前面几项是标志域的，最后一个标志着域内的帐户和组。
**常见SID**
`S-1-0-0                       Nobody  `
`S-1-1-0	                      Everybody`
`S-1-5-11                      Authenticated Users`
`S-1-5-18                      Local System`
`S-1-5-domainidentifier-500    Administrator`



## SID获取
`开始－运行－regedt32－HKEY_LOCAL_MACHINE\SAM\SAM\Domains\Builtin\Aliases\Members`，找到本地的域的代码，展开后，得到的就是本地帐号的所有 SID 列表。  
其中很多值都是固定的，比如第一个000001F4（16进制），换算成十进制是500，说明是系统建立的内置管理员帐号administrator，000001F5换算成10进制是501，也就是GUEST帐号了，详细的参照后面的列表。  
这一项默认是system可以完全控制，这也就是为什么要获得这个需要一个System的Cmd的Shell的原因了，当然如果权限足够的话你可以把你要添加的帐号添加进去。  
或者使用Support Tools的Reg工具：  
`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\ProfileList`
还有一种方法可以获得SID和用户名称的对应关系：  
（1）Regedt32:  
`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion \ProfileList  `
（2）这个时候可以在左侧的窗口看到SID的值，可以在右侧的窗口中ProfileImagePath看到不同的SID关联的用户名，  
比如`%SystemDrive%\Documents and Settings\Administrator.momo `这个对应的就是本地机器的管理员 SID  
`%SystemDrive%\Documents and Settings\Administrator.domain` 这个就是对应域的管理员的帐户
另外微软的 ResourceKit 里面也提供了工具 getsid，sysinternals 的工具包里面也有 Psgetsid，其实感觉原理都是读取注册表的值罢了，就是省了一些事情。

