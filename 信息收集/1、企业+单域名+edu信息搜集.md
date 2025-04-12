
---
文章
https://mp.weixin.qq.com/s/HmikZTFmJbzNv4LkDPWxBw
# 企业主域名收集

1.股权树

```
作用：清晰地尽可能地通过公司名获取全部**主域名**目标
在企查查中根据"股权穿透图"梳理股权树
```


2、法人、企业高管

```
适用于中小型，大厂的法定人、高管大部分与其他公司有关联，容易有很多无关目标。然后一定要对上述的股权树重新梳理，得到最为完整的股权树。
```

3、ICP备案

```
在国内，只要想合法搭建网站，就需要ICP备案。所以我们从icp备案查询来打开突破口。

ICP三大因素：企业名、备案号、域名。可以通过其中之一查找到另外两个
```

3.1 查询方式

- • 使用之前收集的公司名，直接查询
    
- • 再次利用得到的备案号再次查询

3.2 查询方式

```
ICPsearch 批量查询
```

格式化脚本

```python
import re
from openpyxl import Workbook

company_pattern = r'\[Unit\]: ([\u4e00-\u9fff]+) \[Type\]'
record_pattern = r'\[icpCode\]: ([京津沪渝黑吉辽蒙冀晋陕宁甘青新藏川贵云粤桂琼苏浙皖鲁闽赣湘鄂豫][A-Z]?\d?-?[ICP备]*\d{4,10}号)-'
url_pattern = r'www\.((?:\d{1,3}\.){3}\d{1,3}|[a-zA-Z0-9-]+\.[a-zA-Z]{2,})'

with open('result.txt', 'r', encoding='utf-8') as file:
  content = file.read()

companies = re.findall(company_pattern, content)
records = re.findall(record_pattern, content)
urls = re.findall(url_pattern, content)

max_len = max(len(companies), len(records), len(urls))
companies.extend([''] * (max_len - len(companies)))
records.extend([''] * (max_len - len(records)))
urls.extend([''] * (max_len - len(urls)))
```

## 子域名

### 1.暗黑搜索引擎

主要围绕主域名、公用名、组织，进行手工查询，由于各大引擎的搜索语法大同小异，就以fofa为例：

使用搜索引擎时很重要的一点，都使用**all模式**，因为fofa默认展示1年内的资产、hunter则是一个月，使用all模式则可以获取更多边缘资产。

fofa语句1：

```
domain=""||cert=""
```

主要语法：fofa2

```
domain=""||cert="<公用名>"||cert="<组织>"
```

平常除了主域名、公用名之外，主要就关注https网站证书中的"组织"，因为有时候组织名可以让我们收集更多子域名。我们查看证书时，也要多综合收集组织名，一定要多搜集几个。

还可以加入icon值

fofa：

```
domain=""||cert="<公用名>"||cert="<组织>"||icon_hash="<hash值>"
```
icon计算方法
```
单个
密探
多个
httpx
```

## 2.工具

2.1 Oneforall

```
配置好config目录的api.py
```

命令：
```
python oneforall.py --targets targets.txt --brute True run
```

2.2 Onelong

这也是一个综合性不错的信息工具，不过也要配置不少api。主要可以直接根据单个组织（结合了ENScan_GO收集企业的一些方法，但是还是比不上原工具）来进行主子域名收集、指纹识别（一般）、app、小程序搜集，效果还不错！同时还集成了一些邮箱的收集、轻微漏洞扫描的功能。正对一些大型资产可用，中小型资产的话除了子域名收集还行以外，其他模块还是不如手动

3、偏僻子域名寻找的方法

```
https://otx.alienvault.com/api/v1/indicators/domain/baidu.com/url_list?limit=100&page=1

替换上述的baidu.com，然后从burp的响应包中提取hostname即可，也可以直接把limit调大一下，一次性提取
```


# 总结：

整理全部的url和IP（oneforall、搜索引擎导出），一定要把url和ip各自整理一个文本（因为oneforall和搜索引擎得到的ip不一定对，很大可能是cdn）

针对每一个ip分别生成http协议和https协议的列表，对url差哪个协议及添加协议（小脚本实现），整合一下得到目标（如果资产比较多，可以测活一下)——url.txt

