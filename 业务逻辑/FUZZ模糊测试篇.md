
---
# 1、Fuzz

是一种基于黑盒的自动化软件模糊测试技术,简单的说一种懒惰且暴力的技术融合了常见的以及精心构建的数据文本进行网站、软件安全性测试。


# 2、Fuzz的核心思想:

口令Fuzz(弱口令)
目录Fuzz(漏洞点)
参数Fuzz(利用参数)
PayloadFuzz(Bypass)

# 3、Fuzz应用场景：

-爆破用户口令
-爆破敏感目录
-爆破文件地址
-爆破未知参数名
-Payload测漏洞（绕过等也可以用）

在实战黑盒中，目标有很多没有显示或其他工具扫描不到的文件或目录等，我们就可以通过大量的字典Fuzz找到的隐藏的文件进行测试。

# 4、Fuzz项目：

https://github.com/fuzzdb-project/fuzzdb

https://github.com/TheKingOfDuck/fuzzDicts

https://github.com/danielmiessler/SecLists

# Fuzz技术-用户口令-常规&模块&JS插件

https://github.com/c0ny1/jsEncrypter

https://github.com/whwlsfb/BurpCrypto

# Fuzz技术-目录文件-目录探针&文件探针

# Fuzz技术-未知参数名-文件参数&隐藏参数

# Fuzz技术-构造参数值-漏洞攻击恶意Payload