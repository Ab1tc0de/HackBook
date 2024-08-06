# nmap收集web指纹

##  [[端口扫描#^926bee|使用 nse 脚本]]枚举80端口
`sudo nmap -p80 --script=http-enum 192.168.2.200`



# nikto收集web信息
## nikto 常规扫描 web
`nikto -h http://10.10.10.10`
-h 指定目标url
## nikto 指定端口扫描
`nikto -h abc.com -p 443 -ssl`
-p 指定端口号，-ssl使用https扫描
## nikto 扫描目录
`nikto -h http://192.168.2.200/ -c /wordpress`
使用-c 参数指定扫描的目录，使用-c all 可进行目录爆破，并扫描
