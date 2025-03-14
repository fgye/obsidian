
---
➢ Java安全-反序列化-原生序列化类函数

➢ Java安全-SpringBoot框架-泄漏&CVE

# 反序列化-原生序列化类函数

序列化是将Java对象转换成字节流的过程。而反序列化是将字节流转换成Java对象的过程，java序列化的数据一般会以标记(ac ed 00 05)开头，base64编码的特征为rO0AB，JAVA常见的序列化和反序列化的方法有JAVA 原生序列化和JSON 类（fastjson、jackson）序列化等。

## 发现

黑盒发现（流量捕获）

白盒发现（特征类接口函数）

## 原生序列化类函数

`SnakeYaml`：完整的YAML1.1规范Processor，支持Java对象的序列化/反序列化

`XMLDecoder`：xml语言格式序列化类函数接口

`ObjectInputStream.readObject()`：任何类如果想要序列化必须实现java.io.Serializable接口

## 利用项目

Yakit [https://yaklang.com/]

[https://github.com/frohoff/ysoserial]

[https://github.com/NotSoSecure/SerializedPayloadGenerator]

# SpringBoot框架-泄漏&CVE

SpringBoot Actuator模块提供了生产级别的功能，比如健康检查，审计，指标收集，HTTP跟踪等，帮助我们监控和管理Spring Boot应用。

## 检测清单
https://github.com/LandGrey/SpringBootVulExploit

## 黑盒发现（人工识别，BP插件）

https://github.com/API-Security/APIKit

## 白盒发现（pom.xml,引用库）

```
<dependency>

<groupId>org.springframework.boot</groupId>

<artifactId>spring-boot-starter-actuator</artifactId>

</dependency>

Actuator设置全部暴露

management.endpoints.web.exposure.include=*
```

### SpringBootActuator

#### 泄漏安全（配置密码，AK/SK等）

heapdump
https://github.com/whwlsfb/JDumpSpider

https://github.com/wyzxxz/heapdump_tool

#### 漏洞安全（利用类，CVE漏洞等）

https://github.com/AabyssZG/SpringBoot-Scan

https://github.com/LandGrey/SpringBootVulExploit