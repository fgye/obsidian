
---

session的三种处理方式

（1）session_PHP


```php
<?php  
highlight_file(__FILE__);  
error_reporting(0);  
session_start();  
$_SESSION['benben']=$_GET['ben'];  
?>

//http://localhost:3000/text.php?ben=666
//键名+竖线+经过 serlalize()函数序列处理的值

benben|s:3:"666";

```

（2）php_serialize

```php
<?php  
highlight_file(__FILE__);  
error_reporting(0);  
ini_set('session.serialize_handler','php_serialize');  
session_start();  
$_SESSION['benben'] = $_GET['ben'];  
$_SESSION['b'] = $_GET['b'];  

//http://localhost:3000/text.php?ben=hzh&b=666
//经过serialize()函数反序列处理的数组
a:2:{s:6:"benben";s:3:"hzh";s:1:"b";s:3:"666";}

?>
```

（3）php_binary
键名的长度对应的 ASCII字符+ 键名+ 经过 serialize() 函数反序列处理的值
```php
<?php  
highlight_file(__FILE__);  
error_reporting(0);  
ini_set('session.serialize_handler','php_binary');  
session_start();  
$_SESSION['benben'] = $_GET['ben'];  
$_SESSION['b'] = $_GET['b'];  
?>
//二进制表示长度
```

漏洞原因

用php_serialize存储session，但用session_PHP读取session

传入序列化代码前面加上 "|" , session_php 读取时会解析 | 号，将之后的作为参数进行反序列化，从而执行代码

