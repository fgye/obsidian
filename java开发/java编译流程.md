
---

1、创建java主类

```java



public class A{
	public static void main(String args[]){
		//无换行
		System.out.print("hello world");
		//有换行
		System.out.println("Hello world");
	}
	class Test{
		void SpeakHello(){
			System.out.print("hello!");
		}
	}
}
```

文件需要保存为A.java
一个源文件最多只有一个public类

2、编译类命令
```
javac A.java
生成两个.class的文件
```
3、执行文件
```

java A

或者

java -cp . A
```