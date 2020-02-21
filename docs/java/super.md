# 继承、super、this、抽象类

## 继承

继承：就是子类继承父类的属性和行为，使得子类对象具有与父类相同的属性、相同的行为。子类可以直接
访问父类中的非私有的属性和行为。

!>子类的每个构造方法中均有默认的super()，调用父类的空参构造。手动调用父类构造会覆盖默认的super()。
super() 和 this() 都必须是在构造方法的第一行，所以不能同时出现。

在每次创建子类对象时，先初始化父类空间，再创建其子类对象本身。目的在于子类对象中包含了其对应的父类空
间，便可以包含其父类的成员，如果父类成员非private修饰，则子类可以随意使用父类成员。代码体现在子类的构
造方法调用时，一定先调用父类的构造方法

!>注意事项：
无论是成员方法还是成员变量，如果没有都是向上找父类，绝对不会向下找子类的。

重写（Override）
概念：在继承关系当中，方法的名称一样，参数列表也一样。

重写（Override）：方法的名称一样，参数列表【也一样】。覆盖、覆写。
重载（Overload）：方法的名称一样，参数列表【不一样】。

方法的覆盖重写特点：创建的是子类对象，则优先用子类方法。

- ### 继承关系中，父子类构造方法的访问特点：

1. 子类构造方法当中有一个默认隐含的“super()”调用，所以一定是先调用的父类构造，后执行的子类构造。
2. 子类构造可以通过super关键字来调用父类重载构造。
3. super的父类构造调用，必须是子类构造方法的第一个语句。不能一个子类构造调用多次super构造。
   总结：
   子类必须调用父类构造方法，不写则赠送super()；写了则用写的指定的super调用，super只能有一个，还必须是第一个。

##  super和this

super关键字的用法有三种：

1. 在子类的成员方法中，访问父类的成员变量。
2. 在子类的成员方法中，访问父类的成员方法。
3. 在子类的构造方法中，访问父类的构造方法。

```java
public class Zi extends Fu {

    int num = 20;

    public Zi() {
        super();
    }

    public void methodZi() {
        System.out.println(super.num); // 父类中的num
    }

    public void method() {
        super.method(); // 访问父类中的method
        System.out.println("子类方法");
    }

}
```

```java
/*
super关键字用来访问父类内容，而this关键字用来访问本类内容。用法也有三种：

1. 在本类的成员方法中，访问本类的成员变量。
2. 在本类的成员方法中，访问本类的另一个成员方法。
3. 在本类的构造方法中，访问本类的另一个构造方法。
在第三种用法当中要注意：
A. this(...)调用也必须是构造方法的第一个语句，唯一一个。
B. super和this两种构造调用，不能同时使用。
 */
public class Zi extends Fu {

    int num = 20;

    public Zi() {
//        super(); // 这一行不再赠送
        this(123); // 本类的无参构造，调用本类的有参构造
//        this(1, 2); // 错误写法！
    }

    public Zi(int n) {
        this(1, 2);
    }

    public Zi(int n, int m) {
    }

    public void showNum() {
        int num = 10;
        System.out.println(num); // 局部变量
        System.out.println(this.num); // 本类中的成员变量
        System.out.println(super.num); // 父类中的成员变量
    }

    public void methodA() {
        System.out.println("AAA");
    }

    public void methodB() {
        this.methodA();
        System.out.println("BBB");
    }

}
```

!>子类的每个构造方法中均有默认的super()，调用父类的空参构造。手动调用父类构造会覆盖默认的super()。
super() 和 this() 都必须是在构造方法的第一行，所以不能同时出现。

## 抽象类

使用abstract 关键字修饰方法，该方法就成了抽象方法，抽象方法只包含一个方法名，而没有方法体。

继承抽象类的子类必须重写父类所有的抽象方法。否则，该子类也必须声明为抽象类。最终，必须有子类实现该父
类的抽象方法，否则，从最初的父类到最终的子类都不能创建对象，失去意义。

如何使用抽象类和抽象方法：
1. 不能直接创建new抽象类对象。
2. 必须用一个子类来继承抽象父类。
3. 子类必须覆盖重写抽象父类当中所有的抽象方法。
覆盖重写（实现）：子类去掉抽象方法的abstract关键字，然后补上方法体大括号。
4. 创建子类对象进行使用。