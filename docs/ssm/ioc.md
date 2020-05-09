# 基于注解的IOC和ioc案例

## ioc注解分类

**用于创建对象的**

​	他们的作用就和在XML配置文件中编写一个<bean>标签实现的功能是一样的

> ​	Component:
>
> 相当于：`<bean id="" class="">`
>
>  * 作用：用于把当前类对象存入spring容器中
>
>  * 属性：
>
>  * value：用于指定bean的id。当我们不写时，它的默认值是当前类名，且首字母改小写。
>
>    Controller：一般用在表现层
>
>    Service：一般用在业务层
>
>    Repository：一般用在持久层
>
>  * 以上三个注解他们的作用和属性与Component是一模一样。
>
>  *      他们三个是spring框架为我们提供明确的三层使用的注解，使我们的三层对象更加清晰

!>如果注解中有且只有一个属性要赋值时，且名称是value，value在赋值是可以不写

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

    <!--告知spring在创建容器时要扫描的包，配置所需要的标签不是在beans的约束中，而是一个名称为
    context名称空间和约束中-->
    <context:component-scan base-package="com.itheima"></context:component-scan>
</beans>
```



**用于注入数据的**

​	他们的作用就和在xml配置文件中的bean标签中写一个<property>标签的作用是一样的

> ​	**Autowired:**
>
> 相当于：`<property name="" ref="">`
> `<property name="" value="">`
>
>  * 作用：自动按照类型注入。只要容器中有唯一的一个bean对象类型和要注入的变量类型匹配，就可以注入成功
>
>  * 如果ioc容器中没有任何bean的类型和要注入的变量类型匹配，则报错。
>
>  * 如果Ioc容器中有多个类型匹配时：
>
>  * 出现位置：
>
>  * 可以是变量上，也可以是方法上
>
>  * 细节：
>
>  * 在使用注解注入时，set方法就不是必须的了。
>
>    **Qualifier:**
>
>  * 作用：在按照类中注入的基础之上再按照名称注入。它在给类成员注入时不能单独使用。但是在给方法参数注入时可以（稍后我们讲）
>
>  * 属性：
>
>  * value：用于指定注入bean的id。
>
>    **Resource**
>
>  * 作用：直接按照bean的id注入。它可以独立使用
>
>  * 属性：name：用于指定bean的id。
>
>  * 以上三个注入都只能注入其他bean类型的数据，而基本类型和String类型无法使用上述注解实现。
>
>  * 另外，集合类型的注入只能通过XML来实现。
>
>    **Value**
>
>  * 作用：用于注入基本类型和String类型的数据
>
>  * 属性：
>
>  * value：用于指定数据的值。它可以使用spring中SpEL(也就是spring的el表达式）
>
>  *                      SpEL的写法：${表达式}

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200508084026.png)

!>多个实现类匹配类型注入则报错，可以改变变量名称。查找方式先找类型再找变量。`@Repository("accountDao")`和`@Repository("accountDao2")`的处理`private IAccountDao accountDao2;`这样的方式是可以的。等同于`@Resource(name = "accountDao2")`

**用于改变作用范围的**

 * 他们的作用就和在bean标签中使用scope属性实现的功能是一样的

   相当于：`<bean id="" class="" scope="">`

> Scope
>  *          作用：用于指定bean的作用范围
>  *          属性：
>  *              value：指定范围的取值。常用取值：singleton prototype

!>`@Scope("prototype")`不写表示单例模式

**和生命周期相关** 

 *      他们的作用就和在bean标签中使用init-method和destroy-methode的作用是一样的
 *      相当于：`<bean id="" class="" init-method="" destroy-method="" />`

>  **PreDestroy**
>  * 作用：用于指定销毁方法
>
>    **PostConstruct**
>
>  *          作用：用于指定初始化方法

```java
package com.itheima.service.impl;

import com.itheima.dao.IAccountDao;
import com.itheima.service.IAccountService;
import org.springframework.stereotype.Service;

import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;
import javax.annotation.Resource;

