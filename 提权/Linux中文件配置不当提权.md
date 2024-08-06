# Linux 中 S 位提权
## 截取含 S 位的程序库函数提权
1）使用 find 查找所有含有 S 位的可执行文件 ^abbcff
`find / -type -f -perm -u=s 2>/dev/null`
2）确定可执行文件后，使用 ltrace 来追踪执行后调用的函数，有时会调用到其他可执行文件（例如 popen 函数）
`ltrace abc`
3）将关联的可执行文件替换（使用添加用户环境变量 PATH 截取函数执行）
`export PATH=/tmp:$PATH`
4）执行文件就会调用含有恶意代码的可执行文件



## 使用含有 S 位的可执行文件提权
1）使用 GTFOBins 网站查看详情



# Linux 中 Capability 提权
## capability 提权
1） 搜索所有 cap 信息（一般常见 gdb，python，php 等）
`getcap -r / 2>/dev/null`
2）查找 GTFOBins 网站关于 cap 提权
