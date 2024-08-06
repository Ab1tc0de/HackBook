# 白盒测试

## php漏洞

### 变量覆盖漏洞
**register_globals设置**
*php5.3之前可以使用，php5.3之后已经删除*
如果开启这个设置，`$_GET` `$_POST`中没有设置的变量会变成全局变量，覆盖其他变量
**extract()**
如果这个函数传入一个键值对，键值为变量名，值为该变量的值（如果某个变量与键值相同，则会覆盖）
**parse_str()**
这个函数将字符串转变为变量，（如果某个变量与字符串相同，则会覆盖）
**import_request_variables()**
制定将哪种变量转变为全局变量，GET，POST还是COOKIE


### 弱类型漏洞
**比较`==`**
*php7或者php7之前*
`===`：会比较类型是否相同，然后做比较
`==`：直接比较，会将字符串转化为相同比较
字符串与数值比较，会将字符串改变为数值然后在比较
`var_dump('hello' == 0)  true`  开头第一个字符不是数字，则全都变换为0
`var_dump('21abc' == 21) true`  开头为数字，之将数字转换
**字符连接**
`var_dump("abc" . 56)`  会将56数字改变为字符串连接


### 反序列漏洞




### php伪协议
PHP 提供了一些杂项输入/输出（IO）流，允许访问 PHP 的输入输出流、标准输入输出和错误描述符， 内存中、磁盘备份的临时文件流以及可以操作其他读取写入文件资源的过滤器。
**php://input**
php://input 是个可以访问请求的原始数据的只读流。 POST 请求的情况下，最好使用 php://input 来代替 $HTTP_RAW_POST_DATA，因为它不依赖于特定的 php.ini 指令。 而且，这样的情况下 $HTTP_RAW_POST_DATA 默认没有填充， 比激活 always_populate_raw_post_data 潜在需要更少的内存。 enctype="multipart/form-data" 的时候 php://input 是无效的。
**php://filter**
php://filter/read=convert.base64-encode/resource=［文件路径］


## Java漏洞
### 反序列漏洞




# 黑盒测试

## 模糊测试
### wfuzz模糊测试
当出现/~文件名/路径时，可以使用fuzz方法，探测是否还有~文件名类似文件
`wfuzz -c -w [/wordlist/]  -u [url]  --hc 404`


### ffuf模糊测试
除了wfuzz之外还可以使用ffuf模糊测试
`ffuf -u [url] -w [/wordlist/] -e .txt,.php -mc 200`


### 使用gobuster爆破API入口
使用gobuster模式爆破，-p参数
创建模式文件，pattern.txt，其中写入匹配语句：`{GOBUSTER}/v1    {GOBUSTER}/v2，GOBUSTER`将转化为字典中的条目，后面版本号不止v1v2，可以写很多
为了验证是否有任何API属性与username属性相关，我们将通过在最后插入admin用户名来扩展API路径。`..../user/v1/admin`
有时发现register API，可以尝试注册新用户为admin用户，其中写入admin=true值，如果成功则创建了一个管理员用户，使用该账户可以修改admin用户密码
