
---
#PHP-MYSQL-数据请求类型

一、原因
	SQL语句由于在黑盒中是无法预知写法的，SQL注入能发成功是需要拼接原SQL语句，大部分黑盒能做的就是分析后各种尝试去判断，所以有可能有注入但可能出现无法注入成功的情况。究其原因大部分都是原SQL语句的未知性导致的拼接失败！
	由于开发者对于数据类型和SQL语句写法（框架写法）导致SQL注入拼接失败

二、类型

1、数字型(无符号干扰)

	select * from news where id=$id;

2、字符型（有符号干扰）

	select * from news where id='$id';

3、搜索型（有多符号干扰）

	select * from news where id like '%$id%'

4、框架型（有各种符号干扰）

	select * from news where id=('$id');

	select * from news where (id='$id');
绕过方式：==构造闭合==

#PHP-MYSQL-数据请求方法

三、全局变量方法
GET POST SERVER FILES HTTP头等

User-Agent：
使得服务器能够识别客户使用的操作系统，游览器版本等.（很多数据量大的网站中会记录客户使用的操作系统或浏览器版本等存入数据库中）

Cookie：
网站为了辨别用户身份、进行session跟踪而储存在用户本地终端上的数据X-Forwarded-For：简称XFF头，它代表客户端，也就是HTTP的请求端真实的IP,（通常一些网站的防注入功能会记录请求端真实IP地址并写入数据库or某文件[通过修改XXF头可以实现伪造IP]）.

Rerferer：浏览器向 WEB 服务器表明自己是从哪个页面链接过来的.

Host：客户端指定自己想访问的WEB服务器的域名/IP 地址和端口号

四、功能点：
1、用户登录时
2、登录判断IP时

是PHP特性中的$_SERVER['HTTP_X_FORWARDED_FOR'];接受IP的绕过（绕过）
(如果对方用了HTTP_X_FORWARDED_FOR接收用户ip则可以在数据包中修改）
实现：代码配置固定IP去判断-策略绕过

实现：数据库白名单IP去判断-select注入

实现：防注入记录IP去保存数据库-insert注入

3、文件上传将文件名写入数据库-insert注入

#PHP-MYSQL-数据请求格式 
（自己先将注入语句进行转换）

1、数据采用统一格式传输，后端进行格式解析带入数据库（json）

2、数据采用加密编码传输，后端进行解密解码带入数据库（base64）

