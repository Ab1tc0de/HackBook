# 基础命令

## 文件和目录
`ls -F`      在没有颜色突出的系统中显示出目录
`ls -R`       -R 为递归选项，会输出目录中的目录和文件
`ls -l scr \*`       可以使用通配符搜索
`ln -s datafile sl_datafile`         创建 sl_datafile 指向 datafile 的软连接
`mkdir -p test1/test2/test3`        使用-p 参数创建嵌套目录
`tree `         显示当前目录的目录树
`tree dirbase  /tree -hf`       -h 显示文件夹内的内容大小，-f 显示文件完整路径
`file my_file`        查看文件类型
`cat -n test1.sh`         显示 test1文件并且标记行号
`ls -li /etc/hosts`        查看文件 indode 节点
`chattr +i /etc/hosts`       给文件加上 indode 节点，锁定文件
`chattr -i /etc/hosts`       取消锁定
`lsattr /etc/hosts`        查看文件隐藏属性



## 系统监测
`ps aux/ps -ef`       查看所有进程，包括后台进程
`ps aux | grep 目标程序`
`pstree 查看进程树`       -p 可以查看进程 id
 进入top时，输入M（以内存占用排序），P（按cpu占用排序），k  （杀死进程，）输入k时，会要求输入PID值，然后是信号值，15或9（9为强制结束）
 `kill -s 信号值/信号名 3454`            使用进程 id 结束进程
`killall 进程名`           结束进程树
`top -c -o %CPU`            查看 cpu 占用，-c 显示命令行参数，-o 按照指定字段排序
`ps -eo pid,ppid,%mem,%cpu,cmd --sort=-%cpu | head -n `         查看 cpu 占用前5的进程



## 用户组的管理
`sudo授权文件/etc/sudoers`           `%sudo ALL(ALL) ALL`         用户（被授权的用户或组） 主机名列表=（用户） 命令程序列表
`useradd -m -G sudo -s /bin/bash abc`                useradd -m 创建用户家目录 -G 组名称 -s 指定用户登录的 shell
`usermod -p ‘hash 加密的密文’用户名`                修改用户密码
`openssl passwd -1 -salt 用户名密码`           使用 openssl 的 passwd 哈希密码加密功能，加密用户密码，-1 是使用 MD5加密
`passwd 用户名密码 `        设置用户密码，限于已存在用户并且密码为加密密文
`userdel 用户名 （删除用户）/userdel -rf 用户名`       -r 删除用户家目录，-f 强制删除用户无论是否已经登录
`who /w`       查看在线用户
`last `      查看用户最近登录信息



## Linux硬件信息
`uname -a`      查看版本信息
`cat /etc/issue`     本地控制台标题信息
`lsb_release -a`     查看发行版本
`cat /proc/cpuinfo | more `      查看 cpu 型号
`cat /proc/meminfo`       查看内存
`free -m`       查看内存，以 M 为单位
`fdisk -l `        （显示所有硬盘）1-4保留给主分区，扩展分区也占用一个，逻辑分区从5开始
`lsblk `      显示块设备
`lsusb`        显示 usb 设备



## 网络管理
`netstat -antu`       -a 显示所有网络连接，-n 以数字形式不做名称解析，-t 查看 tcp 连接，-u 查看 udp 连接
`netstat -tunlp`     -tu 显示 tcp 和 udp 链接，-lp 显示监听的端口
`lsof -i`         `lsof -i:3306`显示所有端口占用，查看3306端口占用
`systemctl start ssh /systemctl stop ssh /systemctl status ssh /systemctl restart ssh`        开启，关闭，查看，重启 ssh 服务
`systemctl is-enable ssh`        查看 ssh 服务是否开机启动
`systemctl enable ssh`    设置 ssh 服务开机启动
 完成域名解析/etc/hosts（优先级比DNS高）
格式：
IP 地址主机名1  主机名2 ......
作用：加快访问站点的速度，屏蔽一些站点，帮助做实验
`service --status-all  `查看本机所有服务



