
---
1、数据类型
```
boolean
1

byte  short int long
1      2     4   8
float double
4      8
char
2
```

2、输入与输出数据
```java

package cn.hzhsec;  
import java.util.Scanner;  
  
public class Main {  
    public static void main(String[] args) {  
        String t;  
        int num;  
        //new一个对象  
        Scanner sc=new Scanner(System.in);  
        //整形输入  
        num=sc.nextInt();  
        //字符串输入  
        t=sc.next();  
        //输出  
        System.out.println(num);  
        System.out.println(t);  
    }  
}

//输出格式化类似c语言
//%d %c %f %s %md %m.nf
```

3、数组

数组的声明与c语言不同，声明一维数组时不能带数字

```java
package cn.hzhsec;  
import java.util.Arrays;  
import java.util.Scanner;  
  
public class Main {  
    public static void main(String[] args) {  
        int[] a ={2,4,1,5,7,34};  
  
        int[][] b;  
        b=new int[3][4];  
  
        System.out.println(Arrays.toString(a));  
    }  
}
```
