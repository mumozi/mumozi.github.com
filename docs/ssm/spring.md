# Spring理论

## spring是什么
​		Spring是分层的Java SE/EE应用 full-stack轻量级开源框架，以**IoC（Inverse Of Control：反转控制）**和**AOP（Aspect Oriented Programming：面向切面编程）**为内核，提供了展现层Spring MVC和持久层Spring JDBC以及业务层事务管理等众多的企业级应用技术，还能整合开源世界众多著名的第三方框架和类库，逐渐成为使用最多的Java EE企业应用开源框架。

## spring的优势

- 方便解耦，简化开发
- AOP编程的支持
- 声明式事务的支持
- 方便程序的测试
- 方便集成各种优秀框架
- 降低JavaEE API的使用难度
- Java源码是经典学习范例

## spring的体系结构

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200507112024.png)

## BeanFactory 和ApplicationContext 的区别

BeanFactory 才是Spring 容器中的顶层接口。
ApplicationContext 是它的子接口。
BeanFactory 和ApplicationContext 的区别：
创建对象的时间点不一样。
**ApplicationContext**：只要一读取配置文件，默认情况下就会创建对象。
**BeanFactory**：什么使用什么时候创建对象。

## ApplicationContext 接口的实现类

ClassPathXmlApplicationContext：
它是从类的根路径下加载配置文件 推荐使用这种
FileSystemXmlApplicationContext：
它是从磁盘路径上加载配置文件，配置文件可以在磁盘的任意位置。
AnnotationConfigApplicationContext:
当我们使用注解配置容器对象时，需要使用此类来创建spring 容器。它用来读取注解。

## IOC中bean标签和管理对象细节

### bean标签

- 作用： 用于配置对象让spring来创建的。 默认情况下它调用的是类中的无参构造函数。如果没有无参构造函数则不能创建成功。 

- 属性：
  -  id：给对象在容器中提供一个唯一标识。用于获取对象。
  - class：指定类的全限定类名。用于反射创建对象。默认情况下调用无参构造函数。
  -  scope：指定对象的作用范围。
    -  * singleton :默认值，单例的. * prototype :多例的. * request :WEB项目中,Spring创建一个Bean的对象,将对象存入到request域中. * session :WEB项目中,Spring创建一个Bean的对象,将对象存入到session域中. * global session :WEB项目中,应用在Portlet环境.如果没有Portlet环境那么globalSession相当于session. init-method：指定类中的初始化方法名称。 destroy-method：指定类中销毁方法名称。

### bean的作用范围和生命周期

​		单例对象：scope="singleton"

 一个应用只有一个对象的实例。它的作用范围就是整个引用。 生命周期： 对象出生：当应用加载，创建容器时，对象就被创建了。 对象活着：只要容器在，对象一直活着。 对象死亡：当应用卸载，销毁容器时，对象就被销毁了。

 		多例对象：scope="prototype" 

每次访问对象时，都会重新创建对象实例。 生命周期： 对象出生：当使用对象时，创建新的对象实例。 对象活着：只要对象在使用中，就一直活着。 对象死亡：当对象长时间不用时，被java的垃圾回收器回收了。

### 实例化Bean的三种方式

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--把对象的创建交给spring来管理-->
    <!--spring对bean的管理细节
        1.创建bean的三种方式
        2.bean对象的作用范围
        3.bean对象的生命周期
    -->

    <!--创建Bean的三种方式 -->
    <!-- 第一种方式：使用默认构造函数创建。
            在spring的配置文件中使用bean标签，配以id和class属性之后，且没有其他属性和标签时。
            采用的就是默认构造函数创建bean对象，此时如果类中没有默认构造函数，则对象无法创建。

    <bean id="accountService" class="com.itheima.service.impl.AccountServiceImpl"></bean>
    -->

    <!-- 第二种方式： 使用普通工厂中的方法创建对象（使用某个类中的方法创建对象，并存入spring容器）
    <bean id="instanceFactory" class="com.itheima.factory.InstanceFactory"></bean>
    <bean id="accountService" factory-bean="instanceFactory" factory-method="getAccountService"></bean>
    -->

    <!-- 第三种方式：使用工厂中的静态方法创建对象（使用某个类中的静态方法创建对象，并存入spring容器)
    <bean id="accountService" class="com.itheima.factory.StaticFactory" factory-method="getAccountService"></bean>
    -->

    <!-- bean的作用范围调整
        bean标签的scope属性：
            作用：用于指定bean的作用范围
            取值： 常用的就是单例的和多例的
                singleton：单例的（默认值）
                prototype：多例的
                request：作用于web应用的请求范围
                session：作用于web应用的会话范围
                global-session：作用于集群环境的会话范围（全局会话范围），当不是集群环境时，它就是session

    <bean id="accountService" class="com.itheima.service.impl.AccountServiceImpl" scope="prototype"></bean>
    -->

    <!-- bean对象的生命周期
            单例对象
                出生：当容器创建时对象出生
                活着：只要容器还在，对象一直活着
                死亡：容器销毁，对象消亡
                总结：单例对象的生命周期和容器相同
            多例对象
                出生：当我们使用对象时spring框架为我们创建
                活着：对象只要是在使用过程中就一直活着。
                死亡：当对象长时间不用，且没有别的对象引用时，由Java的垃圾回收器回收
     -->
    <bean id="accountService" class="com.itheima.service.impl.AccountServiceImpl"
          scope="prototype" init-method="init" destroy-method="destroy"></bean>