## 压缩文件
`tar -cf document.tar /home/work `        将 work 文件夹压缩为 document.tar 文件，-c 是表示产生新的包，-f 指定包的文件名。
`tar -xf document.tar `        将 document.tar 文件解压到当前目录，-x 是解开的意思。
`tar -czf abc.tar.gz /home/work`      -z 指定 tar.gz 压缩文件，将 work 压缩为 abc.tar.gz 文件
`tar -xzf abc.tar.gz `    将 abc.tar.gz 解压到当前目录
`tar -cjf all.tar.bz2 \*.jpg`   -j 代表 bzip2压缩文件
`tar -xjf all.tar.bz2`
`zip -q -r abc.zip /home/work`    -q 不显示执行过程 -r 递归处理
`unzip abc.zip -d /home/work `    将 abc.zip 解压到 work 目录中



# 基础shell快捷键

## shell控制
`ctrl+z` 将当前页面挂起
`fg` 返回前面挂起的页面


## shell光标控制
`ctrl+xx` 前进到当前输入的前面或者后面
`ctrl+a`  跳转到开头
`ctrl+e` 跳转到结尾
`ctrl+f` 向前一个字符
`ctrl+b` 向后一个字符
`alt+f` 向前跳到下一个单词开头
`alt+b` 向后跳到前一个单词开头


