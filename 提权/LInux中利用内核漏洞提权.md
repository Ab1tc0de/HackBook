# Linux 内核提权
## 使用内核系统漏洞垂直提权
使用系统漏洞将目前用户添加到root组中，或者重新获取一个root权限的shell
首先使用 lsb_release -a 查看机器发行版本，如 centos rhel 等等
利用 searchsploit 查找可用内核提权脚本
找不出后使用 uname -a 查看系统内核版本，进一步缩小范围




