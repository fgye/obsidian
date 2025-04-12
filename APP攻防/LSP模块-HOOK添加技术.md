
---
➢ APK逆向-反编译&LSP&HOOK-绕检测

➢ APK逆向-反编译&LSP&HOOK-加密算法

➢ APK逆向-反编译&LSP&HOOK-逻辑验证

---
# 反编译&LSP&HOOK-绕检测

```java
private final void K() {

LogUtils.b("niming", "===AppUtils.isAppRoot():" + com.blankj.utilcode.util.c.r() + " DeviceUtils.isDeviceRooted() " + w.q());

if (!A() && !m.a(this.activity)) {

	if (a((Context) this)) {
		h("检测到您使用了代理软件,不允许继续使用");
		return;
	}
	h.a().a(com.niming.weipa.b.a.n, "");
	x();
	return;
}

h("检测到您使用的是模拟器或者设备已经root,不允许继续使用");

}
```

```
1、模拟器：goto :end end
```

```
2、mg+lsp+算法组手 自带屏蔽hook
```

```
3、代理检测：postern
```

## HOOK

```
1、com.blankj.utilcode.util.c

r -> 0

2、com.blankj.utilcode.util.w

q -> false
```

# LSP-HOOK-逻辑验证

屏蔽掉/api/mv/watching接口调用

```
让user.getIs_vip 永远返回true

getIsvip - >1 const/4 v0, 0x1

getVip_level ->4 const/4 v0, 4
```

```
if (userBean.getIs_vip() == 1) {

String format = String.format("到期時間：%s", c.d.a.f.i.a(userBean.getExpired_str()));

if (userBean.getVip_level() == 4) {

format = "永久無限觀看";

} else {

this.v.setVisibility(0);
```

## HOOK

```
1、com.blmvl.blvl.bean.UserBean

getIsvip ->1

2、com.blmvl.blvl.bean.UserBean

getVip_level ->4
```

# LSP-HOOK-加密算法

HOOK：mg+lsp+算法组手 算法+逆向=找解密