## shell编辑
`ctrl+u` 剪切整行命令
`alt+d` 删除光标所在位置到光标所在词的结尾位置的所有内容(如果光标是在词开头，则删除整个词)。
`ctrl+y` 粘帖剪切版中的内容
`alt+y` 选择剪贴板中内容一般同ctrl+y使用
`ctrl+p`  上一个历史命令
`ctrl+n` 下一个历史命令
`Ctril + h`  删除光标的前一个字符
`Ctril + d` :删除当前光标所在字符
`Ctril` + k` 删除光标之后所有字符
`Ctril + w`  删除光标前的单词(Word, 不包含空格的字符串)



# 文本过滤

## grep过滤
**grep [option] [pattern] [file name]**
`grep -v '^root' /etc/passwd`        匹配不以 root 开头的内容，-v 反向匹配，^root 以 root 开头的行
`grep -irl  “password” / `         -i 忽略大小写，-r 递归过滤，-l 只打印匹配行的文件名
`grep -c “password” rocket.txt`          -c 得到匹配目标有多少行
`grep -nw “linux”  test.txt`     -n 显示匹配行行号，-w 匹配整个单词
`grep -B 2 “10”  number.txt`      -B 数字匹配行的前几行，-A 数字匹配行的后几行，-C   数字匹配行上下几行
`grep -E '^$|^#' config.txt `  过滤配置中生效的条目，及过滤掉空白行和  \#\开头的行
`grep "r[a-z]*t" test.txt`       [a-z] a 到 z 任意一个字符，[a-z]*  匹配0次或多次，[a-z]+   匹配一次或多次，[a-z]?    匹配0次或1次，{n,m}   最少匹配 n 次，最多匹配 m 次



## awk过滤
**awk [选项] '模式{动作}' 文件
awk首先读取记录（一行一行读取），然后根据模式匹配找到对应的行，然后分割，最后输出
模式可以使用正则表达式（必须用/ /符号括住），或者使用特定表达式**
`awk -F: '/^root/{print $1}' /etc/passwd `       
    输出以 root 开头行的第一个词
`awk -F: 'NR==3{print $1,$3, $4,$ NF}' /etc/passwd`      
    输出第三行的第一，第三，第四和最后一个词，NF 记录当前统计总字段数，NR 用来记录行号
`awk 'BEGIN{i=0}{i++}{print}END{print "count line is " i}' /etc/passwd`       
    可以在显示完内容显示处有多少行
`awk 'BEGIN{FS="\n";RS=""}{print $2}' test.txt`    
    可以使用 FS（字段分隔符，默认是空格），RS（记录分割符，默认是回车）
`awk -F: 'BEGIN{OFS=" | "}{printf "|%+15s|%-15s|\n", $NF, $1}' /etc/passwd`
    |%+15s| 以 | 为分隔符 %s 配合 printf 使用代替当前字符串右对齐占用15字符，|%-15s|  以 | 为分隔符 %s 配合 printf 使用代替当前字符串左对齐占用15字符，$NF  存储以 | 为分隔符的最后一列，
    $1 存储以 | 为分隔符的第一列
`awk -F: '$4 > $3{print $0}' /etc/passwd` 
     $4 代表属组所在列，$3 代表属主所在列，$0  所有行
`awk -F: '$3 + $4 > 2000{print $0}' /etc/passwd `  
    打印出属组 + 属主的 ID 大于 2000 的
`awk -F: '{if($3>$4){print "大于"}else{print "小于或等于"}}' /etc/passwd `         
    if(){} 但分支，if(){}else{} 双分支，if(){}else if(){}else{}多分支
`awk -F: '{for(i=10;i>0;i--){print $0}}' /etc/passwd`
    for 每一行打印10次
    `awk -F: '{i=1; while(i<10){print $0, i++}}' /etc/passwd`
    while 每一行打印10次



## sed 过滤
**参数p 打印匹配行**
`sed -n ’2p’/etc/passwd`       打印出第2行
`sed -n ’1,3p’/etc/passwd`     打印出第1到第3行
`sed -n ‘$p’/etc/passwd`       打印出最后一行
`sed -n ‘/user/p’ /etc/passwd` 打印出含有user的行
`sed -n ‘/\$/p’ /etc/passwd`   打印出含有$元字符的行,$意为最后一行
`sed -n ‘$=’ ok.txt`	       打印总行数
**参数a，i  插入文本**
`sed -n ‘/FTP/p’/etc/passwd`       打印出有FTP的行
`sed ‘/FTP/a\ 456′ /etc/passwd`       在含有FTP的行后面新插入一行，内容为456
`sed ‘/FTP/i\ 123′ /etc/passwd`      在含有FTP的行前面新插入一行，内容为123
`sed ’5 a\ 123′ /etc/passwd`       在第5行后插入一新行，内容为123
**参数d  输出文本**
`sed ‘/^$/d’ file.conf > file.new.conf`
`sed -i ‘/^$/d’ ~/$cid.txt`  删除无内容空行
`sed -i ’1d’ ~/$cid.txt`  删除第一行
`sed -i ‘/^love/d’ file`   删除love开头的行
`sed ‘/^[[:space:]]*$/d’ file.conf > file.new.conf` 删除由空格和Tab而成的空行
**参数s  替换文本**
`sed ‘s/user/USER/’/etc/passwd` 将第1个user替换成USER,g表明全局替换
`sed ‘s/user/USER/g’/etc/passwd` 将所有user替换成USER
`sed ‘s/\ . $//g’` 删除以句点结尾行
`sed ‘-e/abcd/d’ `删除包含a b c d的行
`sed ‘s/[ ] [ ] [ ] */[ ]/g’` 删除一个以上空格，用一个空格代替
`sed ‘s/^ [ ] [ ] *//g’` 删除行首空格
`sed ‘s/\ . [ ] [ ] */[ ]/g’` 删除句点后跟两个或更多空格，代之以一个空格
`sed ‘/^ $/d’ `删除空行
`sed ‘s/^ .//g’ `删除第一个字符
`sed ‘s/COL \ ( . . . \ )//g’` 删除紧跟C O L的后三个字母
`sed ‘s/^ \///g’ `从路径中删除第一个\
`sed ‘s/[ ]/[TAB]//g’ `删除所有空格并用t a b键替代
`sed ‘S/^ [TAB]//g’ `删除行首所有t a b键
`sed ‘s/[TAB] *//g’ `删除所有t a b键
一般sed处理每一行都会在行尾插入换行符，所以使用test跳转指令
`sed -nr ‘ H;${x;s/\n//g;p}’`  删除文本中的换行符
`sed ':a;N;s/\n/,/g;ta'  `将文本中的换行符换为逗号 
N是把下一行加入到当前的hold space模式空间里，使之进行后续处理，最后sed会默认打印hold space模式空间里的内容。也就是说，sed是可以处理多行数据的。
:a和ta是配套使用，实现跳转功能。t是test测试的意思。
另外，还有:a和ba的配套使用方式，也可以实现跳转功能。b是branch分支的意思。



## tee输入输出
**将输出的内容输入到文本中**
`ping 10.10.10.1 -c 4 | tee pingoutput.txt`
**使用-i使用ctrl+c退出tee**
`ping 10.10.10.1 -c 100 | tee -i ping.txt`   这时使用ctrl+c可以优雅推出tee的写入
**追加文本**
一般tee不会将新的内容追加到旧的中，会重新刷新写入，使用-a强制追加
`ping 10.10.10.1 -c 4 | tee -a oldping.txt`