/**
 * 账户的业务层实现类
 *
 * 曾经XML的配置：
 *  <bean id="accountService" class="com.itheima.service.impl.AccountServiceImpl"
 *        scope=""  init-method="" destroy-method="">
 *      <property name=""  value="" | ref=""></property>
 *  </bean>
 *
 * 用于创建对象的
 *      他们的作用就和在XML配置文件中编写一个<bean>标签实现的功能是一样的
 *      Component:
 *          作用：用于把当前类对象存入spring容器中
 *          属性：
 *              value：用于指定bean的id。当我们不写时，它的默认值是当前类名，且首字母改小写。
 *      Controller：一般用在表现层
 *      Service：一般用在业务层
 *      Repository：一般用在持久层
 *      以上三个注解他们的作用和属性与Component是一模一样。
 *      他们三个是spring框架为我们提供明确的三层使用的注解，使我们的三层对象更加清晰
 *
 *
 * 用于注入数据的
 *      他们的作用就和在xml配置文件中的bean标签中写一个<property>标签的作用是一样的
 *      Autowired:
 *          作用：自动按照类型注入。只要容器中有唯一的一个bean对象类型和要注入的变量类型匹配，就可以注入成功
 *                如果ioc容器中没有任何bean的类型和要注入的变量类型匹配，则报错。
 *                如果Ioc容器中有多个类型匹配时：
 *          出现位置：
 *              可以是变量上，也可以是方法上
 *          细节：
 *              在使用注解注入时，set方法就不是必须的了。
 *      Qualifier:
 *          作用：在按照类中注入的基础之上再按照名称注入。它在给类成员注入时不能单独使用。但是在给方法参数注入时可以（稍后我们讲）
 *          属性：
 *              value：用于指定注入bean的id。
 *      Resource
 *          作用：直接按照bean的id注入。它可以独立使用
 *          属性：
 *              name：用于指定bean的id。
 *      以上三个注入都只能注入其他bean类型的数据，而基本类型和String类型无法使用上述注解实现。
 *      另外，集合类型的注入只能通过XML来实现。
 *
 *      Value
 *          作用：用于注入基本类型和String类型的数据
 *          属性：
 *              value：用于指定数据的值。它可以使用spring中SpEL(也就是spring的el表达式）
 *                      SpEL的写法：${表达式}
 *
 * 用于改变作用范围的
 *      他们的作用就和在bean标签中使用scope属性实现的功能是一样的
 *      Scope
 *          作用：用于指定bean的作用范围
 *          属性：
 *              value：指定范围的取值。常用取值：singleton prototype
 *
 * 和生命周期相关 了解
 *      他们的作用就和在bean标签中使用init-method和destroy-methode的作用是一样的
 *      PreDestroy
 *          作用：用于指定销毁方法
 *      PostConstruct
 *          作用：用于指定初始化方法
 */
@Service("accountService")
//@Scope("prototype")
public class AccountServiceImpl implements IAccountService {

//    @Autowired
//    @Qualifier("accountDao1")
    @Resource(name = "accountDao2")
    private IAccountDao accountDao = null;

    @PostConstruct
    public void  init(){
        System.out.println("初始化方法执行了");
    }

    @PreDestroy
    public void  destroy(){
        System.out.println("销毁方法执行了");
    }

    public void  saveAccount(){
        accountDao.saveAccount();
    }
}

```

## 案例（参考

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">
    <!-- 配置Service -->
    <bean id="accountService" class="com.itheima.service.impl.AccountServiceImpl">
        <!-- 注入dao -->
        <property name="accountDao" ref="accountDao"></property>
    </bean>

    <!--配置Dao对象-->
    <bean id="accountDao" class="com.itheima.dao.impl.AccountDaoImpl">
        <!-- 注入QueryRunner -->
        <property name="runner" ref="runner"></property>
    </bean>

    <!--配置QueryRunner-->
    <bean id="runner" class="org.apache.commons.dbutils.QueryRunner" scope="prototype">
        <!--注入数据源-->
        <constructor-arg name="ds" ref="dataSource"></constructor-arg>
    </bean>

    <!-- 配置数据源 -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <!--连接数据库的必备信息-->
        <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/eesy"></property>
        <property name="user" value="root"></property>
        <property name="password" value="1234"></property>
    </bean>
</beans>
```

