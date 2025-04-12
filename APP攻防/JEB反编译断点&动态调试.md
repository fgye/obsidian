
---
加密算法还原-逻辑会员绕过

➢ APK逆向-反编译&动态调试-Jeb&Adb

➢ APK逆向-反编译&动态调试-算法分析

➢ APK逆向-反编译&动态调试-会员机制

---

# APK逆向-反编译-动态调试

## Jeb&Adb

0、模拟器开发者模式

```
-启用开发者模式（快速单击5次关于平板电脑版本）

-系统-高级-开发者选项

-启用USB调试模式

-模拟器设置里面开启root及磁盘共享可写入
```

0、Jeb环境配置

```
-安装Jdk11，加入环境变量JAVA_HOME

保证启动Jeb工具时使用的是JDK11即可

-安装ADB，加入环境变量Android

保证启动Jeb工具时使用的是ADB即可
```

Jeb激活：https://www.52pojie.cn/forum.php?mod=viewthread&tid=1598242

1、APP加入动态调试

```
android:debuggable="true"
```

其他方法：https://www.52pojie.cn/thread-1714727-1-1.html

2、端口转发以及开启adb权限

```
MuMu模拟器：adb connect 127.0.0.1:7555

夜神模拟器：adb connect 127.0.0.1:62001

雷电模拟器：adb connect 127.0.0.1:5555

逍遥安卓模拟器：adb connect 127.0.0.1:21503

天天模拟器：adb connect 127.0.0.1:6555

海马玩模拟器：adb connect 127.0.0.1:53001
```

3、debug模式启动APP

```
adb shell am start -D -n com.zj.wuaipojie/.ui.MainActivity

adb shell am start -D -n

adb shell am start -D -n 包名/类名

am start -n 表示启动一个activity

am start -D 表示将应用设置为可调试模式
```



# 动态调试-会员机制

案例：淘小说用户会员制

1、反编译淘小说家调试分析

2、找到判断逻辑修改返回值

案例：嘟嘟牛登录加解密

1、抓包分析嘟嘟牛登录加密数据

2、反编译嘟嘟牛加调试分析算法