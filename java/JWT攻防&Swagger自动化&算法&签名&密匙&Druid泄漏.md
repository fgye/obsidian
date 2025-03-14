
---
➢ Java安全-Druid监控-未授权访问&信息泄漏

➢ Java安全-Swagger接口-导入&联动批量测试

➢ Java安全-JWT令牌-空算法&未签名&密匙提取

![[1 1 1.jpg]]

![[2 1.jpg]]

![[3 1.jpg]]

# Druid监控-未授权访问&信息泄漏

参考：https://developer.aliyun.com/article/1260382

Druid是阿里巴巴数据库事业部出品，为监控而生的数据库连接池。Druid提供的监控功能，监控SQL的执行时间、监控Web URI的请求、Session监控。当开发者配置不当时就可能造成未授权访问漏洞。

## 攻击点

1、直接拼接URL路径，尝试能否直接未授权访问系统功能点。

2、结合泄露URL路径和Session信息，利用BurpSuite进行尝试登录。

3、利用Cookie编辑器替换Session，再次访问后台路径尝试进入后台。

# Swagger接口-导入&联动批量测试

Swagger是一个用于生成、描述和调用RESTful接口的Web服务。就是将项目中所有（想要暴露的）接口展现在页面上，并可以进行接口调用和测试的服务。所以可以对这个接口进行漏洞测试，看是否存在未授权访问、sql注入、文件上传等漏洞。由于接口太多，一个个接口测试的话太费时间，所以一般会采用自动化接口漏洞安全测试

1、自动化发包测试

Postman

2、自动化漏洞测试

联动BurpSuite Xray等

# JWT令牌-空算法&未签名&密匙获取

JSON Web Token(JWT)。它遵循JSON格式，将用户信息加密到token里，服务器不保存任何用户信息，只保存密钥信息，通过使用特定加密算法验证token，通过token验证用户身份。基于token的身份验证可以替代传统的cookie+session身份验证方法。这使得JWT成为高度分布式网站的热门选择，在这些网站中，用户需要与多个后端服务器无缝交互

## JWT识别

### 1、标头（Header）

Header是JWT的第一个部分，是一个JSON对象，主要声明了JWT的签名算法，如"HS256”、"RS256"等，以及其他可选参数，如"kid"、"jku"、"x5u"等

alg字段通常用于表示加密采用的算法。如"HS256"、"RS256"等

typ字段通常用于表示类型

还有一些其他可选参数，如"kid"、"jku"、"x5u"等

### 2、有效载荷（Payload）

Payload是JWT的第二个部分，这是一个JSON对象，主要承载了各种声明并传递明文数据，用于存储用户的信息，如id、用户名、角色、令牌生成时间和其他自定义声明。

iss：该字段表示jwt的签发者。

sub：该jwt面向的用户。

aud：jwt的接收方。

exp：jwt的过期时间,通常来说是一个时间戳。

iat：jwt的签发时间,常来说是一个时间戳。

jti：此jwt的唯一标识。通常用于解决请求中的重放攻击。该字段在大多数地方没有被提及或使用。因为使用此字段就意味着必须要在服务器维护一张jti表， 当客户端携带jwt访问的时候需要在jti表中查找这个唯一标识是否被使用过。使用这种方式防止重放攻击似乎让jwt有点怪怪的感觉, 毕竟jwt所宣称的优点就是无状态访问

## 签名（Signature）

Signature是对Header和Payload进行签名，具体是用什么加密方式写在Header的alg 中。同时拥有该部分的JWT被称为JWS，也就是签了名的JWT。

对Header和Payload进行签名，具体是用什么加密方式写在Header的alg中。

同时拥有该部分的JWT被称为JWS，也就是签了名的JWT。

第一部分：对 JSON 的头部做 base64 编码处理得到

第二部分：对 JSON 类型的 payload 做 base64 编码处理得到

第三部分：分别对头部和载荷做base64编码，并使用.拼接起来

使用头部声明的加密方式，对base64编码前两部分合并的结果加盐加密处理，作为JWT

### 解析

在线解析：https://jwt.io/

BURP插件：Hae 或 JSON Web Tokens

## JWT安全

### 1、空加密算法（攻击头部不使用加密）

签名算法可被修改为none，JWT支持将算法设定为"None"。如果"alg"字段设为"None"，那么签名会被置空，这样任何token都是有效的。

### 2、未校验签名（攻击签名不使用签名认证）

某些服务端并未校验JWT签名，可以尝试修改payload后然后直接请求token或者直接删除signature再次请求查看其是否还有效。

### 3、暴力破解密钥（攻击签名知道密钥实现重组）

针对是对称加密算法（非对称没有用）

非对称要使用方法：获取源码或者公钥私钥文件

某些签名算法，例如HS256（HMAC+SHA-256），会像密码一样使用一个任意的、独立的字符串作为秘密密钥。这个秘钥如被轻易猜到或暴力破解，则攻击者能以任意的头部和载荷值来创建JWT，然后用密钥重新给令牌签名。

### 4、其他安全参考：（源码泄漏密匙，Kid注入等）

https://blog.csdn.net/weixin_44288604/article/details/128562796

## JWT利用

利用项目：https://github.com/ticarpi/jwt_tool

-Web345(None无签名认证)

-Web346(None算法绕过签名)

-Web347(弱口令密钥获取)

-Web348(爆破密钥上题一样)

### 使用None算法

```
python3 jwt_tool.py JWT_HERE -X a
```

### 自定义修改生成

```
python3 jwt_tool.py JWT_HERE -T
```

### 使用字典破解

```
python3 jwt_tool.py JWT_HERE -C -d dictionary.txt
```

# 指定密码测试

```
python3 jwt_tool.py JWT_HERE -C -p password_here
```

## 公钥私钥泄露

服务器私钥生成jwt，利用公钥解密jwt，只要有私钥重新生成

```python
import jwt

public = open('private.key', 'r').read()

payload={"user":"admin"}

print(jwt.encode(payload, key=public, algorithm='RS256'))
```

## 密钥混淆攻击(RS256=>HS256)

### 原理

将RS256算法改为HS256（非对称密码算法=>对称密码算法）

HS256算法使用密钥为所有消息进行签名和验证。

而RS256算法则使用私钥对消息进行签名并使用公钥进行身份验证。

```
var jwt = require('jsonwebtoken');

var fs = require('fs');

var privateKey = fs.readFileSync('./public.key');

var token = jwt.sign({ user: 'admin' }, privateKey, { algorithm: 'HS256' });

console.log(token)
```

## 黑盒JWT测试

首先找到需要JWT鉴权后才能访问的页面，如个人资料页面，将该请求包重放测试：

1）未授权访问：删除Token后仍然可以正常响应对应页面

2）敏感信息泄露：通过JWt.io解密出Payload后查看其中是否包含敏感信息，如弱加密的密码等

3）破解密钥+越权访问：通过JWT.io解密出Payload部分内容，通过空加密算法或密钥爆破等方式实现重新签发Token并修改Payload部分内容，重放请求包，观察响应包是否能够越权查看其他用户资料

4）检查Token时效性：解密查看payload中是否有exp字段键值对（Token过期时间），等待过期时间后再次使用该Token发送请求，若正常响应则存在Token不过期

5）通过页面回显进行探测：如修改Payload中键值对后页面报错信息是否存在注入，payload中kid字段的目录遍历问题与sql注入问题