
---
➢ MXSS&UXSS-CVE-2021-34506

➢ SVG&PDF&SWF-XSS&上传&反编译

MXSS：#https://www.fooying.com/the-art-of-xss-1-introduction/

UXSS：Universal Cross-Site Scripting

UXSS是利用浏览器或者浏览器扩展漏洞来制造产生XSS并执行代码的一种攻击类型。(例如翻译插件引发xss)

MICROSOFT EDGE uXSS CVE-2021-34506

Edge浏览器翻译功能导致JS语句被调用执行

https://www.bilibili.com/video/BV1fX4y1c7rX

#SVG-XSS

SVG(Scalable Vector Graphics)是一种基于XML的二维矢量图格式，和我们平常用的jpg/png等图片格式所不同的是SVG图像在放大或改变尺寸的情况下其图形质量不会有所损失，并且我们可以使用任何的文本编辑器打开SVG图片并且编辑它，目前主流的浏览器都已经支持SVG图片的渲染。

```
<svg xmlns="http://www.w3.org/2000/svg" version="1.1">

<circle cx="100" cy="50" r="40" stroke="black" stroke-width="2" fill="red" />

<script>alert(1)</script>

</svg>
```

#PDF-XSS

1、创建PDF，加入动作JS

2、通过文件上传获取直链

3、直链地址访问后被触发

借助PDF进行XSS漏洞攻击#https://blog.csdn.net/qq_48201589/article/details/135856195

项目：迅捷PDF编辑器试用版

#FLASH-XSS

-制作swf-xss文件：

1、新建swf文件

2、F9进入代码区域

3、属性发布设置解析

```
//取m参数

var m=_root.m;

//调用html中Javascript中的m参数值

flash.external.ExternalInterface.call(m);

触发：?m=alert(/xss/)
```

项目：Adobe Flash Professional CS6

-测试swf文件xss安全性：

1、反编译swf文件

2、查找触发危险函数

3、找可控参数访问触发

xss一是指执行恶意js，那么为什么说flash xss呢？是因为flash有可以调用js的函数，也就是可以和js通信，因此这些函数如果使用不当就会造成xss。常见的可触发xss的危险函数有：getURL，navigateToURL，ExternalInterface.call，htmlText，loadMovie等等

项目：JPEXS Free Flash Decompiler(反编译swf文件)