</beans>
```

## spring的依赖注入

1. 依赖注入的概念
2. 构造函数注入
3. set方法注入
4. 使用p名称空间注入数据（本质还是调用set方法）

```XML
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- spring中的依赖注入
        依赖注入：
            Dependency Injection
        IOC的作用：
            降低程序间的耦合（依赖关系）
        依赖关系的管理：
            以后都交给spring来维护
        在当前类需要用到其他类的对象，由spring为我们提供，我们只需要在配置文件中说明
        依赖关系的维护：
            就称之为依赖注入。
         依赖注入：
            能注入的数据：有三类
                基本类型和String
                其他bean类型（在配置文件中或者注解配置过的bean）
                复杂类型/集合类型
             注入的方式：有三种
                第一种：使用构造函数提供
                第二种：使用set方法提供
                第三种：使用注解提供（明天的内容）
     -->


    <!--构造函数注入：
        使用的标签:constructor-arg
        标签出现的位置：bean标签的内部
        标签中的属性
            type：用于指定要注入的数据的数据类型，该数据类型也是构造函数中某个或某些参数的类型
            index：用于指定要注入的数据给构造函数中指定索引位置的参数赋值。索引的位置是从0开始
            name：用于指定给构造函数中指定名称的参数赋值                                        常用的
            =============以上三个用于指定给构造函数中哪个参数赋值===============================
            value：用于提供基本类型和String类型的数据
            ref：用于指定其他的bean类型数据。它指的就是在spring的Ioc核心容器中出现过的bean对象

        优势：
            在获取bean对象时，注入数据是必须的操作，否则对象无法创建成功。
        弊端：
            改变了bean对象的实例化方式，使我们在创建对象时，如果用不到这些数据，也必须提供。
    -->
    <bean id="accountService" class="com.itheima.service.impl.AccountServiceImpl">
        <constructor-arg name="name" value="泰斯特"></constructor-arg>
        <constructor-arg name="age" value="18"></constructor-arg>
        <constructor-arg name="birthday" ref="now"></constructor-arg>
    </bean>

    <!-- 配置一个日期对象 -->
    <bean id="now" class="java.util.Date"></bean>



    <!-- set方法注入                更常用的方式
        涉及的标签：property
        出现的位置：bean标签的内部
        标签的属性
            name：用于指定注入时所调用的set方法名称
            value：用于提供基本类型和String类型的数据
            ref：用于指定其他的bean类型数据。它指的就是在spring的Ioc核心容器中出现过的bean对象
        优势：
            创建对象时没有明确的限制，可以直接使用默认构造函数
        弊端：
            如果有某个成员必须有值，则获取对象是有可能set方法没有执行。
    -->
    <bean id="accountService2" class="com.itheima.service.impl.AccountServiceImpl2">
        <property name="name" value="TEST" ></property>
        <property name="age" value="21"></property>
        <property name="birthday" ref="now"></property>
    </bean>


    <!-- 复杂类型的注入/集合类型的注入
        用于给List结构集合注入的标签：
            list array set
        用于个Map结构集合注入的标签:
            map  props
        结构相同，标签可以互换
    -->
    <bean id="accountService3" class="com.itheima.service.impl.AccountServiceImpl3">
        <property name="myStrs">
            <set>
                <value>AAA</value>
                <value>BBB</value>
                <value>CCC</value>
            </set>
        </property>

        <property name="myList">
            <array>
                <value>AAA</value>
                <value>BBB</value>
                <value>CCC</value>
            </array>
        </property>

        <property name="mySet">
            <list>
                <value>AAA</value>
                <value>BBB</value>
                <value>CCC</value>
            </list>
        </property>

        <property name="myMap">
            <props>
                <prop key="testC">ccc</prop>
                <prop key="testD">ddd</prop>
            </props>
        </property>

        <property name="myProps">
            <map>
                <entry key="testA" value="aaa"></entry>
                <entry key="testB">
                    <value>BBB</value>
                </entry>
            </map>
        </property>
    </bean>

</beans>
```

