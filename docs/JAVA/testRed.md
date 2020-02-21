# 发红包实例

## 题目

群主发普通红包。某群有多名成员，群主给成员发普通红包。普通红包的规则：
1. 群主的一笔金额，从群主余额中扣除，平均分成n等份，让成员领取。
2. 成员领取红包后，保存到成员余额中。
请根据描述，完成案例中所有类的定义以及指定类之间的继承关系，并完成发红包的操作。

## 分析

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200220212702.png)

## 实战

### 1.manage类

```java
package com.mayday.red;

import java.util.ArrayList;

//群主类
public class Manager extends User {

    public Manager() {
    }

    public Manager(String name, int money) {
        super(name, money);
    }

    public ArrayList<Integer> send(int totalMoney, int count) {
        //首先需要一个集合，用来存储若干个红包的集合
        ArrayList<Integer> redList = new ArrayList<>();

        //首先看一下群主有多少钱
        int leftMoney = super.getMoney();
        if (totalMoney > leftMoney) {
            System.out.println("余额不足");
            return redList;
        }

        //扣钱,就是重新设置余额
        super.setMoney(leftMoney - totalMoney);

        //发红包需要平均分成若干份
        int avg = totalMoney / count;
        int mod = totalMoney % count;
        //剩下的零头放在最后一个红包里
        for (int i = 0; i < count - 1; i++) {
            redList.add(avg);
        }
        redList.add(avg + mod);
        return redList;
    }
}
```

### 2.member类

```java
package com.mayday.red;

import java.util.ArrayList;
import java.util.Random;

//普通成员类
public class Member extends User {

    public Member(String name, int money) {
        super(name, money);
    }

    public Member() {
    }

    public void receive(ArrayList<Integer> list) {
        //随机获取一个集合当中的索引编号
        int index = new Random().nextInt(list.size());
        //根据索引，从集合中删除，并且删除的红包给自己
        int delta = list.remove(index);
        super.setMoney(super.getMoney() + delta);
    }
}
```

### 3.user类

```java
package com.mayday.red;

/**
 * 群主发普通红包。某群有多名成员，群主给成员发普通红包。普通红包的规则：
 * 1. 群主的一笔金额，从群主余额中扣除，平均分成n等份，让成员领取。
 * 2. 成员领取红包后，保存到成员余额中。
 * 请根据描述，完成案例中所有类的定义以及指定类之间的继承关系，并完成发红包的操作。
 */
//用户类
public class User {

    private String name;//姓名
    private int money;//余额，也就是当前用户拥有的钱数

    //有参构造
    public User(String name, int money) {
        this.name = name;
        this.money = money;
    }

    //无参构造
    public User() {
    }

    //展示当前用户还有多少钱
    public void show() {
        System.out.println("我叫: " + name + ", 我有多少钱：" + money);
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getMoney() {
        return money;
    }

    public void setMoney(int money) {
        this.money = money;
    }
}
```

### 4.执行方法main

```java
package com.mayday.red;

import java.util.ArrayList;

public class MainRed {

    public static void main(String[] args) {
        Manager manager = new Manager("群主", 100);

        Member one = new Member("成员A", 0);
        Member two = new Member("成员B", 0);
        Member three = new Member("成员C", 0);

        manager.show();
        one.show();
        two.show();
        three.show();
        System.out.println("==================");

        ArrayList<Integer> redList = manager.send(20, 3);
        one.receive(redList);
        two.receive(redList);
        three.receive(redList);

        manager.show();
        one.show();
        two.show();
        three.show();
    }
}
```