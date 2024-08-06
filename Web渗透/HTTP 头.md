# HTTP请求头和响应头 
在许多默认配置中，响应头中还显示版本号。
响应头中的名称或值通常会显示有关应用程序所使用的技术栈的附加信息。


## HTTP 请求 
* **Accept**： 浏览器可接受的MIME类型。
* **Accept-Charset**：浏览器可接受的字符集。
* **Accept-Encoding**：浏览器能够进行解码的数据编码方式。
* **Accept-Language**：浏览器所希望的语言种类。
* **Authorization**：授权信息，通常出现在对服务器发送的WWW-Authenticate头的应答中。
* **Content-Length**：表示请求消息正文的长度。
* **Host**： 客户机通过这个头告诉服务器，想访问的主机名。
* **If-Modified-Since**：客户机通过这个头告诉服务器，资源的缓存时间。
* **Referer**：客户机通过这个头告诉服务器，它是从哪个资源来访问服务器的(防盗链)。
* **User-Agent**：User-Agent头域的内容包含发出请求的用户信息。
* **Cookie**：客户机通过这个头可以向服务器带数据，这是最重要的请求头信息之一。
* **Pragma**：指定“no-cache”值表示服务器必须返回一个刷新后的文档，即使它是代理服务器而且已经有了页面的本地拷贝。
* **From**：请求发送者的email地址，由一些特殊的Web客户程序使用，浏览器不会用到它。
* **Connection**：处理完这次请求后是否断开连接还是继续保持连接。
* **Range**：Range头域可以请求实体的一个或者多个子范围。


## HTTP 响应 
### （1）响应行：
* 响应行一般由协议版本、状态码及其描述组成 比如 HTTP/1.1 200 OK，其中协议版本HTTP/1.1或者HTTP/1.0，200就是它的状态码，OK则为它的描述。
* 常见状态码：
  100~199：表示成功接收请求。
  200~299：表示成功接收请求并已完成整个处理过程。常用200
  300~399：为完成请求，客户需进一步细化请求。
  400~499：客户端的请求有错误。
  500~599：服务器端出现错误。
### （2）响应头：
* **Allow**：服务器支持哪些请求方法(如GET、POST等)。
* **Content-Encoding**：文档的编码(Encode)方法。
* **Content-Length**：表示内容长度。
* **Content- Type**：表示后面的文档属于什么MIME类型。
* **Date**：当前的GMT时间。
* **Expires**：告诉浏览器把回送的资源缓存多长时间，-1或0则是不缓存。
* **Last-Modified**：文档的最后改动时间。
* **Location**：这个头配合302状态码使用，用于重定向接收者到一个新URI地址。
* **Refresh**：告诉浏览器隔多久刷新一次，以秒计。
* **Server**：服务器通过这个头告诉浏览器服务器的类型。
* **Set-Cookie**：设置和页面关联的Cookie。。
* **Transfer-Encoding**：告诉浏览器数据的传送格式。
* **WWW-Authenticate**：客户应该在Authorization头中提供什么类型的授权信息。
  *注：设置应答头最常用的方法是HttpServletResponse的setHeader，该方法有两个参数，分别表示应答头的名字和值。和设置状态代码相似，设置应答头应该在发送任何文档内容之前进行。*
*  **setContentType**：设置Content-Type头。
* **setContentLength**：设置Content-Length头。
* **addCookie**：设置一个Cookie



## HTTP 方法
### （1）GET 方法
* 请求指定资源，没有请求内容体
  `GET /index.html `
### （2）HEAD 方法
* 在下载一定大文件时，使用 head 方法得知其 content-length，节约带宽
  `HEAD /index.html`
### （3）POST 方法
* 发送数据给服务器
  `application/x-www-form-urlencode` 使用&分割上传的键值对
  `multipart/form-data`每个上传的值都包含在主题部分中
### （4）PUT 方法
* 上传当前载荷，或者好似用该载荷替换目标
### （5）DELETE 方法
* 删除选定的资源
  `DELETE /file.html HTTP/1.1`
### （6）CONNECT 方法
* 与代理服务器建立 tcp 隧道，一般用于 HTTPS 代理
 ![[Pasted image 20240125160126.png]]  
### （7）OPTIONS 方法
* 请求服务器可以使用的方法
  `OPTIONS /index.html HTTP/1.1`