```
工具Idregex添加协议http/https不错
```

## ip全端口+c段

**流程**：得到url，单独提取其中的域名和IP，然后针对域名使用——Eeyes——进行解析和cdn基础识别，去重整合得到部分完整的IP，提取占比大的c段，整合到ip中——ip.txt——，整体使用fscan全端口扫描，得到——port.txt——

url.txt中提取域名、ip
脚本
```python
import re

  

# 定义正则表达式，分别匹配 IP 地址和子域名

ip_pattern = r"\b\d{1,3}(?:\.\d{1,3}){3}\b"

domain_pattern = r"(?:https?://)?([a-zA-Z0-9-]+\.[a-zA-Z0-9.-]+\.[a-zA-Z]{2,})"

  

# 打开 url.txt 读取内容

with open(r"C:\Users\huangzonghui\Downloads\zp\boolv\url.txt", "r",encoding="utf8") as infile:

  content = infile.read()

  

# 提取 IP 地址并保存到 ip.txt

ip_matches = re.findall(ip_pattern, content)

with open("ip.txt", "w",encoding="utf8") as ipfile:

  for ip in sorted(set(ip_matches)):

    ipfile.write(ip + "\n")

  

# 提取子域名并保存到 ym.txt

domain_matches = re.findall(domain_pattern, content)

with open("ym.txt", "w",encoding="utf8") as domainfile:

  for domain in sorted(set(domain_matches)):

    domainfile.write(domain + "\n")

  

print("IP 地址提取完成，结果已保存到 ip.txt")

print("子域名提取完成，结果已保存到 ym.txt")

```

• Eeyes，单独把上述的ym.txt扔入Eeyes中，提取ip

```
Eeyes -l ym.txt
```

提取ip脚本

```python
#Eeyes提取脚本

import re

  

# 定义正则表达式，匹配 IP 地址或域名

pattern = r"\d+\.\d+\.\d+\.\d+"

  

# 打开 url.txt 读取内容，并在 ipym.txt 中写入匹配结果

with open("Eeyes.txt", "r",encoding="utf-8") as infile, open("ip2.txt", "w",encoding="utf-8") as outfile:

  content = infile.read()

  matches = re.findall(pattern, content)

  

  # 去重并按顺序写入结果

  for match in sorted(set(matches)):

    outfile.write(match + "\n")

  

print("提取完成，结果已保存到 ip2.txt")
```

• c段提取

因为一些比较大的产业，很大可能会在卖服务器IP时是按c段买的，我们就可以利用这一点，说不定还可以获取不少隐蔽的站点等等。

根据ip.txt中的数据，找出c段重复率高的

- • 全端口扫描，祭出神器fscan

```
fscan.exe -hf ip.txt -t 3000 -p 1-65535 -num 100 -np -o port.txt
```

得到port.txt

最终配合http/https协议获取最全web资产合集web.txt，一般而言，我们应该会直接进行测活，并重新整理存活的站点测试。但是有时候会遇到一种情况，就是主站点访问为空或者重定向，但是我们添加某个路由之后又能正常访问，这就可能让我们丢失一些可能存在漏洞的目标，所以还有一道工序

## Host碰撞

使用工具提取ip  域名  工具Idregex

web.txt测活选用Tscanplus的内置工具

再根据上面的直接分类筛选出**不正常**的ip、域名，都要带端口

直接开始host碰撞

java -jar HostCollision.jar -ifp ips.txt -hfp hosts.txt

## 指纹识别

**工具**

1、Ehole魔改版

2、httpx（有计算favicon.icon的功能）（httpx.exe -l 1.txt -sc -title -tech-detect）

3、hfinger

展示看起来一般，但是指纹库、识别方式较好，用于快速侦察比较脆弱的目标

4、spray

感觉输出不太舒服，但是指纹库还行

# 单个站点收集

主要看能不能找一些敏感文件，方式：google黑语法、github泄露、快照泄露

1、google黑语法：

