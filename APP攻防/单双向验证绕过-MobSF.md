
---

➢ APP-综合分析-Mobexler&MobSF识别

➢ APP-某社交-双向证书绕过&Frida&HOOK

➢ APP-某游戏-单向证书绕过&XP框架&Frida


---

# APP-综合分析

## Mobexler&MobSF识别


==MobSF==

```

移动安全框架 (MobSF) 是一种自动化的一体化移动应用程序 (Android/iOS/Windows) 渗透测试、恶意软件分析和安全评估框架，能够执行静态和动态分析。

MobSF 支持移动应用程序二进制文件（APK、XAPK、IPA 和 APPX）以及压缩源代码，并提供 REST API 以与您的 CI/CD 或 DevSecOps 管道无缝集成。动态分析器可帮助您执行运行时安全评估和交互式仪器测试

```

==Mobexler==

```
是基于Elementary OS的定制虚拟机，旨在帮助进行Android和iOS应用程序的渗透测试。Mobexler预装了各种开源工具，脚本，黑客必备软件等，这些都是安全测试Android和iOS应用程序所必需的。
```

https://mobexler.com/

# APP-某社交-双向证书绕过

## Frida&HOOK

-r0capture仅限安卓平台，测试安卓7、8、9、10、11可用 ；无视所有证书校验或绑定，不用考虑任何证书的事情；通杀TCP/IP四层模型中的应用层中的全部协议；通杀协议包括：Http,WebSocket,Ftp,Xmpp,Imap,Smtp,Protobuf等、及它们的SSL版本；通杀所有应用层框架，包括HttpUrlConnection、Okhttp1/3/4、Retrofit/Volley等等；无视加固，不管是整体壳还是二代壳或VMP，不考虑加固事情

Firda 是一款易用的跨平Hook工具， Java层到Native层的Hook无所不能，是一种 动态 的插桩工具，可以插入代码到原生App的内存空间中，动态的去监视和修改行为，原生平台包括Win、Mac、Linux、Android、iOS全平台。

测试环境：

## 准备Frida与模拟器连接

Python3 逍遥模拟器 r0capture frida-server Burpsuite wireshark

https://github.com/frida/frida/releases

1、本地安装Frida

```
pip install frida
pip install frida-tools
```

2、模拟器安装Frida

注意：版本要与本地Frida一致

下载：https://github.com/frida/frida/releases

```
真机：ARM版本及位数
模拟器：无ARM的位数

获取本机的位数
getprop ro.product.cpu.abi

adb指令

查看设备
adb devices

上传frida
adb push frida-server /data/local/tmp/

权限
adb shell chmod 755 /data/local/tmp/frida-server

执行
adb shell su /data/local/tmp/frida-server &

查看是否运行
ps | grep frida
```

3、转发并启动Frida

```
adb forward tcp:27042 tcp:27042

连接判断：frida-ps -U frida-ps -R
```

## firda绕过方案

### 方案1：Firda+r0capture+WireShark

https://github.com/r0ysue/r0capture

```
模拟器执行：./frida-server

获取包名：ls /data/data 或 Apk资源器查看

python r0capture.py -U -f 包名 -p xxxx.pcap
```

### 方案2：Firda+HOOK-JS+BurpSuite

https://github.com/apkunpacker/FridaScripts

```
模拟器执行：./frida-server

本地执行：frida -U -f 包名 -l hookjs文件
```

## 逆向获取证书方案

### 方案3：导入证书

```

利用场景：能反编译，有证书文件

1、解压获取apk的证书文件

2、反编译后得到证书文件的密钥

3、Burp导入证书后实现对应抓包

```

IOS端-双向认证-SSL Kill switch 2
```

教程地址：https://testerhome.com/topics/17249

GitHub：https://github.com/nabla-c0d3/ssl-kill-switch2/releases
```

# APP-某游戏-单向证书绕过

XP框架&Frida

```
方案1：Xposed+SSL模块+BurpSuite

逍遥模拟器安装xp已经上节课讲过

夜神模拟器安装xp直接市场下载后激活即可
```

```
方案2：Firda+HOOK-JS+BurpSuite

模拟器执行：./frida-server

本地执行：frida -U -f 包名 -l hookjs文件
```