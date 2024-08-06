# SSTI 模板注入漏洞
## SSTI原理
php，python，ruby，java建立网站时，都会使用到模板，并且是MVC（Model View Controller）架构，当用户输入进入Controller，由Controller转交给逻辑处理程序Model进行数据处理（数据库操作等等），最后将数据交给View，显示在网页上
所以当用户输入内容没有过滤是，将恶意代码带入模板引擎，在渲染画面时执行了恶意代码，导致SSTI模板注入


## SSTI漏洞挖掘
### （1）常用模板引擎
* PHP
  Twig模板变量：`{{%s}}`
  Smarty模板变量：`{%s}`
  Blade模板变量：`{{%s}}﻿`
* Python
  Jinja2模板变量：`{{%s}}`
  Tornado模板变量：`{{%s}}`
  Django模板变量：`{{ }}﻿`
* Java
  FreeMarker模板变量：`<#%s>``${%s}`
  Velocity模板变量：`#set($x=1+1)${x}﻿`
* Ruby
  ERB模板变量：`<%=$s%>`
### （2）SSTI漏洞判断
* 使用wallalyzer插件得知网站使用的架构
* 一般当网站使用ruby或者python等高级语言搭建时，很有可能使用了该语言的模板引擎，所以很有可能存在SSTI模板注入
### （3）SSTI漏洞挖掘
* 首先寻找SSTI漏洞，利用fuzz方法输入特殊字符`${{<%[%'"}}%\`模糊模板构造，这样会引发报错，则含有SSTI漏洞
* 或者使用运算来确定SSTI漏洞，输入`{{7*7}}`，如果页面返回含有49，则含有SSTI漏洞（有时运算结果会不同，例如在python的模板引擎中使用`{{7*7}}`会返回7个7,这也是含有SSTI漏洞）
  *有时输入一些语句或者运算式，网页没有任何回应（即不报错，也不会显示结果）* 这时可以使用网站架构对应语言的休眠语句，例如sleep函数，当网页延迟一段时间回应，则包含SSTI漏洞



## SSTI漏洞利用
一般查找对应语言的可执行命令的函数，例如php中的exec（），system（）函数等
`irb` 进入ruby交互界面
### （1）ruby执行命令函数
* `exec("ls")` 当前进程执行，结束后，进程结束
* `system("ls")` 会创建行的进程执行
* `IO.popen("ls"){|f| puts f.gets}`  使用IO类中的popen函数创建新的进程执行命令
* `stdin, stdout, stderr = Open3.popen3('dc')`  或者使用Open3类中的popen，并且这个函数可以接受输入
### （2）php执行命令函数
* `system("ls")`  执行命令，会返回信息
* `exec("ls")`   执行命令，但是不会返回结果
* `assert("echo hello")`  执行php代码，但是只适用与php7以下
* `@eval("echo php")`   执行php代码，@代表忽略错误信息显示
* `call_user_func("system","whoami")`  执行php函数，会有结果显示，第一个参数为函数名，第二个为函数参数
* `array_map($func,$cmd)`  将用户自定义的函数作用在数组值上
### （3）python执行命令函数
* `os.system('ls')`  当前进程执行命令，没有返回值
* `result=os.popen('ls').read()`   新开线程，需要变量承载结果
* `subprocess.run(["ls", "-l", "/dev/null"])`  使用subprocess创建新的进程执行