注解改造：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 告知spring在创建容器时要扫描的包 -->
    <context:component-scan base-package="com.itheima"></context:component-scan>
    <!--配置QueryRunner-->
    <bean id="runner" class="org.apache.commons.dbutils.QueryRunner" scope="prototype">
        <!--注入数据源-->
        <constructor-arg name="ds" ref="dataSource"></constructor-arg>
    </bean>

    <!-- 配置数据源 -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <!--连接数据库的必备信息-->
        <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/eesy"></property>
        <property name="user" value="root"></property>
        <property name="password" value="1234"></property>
    </bean>
</beans>
```

## 新注解说明

### @Configuration

作用：

 用于指定当前类是一个spring配置类，当创建容器时会从该类上加载注解。获取容器时需要使用AnnotationApplicationContext(有@Configuration注解的类.class)。

 属性： value:用于指定配置类的字节码

### @ComponentScan

作用： 

用于指定spring在初始化容器时要扫描的包。作用和在spring的xml配置文件中的： <context:component-scan base-package="com.itheima"/>是一样的。 

属性： basePackages：用于指定要扫描的包。和该注解中的value属性作用一样。

### @Bean

作用： 该注解只能写在方法上，表明使用此方法创建一个对象，并且放入spring容器。 

属性： name：给当前@Bean注解方法创建的对象指定一个名称(即bean的id）。

### @PropertySource

作用：用于加载.properties文件中的配置。例如我们配置数据源时，可以把连接数据库的信息写到properties配置文件中，就可以使用此注解指定properties配置文件的位置。 

属性： value[]：用于指定properties文件位置。如果是在类路径下，需要写上classpath:

### @Import

作用： 用于导入其他配置类，在引入其他配置类时，可以不用再写@Configuration注解。当然，写上也没问题。 

属性： value[]：用于指定其他配置类的字节码。

通过注解获取容器：`ApplicationContext ac = new AnnotationConfigApplicationContext(SpringConfiguration.class);`

```java
package config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Import;
import org.springframework.context.annotation.PropertySource;

/**
 * 该类是一个配置类，它的作用和bean.xml是一样的
 * spring中的新注解
 * Configuration
 *     作用：指定当前类是一个配置类
 *     细节：当配置类作为AnnotationConfigApplicationContext对象创建的参数时，该注解可以不写。
 * ComponentScan
 *      作用：用于通过注解指定spring在创建容器时要扫描的包
 *      属性：
 *          value：它和basePackages的作用是一样的，都是用于指定创建容器时要扫描的包。
 *                 我们使用此注解就等同于在xml中配置了:
 *                      <context:component-scan base-package="com.itheima"></context:component-scan>
 *  Bean
 *      作用：用于把当前方法的返回值作为bean对象存入spring的ioc容器中
 *      属性:
 *          name:用于指定bean的id。当不写时，默认值是当前方法的名称
 *      细节：
 *          当我们使用注解配置方法时，如果方法有参数，spring框架会去容器中查找有没有可用的bean对象。
 *          查找的方式和Autowired注解的作用是一样的
 *  Import
 *      作用：用于导入其他的配置类
 *      属性：
 *          value：用于指定其他配置类的字节码。
 *                  当我们使用Import的注解之后，有Import注解的类就父配置类，而导入的都是子配置类
 *  PropertySource
 *      作用：用于指定properties文件的位置
 *      属性：
 *          value：指定文件的名称和路径。
 *                  关键字：classpath，表示类路径下
 */
//@Configuration
@ComponentScan("com.itheima")
@Import(JdbcConfig.class)
@PropertySource("classpath:jdbcConfig.properties")
public class SpringConfiguration {


}

```