```
site:target.com filetype:.doc | .docx | .xls | .xlsx | .ppt | .pptx | .odt | .pdf | .rtf | .sxw | .psw | .csv  
  
site:target.com ext:log | ext:txt | ext:conf | ext:cnf | ext:ini | ext:env | ext:sh | ext:bak | ext:backup | ext:swp | ext:old | ext:~ | ext:git | ext:svn | ext:htpasswd | ext:htaccess）
```

不限于此：

Google Dorks 获取漏洞赏金（雨苁）：https://www.ddosi.org/dork/

懒人dork： https://iamunixtz.github.io/LazyDork/

特别是在edusrc上用得很频繁

```
-----------------------------常常用到的-----------------------------  
 site:xx.edu.cn filetype:pdf 身份证 学号 工号  
 site:xx.edu.cn filetype:docx 身份证 学号 工号  
 site:xx.edu.cn filetype:xlsx 身份证 学号 工号  
  
 ---------------------------------------------------------------------------------------  
 title关键词  
 site:google.com intitle:身份证|sfz|学号|xh|登录|注册|管理|平台|验证码|账号|系统|手册|默认密码|初始密码|password|联系电话|操作手册|vpn|名单  
  
 body关键词  
 site:google.com intext:身份证|sfz|学号|xh|登录|注册|管理|平台|验证码|账号|系统|手册|默认密码|初始密码|password|联系电话|操作手册|vpn|名单  
  
 文件类型（文件类型不支持｜，需要拆分出来单独查询）  
 site:google.com filetype:pdf|xls|xlsx|docx|doc|text|ini|mdb|txt  
  
 SQL注入  
 site:google.com inurl:aspx|asp|php|jsp|do|.action|html  
  
 文件路径  
 site:google.com inurl:login|admin|manage|sysadmin|system|master|admin_login|cms|data|templates|index  
  
 文件上传  
 site:google.com inurl:file|upload|uploadfile|ewebeditor|kindediter|Ueditor|file|choosefile  
  
  -----------------------------目录穿越-----------------------------  
 intitle:index.of "parent directory"  
 intitle:index of /ppt  
 intitle:index of /password  
 intext:转到父目录/转到父路径  
 intext:to parent directory  
  
  -----------------------------业务相关性-----------------------------  
 site:google.com "审计报告" "身份证" "SFZ"  
 site:google.com "财务报告" "身份证" "SFZ"  
 site:google.com "科技奖" "身份证" "SFZ"  
 site:google.com "发明专利" "身份证" "SFZ"  
 site:google.com "财务报告" "身份证" "SFZ"  
 site:google.com "无犯罪记录" "身份证" "SFZ"  
 site:google.com "特发此证" "身份证" "SFZ"  
 site:google.com "出版合同" "身份证" "SFZ"  
 site:google.com "甲方代表" "身份证" "SFZ"  
 site:google.com "乙方代表" "身份证" "SFZ"  
 site:google.com "籍贯" "身份证" "SFZ"  
 site:google.com "汉族" "身份证" "SFZ"  
 site:google.com "职称" "身份证" "SFZ"  
  
 邮箱配置信息泄露  
 site:github.com "google.com" smtp password  
 site:github.com "google.com" smtp @126.com  
 site:github.com "google.com" String password smtp  
  
  
 数据库信息泄露  
 site:github.com  "google.com" root password  
 site:github.com  "google.com" sa password  
 site:github.com  "google.com" User ID='sa';Password  
  
 svn信息泄露  
 site:github.com  "google.com" svn  
 site:github.com  "google.com" svn password  
 site:github.com  "google.com" svn username  
 site:github.com  "google.com" svn username password  
  
 数据库备份文件  
 site:github.com  "google.com" inurl:sql  
  
  
 综合信息泄露  
 site:github.com  "google.com" password  
 site:github.com  "google.com" ftp ftppassword  
 site:github.com  "google.com" 密码  
 site:github.com  "google.com" 内部
```

2、网站时光机：

```
找一些路由，替换参数url为网址即可  
https://web.archive.org/cdx/search?collapse=urlkey&fl=original&limit=10000000000000000&matchType=domain&output=text&url=1  
  
时光机可以查看一些文件，在下面的那个红色框里面搜索：.txt、.xlsx、.pdf...  
https://web.archive.org/
```

