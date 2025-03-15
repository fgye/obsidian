

---
# 操作系统

1、Web大小写

2、端口服务特征

3、TTL值判断返回

# IP资产

归属地查询
归属云厂商
IP反查机构
IP反查域名
IP-C段查询

这里可以转换成Web上，机构上，从而获取更多信息

# 端口资产

## 1、网络资产引擎：

直接使用网络测绘引擎(Fofa、Hunter、Quake、00信安等搜索IP；此类网络资产测绘引擎都是每隔一段时间会对全网的网络资产去做一个轮询，那可能每个引擎的轮询周期、扫描精准度都不同，故建议可使用不同的搜索引擎以获取更多资产。

地址导航：https://dh.aabyss.cn/

参考：https://mp.weixin.qq.com/s/FRgPQKJDj2xRCduwPfZrTw

## 2、在线端口扫描：

百度或google直接搜索在线端口扫描就会有一些网站，同理很多功能都可以直接搜索在线xxx；例如在线正则提取解析、在线编码转换等。

## 3、本地离线工具：

推荐使用Nmap、Masscan、Fscan、KScan，其中Nmap最为准确，但最慢；Masscan最快，误报相对更高。

端口扫描：https://xz.aliyun.com/t/15753

演示：Yakit Nmap TscanPlus FScan Tanggo等

考虑：1、防火墙 2、内网环境

可能出现案例：数据库端口开放，但进行端口扫描，发现数据库端口没有开放（排除防火墙）*注意：扫描中选择扫描协议是绕过安全组防火墙设置的一种手法，具体成功需看出网入网配置


# 角色定性判定

1、网站服务器
2、数据库服务器
3、邮件系统服务器
4、文件存储服务器
5、网络通信服务器
6、安全系统服务器

# 源码泄漏原因

1、从源码本身的特性入口
2、从管理员不好的习惯入口
3、从管理员习惯的开发入口
4、从管理员不好的配置入口
5、从管理员不好的意识入口
6、从管理员资源信息搜集入口

## 源码泄漏大概集合：

```
Webpack打包泄漏

composer.json

GitHub源码泄漏

git源码泄露

svn源码泄露

网站备份压缩文件

DS_Store文件泄露

hg源码泄漏

SWP 文件泄露

CVS泄露

Bzr泄露

WEB-INF/web.xml 泄露
```

## 获取源码思路点：

1、使用指纹识别平台获取到目标是什么名字的源码程序
通过搜索引擎获取到源码

2、使用指纹识别平台不能获取目标信息

```
后端：借助svn git composer.json ds_store 备份扫描等配置泄漏安全

前端：webpack打包 谷歌插件获取源码
```

如果没有这些问题 还想获取源码怎么办 见下面方法

3、借助资源平台去搜索（github gitee oschina等源码）

```
-特定文件

-特征联系方式

-github监控（ 除了源码，漏洞更新，工具更新等都有用处）

目标使用的源码是开源还是闭源，是否公开
```

4、WebPack打包-未知指纹识别获取源码途径

https://github.com/NothingCw/SourceDetector-dist

5、composer.json-未知指纹识别获取源码途径

6、源码文件备份泄漏-未知指纹识别获取源码途径

7、Github资源搜索-未知指纹识别获取源码途径

*拓展：配合后期监控保证第一时间通知

资源搜索语法：

```

in:name test #仓库标题搜索含有关键字

in:descripton test #仓库描述搜索含有关键字

in:readme test #Readme文件搜素含有关键字

stars:>3000 test #stars数量大于3000的搜索关键字

stars:1000..3000 test #stars数量大于1000小于3000的搜索关键字 forks:>1000 test #forks数量大于1000的搜索关键字

forks:1000..3000 test #forks数量大于1000小于3000的搜索关键字 size:>=5000 test #指定仓库大于5000k(5M)的搜索关键字 pushed:>2019-02-12 test #发布时间大于2019-02-12的搜索关键字 created:>2019-02-12 test #创建时间大于2019-02-12的搜索关键字 user:test #用户名搜素

license:apache-2.0 test #明确仓库的 LICENSE 搜索关键字 language:java test #在java语言的代码中搜索关键字

user:test in:name test #组合搜索,用户名test的标题含有test的

```

关键字配合谷歌搜索：

```
site:Github.com smtp

site:Github.com smtp @qq.com

site:Github.com smtp @126.com

site:Github.com smtp @163.com

site:Github.com smtp @sina.com.cn

site:Github.com smtp password

site:Github.com String password smtp
```

9、另类目标源码获取

https://www.huzhan.com/

https://www.goofish.com/

从目标转到寻找源码系统上，从源码系统上找应用目标

## 源码泄漏实战文章

渗透测试实战---某资金盘信息收集(.idea目录泄露)

https://mp.weixin.qq.com/s/7cSrDZci_drE6wTGvQOOHw

SRC挖掘-js.map泄露到接管云上域控

https://mp.weixin.qq.com/s/QZsNjbTiaqC1qwzARVAiGQ