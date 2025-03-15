
---
# Web架构

开源CMS：

```
Discuz、WordPress、PageAdmin、蝉知等
```

前端技术：

```
HTML5、Jquery、Bootstrap、Vue、NodeJS等
```

开发语言：

```
PHP、JAVA、Ruby、Python、C#、JS、Go等
```

框架组件：

```
SpringMVC、Thinkphp、Yii、Tornado、Vue等
```

Web服务器：

```
Apache、Nginx、IIS、Lighttpd等
```

应用服务器：

```
Tomcat、Jboss、Weblogic、Websphere等
```

## 数据库类型：

端口扫描，组合判断，应用功能

```
Mysql、SqlServer、Oracle、Redis、MongoDB等
```

操作系统信息：

Linux、Windows、Mac等

应用服务信息：

```
FTP、SSH、RDP、SMB、SMTP、LDAP、Rsync等
```

CDN信息：

```
帝联、Cloudflare、网宿、七牛云、阿里云等
```

WAF信息：

```
创宇盾、宝塔、ModSecurity、玄武盾、OpenRASP等。
```

蜜罐信息：

```
HFish、TeaPot、T-Pot、Glastopf等
```

其他组件信息：

```
FastJson、Shiro、Log4j、Solr、XStream等
```


# 指纹识别：

1、平台：

https://www.yunsee.cn/http://finger.tidesec.net/

https://fp.shuziguanxing.com/

2、项目：

https://github.com/AliasIO/wappalyzer

https://github.com/HackAllSec/hfinger

https://github.com/Tuhinshubhra/CMSeeK

https://github.com/lemonlove7/EHole_magic

https://github.com/emo-crab/observer_ward

# WAF识别：

拦截页面，identywaf项目内置

https://github.com/stamparm/identYwaf

https://github.com/EnableSecurity/wafw00f

# 蜜罐识别：

1、项目识别：

https://github.com/graynjo/Heimdallr

https://github.com/360quake/quake_rs

```
quake.exe init apikey值

quake.exe honeypot 目标
```

2、人工识别：

*端口多而有规律性（针对多服务蜜罐）

*Web访问协议就下载（针对多服务蜜罐）

*设备指纹分析（见上图，针对蜜罐特征）