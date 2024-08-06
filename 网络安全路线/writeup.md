## mercury writeup

8080端口报错输出web目录

使用软连接提权（sudo -l后发现root执行的脚本，其中含有一些命令比如tail）

ln -s /usr/bin/vi tail   （将tail替换为vi）

加入到环境变量中： export PATH=.:$PATH （将此目录加入环境变量）

想方法让脚本执行

***

## NYXvm writeup

爆破80端口，得出key.php

nmap script=vuln得到另一个网页，其中含有ssh私钥

拿到普通权限mpampis，sudo -l 得到可以以root权限运行gcc

在GTFOBins网站中搜索gcc提权

sudo gcc -wrapper /bin/bash,-s .     提权成功

***

## vulnix writeup

nmap扫描后，显示smtp枚举

含有用户user，使用hydra爆破ssh 得到密码letmein

进入shell后，查看passwd文件得到vulnix的用户组gid

创建和vulnix相同的用户（gid为2008）使用nfd挂载远程目录，进入vulnix，发现可以创建文件和文件夹

创建.ssh文件夹，其中ssh-keygen创建id-rsa和id-rsa.pub，将id-rsa.pub改名为authorized_keys，建立免密登录ssh

将id-rsa复制到本地，ssh -i id-rsa vulnix@192.168.2.20 -oPubkeyAcceptedKeyTypes=+ssh-rsa（rsa加密已经过时，新版openssl不支持rsa，需要加上）

拿到shell，sudo -l，得到可以root用户修改/etc/exports（nfs挂载配置文件）

修改/etc/export 将root目录挂载

提权同上

***

## mrRobot writeup

开放80和443端口，目录扫描后发现wordpress

发现robot目录，其中含有一个类似字典的文件fsocity.dic

使用wpsan但是无法发现用户

使用hydra爆破用户名为Elliot，将发现的字典加入

使用fsocity.dic字典爆破密码ER28-0652

进入wordpress发现可以修改wordpress页面

修改404.php页面，加入木马

得到初始shell，查看用户robot目录，发现密码文件

破解密码文件md5，切换到robot用户

查找s位文件，发现nmap文件可以执行并且含有s位

nmap --interactive （nmap打开互动界面）

nmap> !sh（运行一个shell）

得到root

***

## pwnlab writeup

nmap扫描开放80端口，进入网页后看到登陆网页

尝试爆破无果

发现网页使用php，尝试文件包含漏洞

使用../../../../../etc/passwd无法读出文件

尝试php://filter/read=convert.base64-encode/resource=login读出login.php文件，但是base64加密，解密发现login.php文件

读出config.php文件，其中包含mysql账号，

使用账号远程登录mysql

mysql -h192.168.2.76 -p3306 -uroot -p

读出users文件中的账号密码

使用账号密码登录，发现是上传页面，利用php://filter读取upload.php文件，发现cookie文件包含，上传图片马，利用cookie包含木马，获取shell

利用之前得到的用户和密码切换用户到kane，发现含有s位的执行文件，属主位mike，执行后发现执行了cat命令，使用ln -s /usr/bin/vi提权，获得mike的shell

其中含有文件msg2root，strings 文件发现其中含有fgets函数，使用命令注入abcd ; /bin/sh 获取root权限

***

## Lin-security wirteup

首先扫描端口，发现开放nfs服务

挂载peter目录，可能为用户家目录

目录中没有其他东西，测试能否写入，发现可以写入

写入.ssh文件夹，创建公钥私钥，将公钥改名为authorized_keys

复制私钥，使用私钥登录ssh

登陆后拿到peter用户shell

sudo -l发现可以免密root执行strace程序

strace -o /dev/null /bin/sh 提权成功

***

## zico2 writeup

首先扫描开放80端口

扫描目录发现phpliteadmin文件系统

通过searchsploit得知1.9.3含有漏洞，创建后缀为.php的数据库，写入一句话木马

网页view页面含有文件包含漏洞，包含之前写入木马的数据库，拿到www-data的shell

枚举zico用户，发现其中有wordpress目录，进入目录发现wp-config.php文件，得到账号密码

尝试ssh登录，得到zico的shell

sudo -l 发现可以免密root用户执行tar命令

使用tar提权，提权成功

***

## webdeveloper writeup

扫描开放22，80端口

80上跑着wordpress，目录爆破发现ipdata/目录中有一个抓包文件

使用wireshark打开发现登录账号和密码

使用账号登录wordpress，上传含有木马的主题

获得shell，查看wp-config.php文件得知另一组账号密码

使用ssh登录，webdeveloper登陆成功

sudo -l 发现可以以root用户运行tcpdump

使用tcpdump提权

***







