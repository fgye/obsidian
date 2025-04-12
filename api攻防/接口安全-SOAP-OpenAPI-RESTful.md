
---
➢ API分类特征-SOAP&OpenAPI&RESTful

➢ API检测项目-Postman&APIKit&ReadyAPI

---


1、API分类特征-SOAP&OpenAPI&RESTful

2、API检测项目-Postman&APIKit&ReadyAPI


类型特征：SOAP,RESTful,OpenAPI,GraphQL,gRPC等

漏洞检测：接口发现，遵循分类，依赖语言，V1/V2多版本等

项目使用：APIKit Postman Swagger-hack SoapUI ReadyAPI等

安全总结：https://github.com/arainho/awesome-api-security

# 1、API分类特征

```
SOAP - WSDL

OpenApi - Swagger

RESTful - /v1/api/
```

# 2、API常见漏洞

```
OWASP API Security TOP 10 2023
```

# 3、API检测流程

接口发现，遵循分类，依赖语言，V1/V2多版本等

```
Method：请求方法

攻击方式：OPTIONS,PUT,MOVE,DELETE

效果：上传恶意文件，修改页面等
```

```
URL：唯一资源定位符

攻击方式：猜测，遍历，跳转

效果：未授权访问等
```

```
Params：请求参数

攻击方式：构造参数，修改参数，遍历，重发

效果：爆破，越权，未授权访问，突破业务逻辑等
```

```
Authorization：认证方式

攻击方式：身份伪造，身份篡改

效果：越权，未授权访问等
```

```
Headers：请求消息头

攻击方式：拦截数据包，改Hosts，改Referer，改Content-Type等

效果：绕过身份认证，绕过Referer验证，绕过类型验证，DDOS等
```

```
Body：消息体

攻击方式：SQL注入，XML注入，反序列化等

效果：提权，突破业务逻辑，未授权访问等
```

# 4、API检测项目

工具自动化：

SOAP - WSDL 测试 InfoSystem
Postman 联动
SoapUI ReadyAPI

OpenApi - Swagger 测试
Postman 联动
https://github.com/lijiejie/swagger-exp
https://github.com/jayus0821/swagger-hack

RESTful - /v1/api/ 测试
Postman 联动
APIKit 联动

手工发包测：vapi
API1-身份越权
ID值更改遍历用户信息

API4-验证枚举
验证逻辑可持续提交

API5-接口枚举
更改URL地址尝试获取

API8-数据库注入

API9-V1/2多版本
V2无法爆破/V1爆破1655

部分项目下载：

https://github.com/SmartBear/soapui

https://github.com/API-Security/APIKit

https://github.com/lijiejie/swagger-exp

https://github.com/jayus0821/swagger-hack

靶场和资源总结：

https://github.com/roottusk/vapi

https://github.com/API-Security/APISandbox

https://github.com/arainho/awesome-api-security