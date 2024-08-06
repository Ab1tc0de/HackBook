# evil-winrm
有时使用 nc 或者其他反弹 shell 建立的会话在执行一些命令时会有权限不足或者没有显示，这是反弹 shell 不具备互动性，使用 evil-winrm 建立会话
`evil-winrm -i 10.10.10.1 -u "dave" -p "password"`


