# 利用明文敏感信息提权
## Windows查找有关明文敏感信息
利用提权信息枚举方法论，全面了解主机，一般敏感信息存储在一些会议文件，配置文件，文档等等
**确定敏感信息的类型后使用 Get-ChildItem 获取文件位置**
`Get-ChildItem -Path c:\xampp -Include *.txt,*.ini -File -Recure -ErrorAction SilentlyContinue`
这个 powershell 语句可以查询 xampp 应用中的配置文件
`Get-ChildItem -Path c:\Users\dave\ -Include *.txt,*.pdf -File -Recure -ErrorAction SilentlyContinue` ^7f39ab
**当获得账号密码时查看用户是否可以远程登录，登录后再次进行铭感信息收集**
`runas /user:Tom@123.com cmd`
Powershell 切换用户



# 利用历史记录提权
## 敏感历史记录种类
（1）用户普通输入 History 记录
（2）powershell transcription：用户输入信息存储在 transcript files 中，这些文件通常保存在用户的主目录、一台机器的所有用户的中心目录或从所有配置的机器收集文件的网络共享中
（3）powershell script block logging：这个信息记录了用户执行代码和命令
## 读取用户历史输入
**History 查看输入记录，但是很有可能已经被清除**
`Get-History`
`Clear-History`
清除历史数据，但是只是清除表面
**使用 PSReadline 读取历史文件**
得到用户历史输入文件保存的文件位置：
`(Get-PSReadlineOption).HistorySavePath`
使用 type 命令读取文件
`type C:\Users\dave\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt`
**Transcript 文件读取**
有时在 PSReadline 中会读取到 Start-Transcript，这时用户选择了将 PowerShell 会话的全部或部分记录创建到文本文件。该脚本包括用户键入的所有命令和在控制台上显示的所有输出。
从 Windows PowerShell 5.0 开始，Start-Transcript 所有脚本生成的文件名中包含主机名。这在集中处理企业的日志记录时尤其有用。 Cmdlet Start-Transcript 创建的文件在名称中包含随机字符，以防止在同时启动两个或更多脚本时可能发生的覆盖或重复。这还可以防止未经授权地发现存储在集中文件共享中的脚本。
`Start-Transcript -Path "c:\transcript_file"`
选择读取该文件，可进一步得知用户历史输入
