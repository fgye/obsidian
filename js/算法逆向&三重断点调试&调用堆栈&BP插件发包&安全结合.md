
---
➢ JS逆向-流程&全局搜索-登录算法

➢ JS逆向-标签&XHR断点-登录算法

➢ JS逆向-结合BurpSuite-插件引用

---

![[image 3.jpg]]

前置知识
1、作用域：（本地&全局）

简单来说就是运行后相关的数据值

2、调用堆栈：（由下到上）

简单来说就是代码的执行逻辑顺序

3、常见分析调试：

-代码全局搜索

-文件流程断点

-代码标签断点

-XHR提交断点(xmlhttprequest)

#JS逆向-流程-全局搜索-登录算法

-流程断点：审查元素抓网络请求包发起程序

-全局搜索：通过抓包参数名进行代码全局搜索

#JS逆向-标签-XHR断点-登录算法

-标签断点：审查元素对应标签按钮加入断点

-XHR断点：审查元素源代码加入XHR断点配置

#JS逆向-结合BurpSuite-插件引用

1、下载phantomjs并设置环境变量

https://phantomjs.org/download.html

2、BurpSuite加载jsEncrypter插件

https://github.com/c0ny1/jsEncrypter/releases

3、对逆向的加密算法提取JS文件及代码
JSEncrypt.js
```
var r = new JSEncrypt,

o = "xxxxxxxxx";

r.setPublicKey(o);

var s = r.encrypt(password)

return s
```

4、将代码写入到模版中（引用JS和调用加密）
```
var wasSuccessful = phantom.injectJs('JSEncrypt.js');

function encrypt(password){

var r = new JSEncrypt,

o = "xxxxxxxxx";

r.setPublicKey(o);

var s = r.encrypt(password)

return s

}

// 处理函数

function js_encrypt(payload){

var newpayload;

/**********在这里编写调用加密函数进行加密的代码************/

var newpayload=encrypt(payload)

/**********************************************************/

return newpayload;

}
```

5、运行刚写入的模版文件后插件连接测试

phantomjs xxxxx.js

6、正常设置发包后选择引用插件选项