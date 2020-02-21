# Scanner类、Random类、ArrayList类

## Scanner类

一个可以解析基本类型和字符串的简单文本扫描器

```java
import java.util.Scanner;
public class Test01Scanner {
public static void main(String[] args) {
    // 创建对象
    Scanner sc = new Scanner(System.in);
    // 接收数据
    System.out.println("请输入第一个数据：");
    int a = sc.nextInt();
    System.out.println("请输入第二个数据：");
    int b = sc.nextInt();
    // 对数据进行求和
    int sum = a + b;
       System.out.println("sum:" + sum);
    }
}
```

!>public int nextInt() ：将输入信息的下一个标记扫描为一个 int 值。

## Random类

此类的实例用于生成伪随机数。

```java
// 导包
import java.util.Random;
public class Test01Random {
public static void main(String[] args) {
int n = 50;
// 创建对象
Random r = new Random();
// 获取随机数
int number = r.nextInt(n) + 1;
// 输出随机数
System.out.println("number:" + number);
}
}
```

## ArrayList类

1. java.util.ArrayList 是大小可变的数组的实现，存储在内的数据称为元素。此类提供一些方法来操作内部存储
   的元素。 ArrayList 中可不断添加元素，其大小也自动增长。
2. java.util.ArrayList <E> ，表示一种指定的数据类型，叫做泛型。E ，取自Element（元素）的首字母。在出现E 的地方，我们使
   用一种引用数据类型将其替换即可，表示我们将存储哪种引用类型的元素

### 常用方法

对于元素的操作,基本体现在——增、删、查。常用的方法有：
public boolean add(E e) ：将指定的元素添加到此集合的尾部。
public E remove(int index) ：移除此集合中指定位置上的元素。返回被删除的元素。
public E get(int index) ：返回此集合中指定位置上的元素。返回获取的元素。
public int size() ：返回此集合中的元素数。遍历集合时，可以控制索引范围，防止越界。

### 如何存储基本数据类型

ArrayList对象不能存储基本类型，只能存储引用类型的数据。类似<int> 不能写，但是存储基本数据类型对应的
包装类型是可以的。所以，想要存储基本类型数据， <> 中的数据类型，必须转换后才能编写，转换写法如下

基本类型基本类型包装类
![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200220115317.png)