3、github泄露：（在挖掘企业时有时候有奇效）

```
in:name baidu       #标题搜索含有关键字baidu  
in:descripton baidu     #仓库描述搜索含有关键字  
in:readme baidu       #Readme文件搜素含有关键字  
stars:>3000 baidu      #stars数量大于3000的搜索关键字  
stars:1000..3000 baidu   #stars数量大于1000小于3000的搜索关键字  
forks:>1000 baidu      #forks数量大于1000的搜索关键字  
forks:1000..3000 baidu   #forks数量大于1000小于3000的搜索关键字  
size:>=5000 baidu      #指定仓库大于5000k(5M)的搜索关键字  
pushed:>2019-02-12 baidu  #发布时间大于2019-02-12的搜索关键字  
created:>2019-02-12 baidu  #创建时间大于2019-02-12的搜索关键字  
user:name         #用户名搜素  
license:apache-2.0 baidu  #明确仓库的 LICENSE 搜索关键字  
language:java baidu     #在java语言的代码中搜索关键字  
user:baidu in:name baidu   #组合搜索,用户名baidu的标题含有baidu的等等
```

3、目录扫描

```
这个目录扫描并非全是纯跑目录字典，更别说那些waf比较强的站点，你更不可能全去爆破，最重要的点还有有想象力，根据站点已暴露的目录，去猜其他可能的站点，例如：有/login目录，那也有可能有一个/register站点。
```

4、中间件

```
请求头、指纹识别暴露 、wappalyzer 、根据报错信息判断 、根据默认页面判断。
```

5、开发语言

```
查看网站包

查看站点文件后缀

搜索引擎：site:<域名> <语言>
```

6、源码查找

```
可以直接指纹识别CMS、寻找网站框架，根据网站的一些图标、logo、关键字，还有一些特殊的目录，甚至是f12源代码中的作者泄露去寻找源码。

源码可以在github、fofa、搜索引擎、专属源码站点上去寻找
```

## Edusrc信息收集

信息收集重心：学号、身份证、工号、电话号

```
-----------------------------google语法-----------------------------  
 site:xx.edu.cn filetype:pdf 身份证 学号 工号 初始密码  
 site:xx.edu.cn filetype:docx 身份证 学号 工号  
 site:xx.edu.cn filetype:xlsx 身份证 学号 工号  
  
 再者就是学校官网的文件的信息泄露：帮助说明、默认密码、贫困生补助、奖学金补助等  
 -----------------------------fofa语法-----------------------------  
 通用挖掘：("注册" || "系统" || "登录" || "后台" || "管理") && org="China Education and Research Network Center"  
  
 单个学校收集，当然主域名不止这一个  
 domain="xxx.edu.cn" || cert="xxx.edu.cn" || cert="xx大学"  ....  
  
 -----------------------------偏门一点的检索方式-----------------------------  
 1、可以去当地的教育局查看是否有敏感信息。  
     直接使用搜索引擎，哪个省份哪个市，就找那个省份那个市的教育厅，去找可能的奖学金文件。  
 2、直接去社交平台找泄露  
     抖音、小红书、微博、贴吧、快手、qq频道  
 3、社工哥  
     3.1、直接加qq群，你知道的呀！  
     3.2、可以去找一些学校可能存在什么失物招领的平台，直接打call，你知道的呀
```

## 供应链信息收集

实在无法破局，采用供应链（多在edu、企业也有）

```
1、确定供应商  
关键系统的版权信息，最底部  
  
2、无版权信息，信息收集得到单位喜欢使用的供应商产品  
3、目标的**招标**公开网（学校）  
再找关键应用的供应商  
  
4、爱企查、天眼查找招标信息  
5、用全国采购与招标网  
6、借用系统的https证书确定开发单位  
还有一种，有部分系统在访问前，会需要你先安装什么程序或者插件，从中也可以提取出相关的证书，从而确认其生产单位  
  
7（重点）、去测试相同产品的站点，如果渗透成功，就以这个站点类比目标站点，去寻找可能存在的相同路由，相同漏洞点。也有可能两个站点之间存在某种隐藏的联系和关联处。甚至可以直接切换请求头host，把包直接替换
```