### （8）TRACE 方法
 * 发送 trace 请求，服务器会将原始请求报文作为响应返回
   `TRACE /index.html HTTP/1.1`
   *一般如果含有 TRACE 方法，可以尝试 [[XSS漏洞#cookie盗用与利用|XSS]] 盗取用户凭据，毕竟服务器会将请求内容以明文返回，其中会包括 cookie 或者 session*
### （9）PATCH 方法
 * 对于资源进行修改



# HTTP x-forwarded-for
## 正向代理与反向代理 
* **正向代理**：
  代理服务器代理客户端去访问服务器，即客户端请求发送到代理端，代理端在及那个请求交给服务端，服务端不知道客户端的地址。（隐藏客户端ip，代理）
* **反向代理**：
  代理服务器代理服务端去和客户端交互，即是指以代理服务器来接受internet上的连接请求，然后将请求转发给内部网络上的服务器，并将从服务器上得到的结果返回给internet上请求连接的客户端，此时代理服务器对外就表现为一个反向代理服务器。客户端不知道服务端地址。（用于隐藏服务器ip，负载均衡，提高访问速度）



## x -forwarded-for
* 格式：X-Forwarded-For: client, proxy1, proxy2
  如果请求经过多个代理服务器，每个代理服务器的 IP 地址会依次出现在列表中。这意味着，如果客户端和代理服务器行为良好，最右边的 IP 地址会是最近的代理服务器的 IP 地址，最左边的 IP 地址会是原始客户端的 IP 地址。



# HTTP WWW-Authenticate
存在于HTTP header中，定义了如何进行身份验证
## WWW-Authenticate认证流程
### （1）访问授权
* 当访问需要授权时，服务器会犯会401 Authorization Required
```
HTTP/1.1 401 Authorization Required
www-Authenticate: Basic realm= "family"
```
Basic：说明需要基本认证，realm：说明客户端需要输入这个安全区的用户名和密码，而不是其他区的。因为服务器可以为不同的安全区设置不同的用户名和密码。如果服务器只有一个安全区，那么所有的基本认证用户名和密码都是一样的
### （2）客户端需要返回认证信息
```
GET /family/son.jpg  HTTP/1.1 Authorization: Basic
U2h1c2hlbmcwMDcldUZGMUFzczAwNw==
```
Basic 内容为： `用户名:密码` base64形式



## WWW -Authenticate 突破
* 使用burpsuit爆破，首先创建 WWW-Authenticate认证头
* 将base64加密部分作为payload
* 选择字典
* 设置payload processing即，在开始爆破前对字典进行操作，添加Add Prefix：admin:，再添加Base64-encode
* 爆破，错误代码位401

  


# User-Agent
有些时候使用默认的user-agent会引起目标服务器警觉，导致被封ip，可以使用搜索引擎的user-agent进行扫描
## 常用 user -agent
### （1）google
```
Mozilla/5.0 (compatible; Googlebot/2.1;+http://www.google.com/bot.html) 

Googlebot-Image/1.0   //google图片搜索
```
### （2）bing
```
Mozilla/5.0 (compatible; bingbot/2.0;+http://www.bing.com/bingbot.htm)
```
### （3）baidu
```
Mozilla/5.0 (compatible; Baiduspider-render/2.0;+http://www.baidu.com/search/spider.html)

Mozilla/5.0 (iPhone; CPU iPhone OS 9_1 like Mac OS X)

AppleWebKit/601.1.46 (KHTML, like Gecko) Version/9.0 

Mobile/13B143   Safari/601.1 (compatible; Baiduspider-render/2.0;+http://www.baidu.com/search/spider.html)   //百度移动搜索

Mozilla/5.0 (compatible; Baiduspider/2.0; +http://www.baidu.com/search/spider.html)

Baiduspider-image+(+http://www.baidu.com/search/spider.htm)   //百度图片搜索
```
### （4）360
```
Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0);

360spider (http://webscan.360.cn)  //360网站安全检测
```
### （5）腾讯搜搜
```
Sosospider+(+http://help.soso.com/webspider.htm)

Sosoimagespider+(+http://help.soso.com/soso-image-spider.htm)   //搜搜图片搜索
```



# Token
## JWT Token
json web token全称
### （1）JWT编码后样子
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.UQmqAUhUrpDVV2ST7mZKyLTomVfg7sYkEjmdDI5XF8Q
### （2）JWT由三部分组成
* **header** 其中含有声明类型，加密算法所有内容经过base64加密后的密文
* **playload** 存放有效信息位置，包括标准注册声明，公共声明，私有声明
* **signature** 这个部分需要base64加密后的header和base64加密后的payload使用.连接组成的字符串(头部在前)，然后通过header中声明的加密方式进行加盐secret组合加密，然后就构成了jwt的第三部分。
  *JWT token会在请求头中，Authorization：.....*



## Wordpress nonce Token
* WordPress的Nonce用来保护URL和表单免受恶意攻击。它帮助WordPress确定一个请求是否有效，防止未经授权的行动和输入。
* Nonces通过给URL增加一个额外的保护层来防止CSRF攻击。例如在url中嵌入wpnonce字段
```
http://yourwebsite.com/wp-admin/post.php?post=123&action=trash&_wpnonce=b192fc4204
```
* 如果你在没有WordPress生成的正确的nonce的情况下试图进入该URL，你会看到一个403 Forbidden。
* Nonce的默认寿命是24小时

