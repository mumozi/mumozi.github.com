# SpringBoot整合JPA

**SpringData：**

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200218101957.png)

## 依赖

```xml
<dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
```

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200218102031.png)



## 实体类

```java
package cn.clboy.springbootjpa.entity;


import javax.persistence.*;


@Entity //使用JPA注解配置映射关系,告诉JPA这是一个实体类（和数据表映射的类）
@Table(name = "tbl_user")   //@Table来指定和哪个数据表对应;如果省略默认表名类名小写；
public class User {

    @Id //这是一个主键
    @GeneratedValue(strategy = GenerationType.IDENTITY) //自增主键
    private Integer id;

    @Column(name = "last_name",length = 50) //这是和数据表对应的一个列
    private String lastName;
    @Column //省略默认列名就是属性名
    private String email;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getLastName() {
        return lastName;
    }

    public void setLastName(String lastName) {
        this.lastName = lastName;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }
}

```

## DAO

```java
package cn.clboy.springbootjpa.repository;

import cn.clboy.springbootjpa.entity.User;
import org.springframework.data.jpa.repository.JpaRepository;

/**
 * 继承JpaRepository来完成对数据库的操作
 * 泛型是（实体类，主键）
 */
public interface UserRepository extends JpaRepository<User, Integer> {

}
```

## 配置文件

```yaml
spring:
  datasource:
    username: root
    password: 123456
    url: jdbc:mysql://192.168.179.128:3306/jdbc?useSSL=false&serverTimezone=Asia/Shanghai&characterEncoding=utf-8
    driver-class-name: com.mysql.cj.jdbc.Driver
  jpa:
    #控制台显示sql
    show-sql: true
    hibernate:
      #更新或者创建数据表结构
      ddl-auto: update
```

!>注意观察yml配置文件属性是否写错（易错）

## Controller

```java
package com.mayday.jpa.controller;


import com.mayday.jpa.entity.User;
import com.mayday.jpa.repository.UserRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class UserController {

    @Autowired
    UserRepository userRepository;

    @GetMapping("/user/{id}")
    public User getUser(@PathVariable("id") Integer id) {
        User user = userRepository.getOne(id);
        return user;
    }

    @GetMapping("/user")
    public User insertUser(User user) {
        User save = userRepository.save(user);
        return save;
    }
}

```



添加User，访问：

<http://localhost:8080/user?lastName=zhangsan&email=123456@qq.com>

<http://localhost:8080/user?lastName=lisi&email=78215646@qq.com>

查询用户访问：

<http://localhost:8080/user/1>，然后你就会发现抛出500错误，原因是getOne方法使用的懒加载，获取到的只是代理对象，转换为json时会报错

**解决方法有两种：**

1. 关闭懒加载，在实体类上加`@Proxy(lazy = false)`注解

   ```java
   @Entity
   @Table(name = "tbl_user")
   @Proxy(lazy = false)
   public class User
   ```

2. 转json的时候忽略`hibernateLazyInitializer`和`handler`属性

   ```java
   @Entity
   @Table(name = "tbl_user")
   @JsonIgnoreProperties(value = {"hibernateLazyInitializer", "handler"})
   public class User 
   ```

   !>getOne：当我查询一个不存在的id数据时，直接抛出异常，因为它返回的是一个引用，简单点说就是一个代理对象。所以说，如果想无论如何都有一个返回，那么就用findOne,否则使用getOne。 只是在2.0版本中没有findOne(id) 但是有 findById(id) 只是要稍微处理一下，需要用get()方法。

