# 基本概念、入口、请求参数的绑定、**常用注解**

## 第一章：三层架构和MVC

### 三层架构

1. 咱们开发服务器端程序，一般都基于两种形式，一种C/S架构程序，一种B/S架构程序
2. 使用Java语言基本上都是开发B/S架构的程序，B/S架构又分成了三层架构
3. 三层架构
   2. 表现层：WEB层，用来和客户端进行数据交互的。表现层一般会采用MVC的设计模型
   3. 业务层：处理公司具体的业务
   3. 持久层：用来操作数据库的

### MVC模型

1. MVC全名是Model View Controller 模型视图控制器，每个部分各司其职。

2. Model：数据模型，JavaBean的类，用来进行数据封装。

3. View：指JSP、HTML用来展示数据给用户

4. Controller：用来接收用户的请求，整个流程的控制器。用来进行数据校验等。


### SpringMVC的概述

 1. 是一种基于Java实现的MVC设计模型的请求驱动类型的轻量级WEB框架。
  2. Spring MVC属于SpringFrameWork的后续产品，已经融合在Spring Web Flow里面。Spring 框架提供了构建 Web 应用程序的全功能 MVC 模块。
 3. 使用 Spring 可插入的 MVC 架构，从而在使用Spring进行WEB开发时，可以选择使用Spring的
    SpringMVC框架或集成其他MVC开发框架，如Struts1(现在一般不用)，Struts2等。

### SpringMVC在三层架构中的位置

表现层框架

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200509082434.png)

### SpringMVC的优势

1、清晰的角色划分： 前端控制器（DispatcherServlet） 请求到处理器映射（HandlerMapping） 处理器适配器（HandlerAdapter） 视图解析器（ViewResolver） 处理器或页面控制器（Controller） 验证器（ Validator） 命令对象（Command 请求参数绑定到的对象就叫命令对象） 表单对象（Form Object 提供给表单展示和提交到的对象就叫表单对象）。 

2、分工明确，而且扩展点相当灵活，可以很容易扩展，虽然几乎不需要。 

3、由于命令对象就是一个POJO，无需继承框架特定API，可以使用命令对象直接作为业务对象。

 4、和Spring 其他框架无缝集成，是其它Web框架所不具备的。 

5、可适配，通过HandlerAdapter可以支持任意的类作为处理器。 

6、可定制性，HandlerMapping、ViewResolver等能够非常简单的定制。 

7、功能强大的数据验证、格式化、绑定机制。

 8、利用Spring提供的Mock对象能够非常简单的进行Web层单元测试。

 9、本地化、主题的解析的支持，使我们更容易进行国际化和主题的切换。 

10、强大的JSP标签库，使JSP编写更容易。 ………………还有比如RESTful风格的支持、简单的文件上传、约定大于配置的契约式编程支持、基于注解的零配置支持等等。

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200509082733.png)

## SpringMVC的入门程序

### 1. 创建WEB工程，引入开发的jar包

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <spring.version>5.2.6.RELEASE</spring.version>
  </properties>

  <dependencies>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-web</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>servlet-api</artifactId>
      <version>2.5</version>
      <scope>provided</scope>
    </dependency>
    <dependency>
      <groupId>javax.servlet.jsp</groupId>
      <artifactId>jsp-api</artifactId>
      <version>2.0</version>
      <scope>provided</scope>
    </dependency>

  </dependencies>
```

### 2配置核心的控制器（配置DispatcherServlet）

- 1. 在web.xml配置文件中核心控制器DispatcherServlet

```xml
<!DOCTYPE web-app PUBLIC
        "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
        "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
    <display-name>Archetype Created Web Application</display-name>
    <!-- 配置spring mvc的核心控制器 -->
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!-- 配置Servlet的初始化参数，读取springmvc的配置文件，创建spring容器 -->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springMVC.xml</param-value>
        </init-param>
        <!-- 配置servlet启动时加载对象 -->
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>

```

### 3. 编写springmvc.xml的配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans.xsd
http://www.springframework.org/schema/mvc
http://www.springframework.org/schema/mvc/spring-mvc.xsd
http://www.springframework.org/schema/context
http://www.springframework.org/schema/context/spring-context.xsd">
    <!-- 配置spring创建容器时要扫描的包 -->
    <context:component-scan base-package="com.mayday"/>
    <!-- 配置视图解析器 -->
    <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/pages/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

    <!-- 配置spring开启注解mvc的支持-->
    <mvc:annotation-driven/>
</beans>
```

### 4. 编写index.jsp和HelloController控制器类

```jsp
<body>
        <h3>入门程序</h3>
        <a href="hello">入门案例</a>
    </body>
```

```java
package com.mayday;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

/**
 * @description:控制器
 * @author: MayDay
 * @time: 2020/5/9 19:06
 */
@Controller
public class HelloController {
    @RequestMapping(path = "/hello")
    public String sayHello() {
        System.out.println("Hello StringMVC");
        return "success";
    }
}

```

### 5. 在WEB-INF目录下创建pages文件夹，编写success.jsp的成功页面

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200509194258.png)

### 6. 启动Tomcat服务器，进行测试

## 3. 入门案例的执行过程分析

1. 入门案例的执行流程
	1. 当启动Tomcat服务器的时候，因为配置了load-on-startup标签，所以会创建DispatcherServlet对象，
	就会加载springmvc.xml配置文件
	2. 开启了注解扫描，那么HelloController对象就会被创建
	3. 从index.jsp发送请求，请求会先到达DispatcherServlet核心控制器，根据配置@RequestMapping注解
	找到执行的具体方法
	4. 根据执行方法的返回值，再根据配置的视图解析器，去指定的目录下查找指定名称的JSP文件
	5. Tomcat服务器渲染页面，做出响应
	
2. SpringMVC官方提供图形

   ![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200509195020.png)

3. 入门案例中的组件分析
  1. 前端控制器（DispatcherServlet）
  2. 处理器映射器（HandlerMapping）
  3. 处理器（Handler）
  4. 处理器适配器（HandlAdapter）
  5. 视图解析器（View Resolver）
  6. 视图（View）

## RequestMapping注解

1. RequestMapping注解的作用是建立请求URL和处理方法之间的对应关系
2. RequestMapping注解可以作用在方法和类上
	1. 作用在类上：第一级的访问目录
	2. 作用在方法上：第二级的访问目录
	3. 细节：路径可以不编写 / 表示应用的根目录开始
	4. 细节：${ pageContext.request.contextPath }也可以省略不写，但是路径上不能写 /
3. RequestMapping的属性
	1. path 指定请求路径的url
	2. value value属性和path属性是一样的
	3. mthod 指定该方法的请求方式
	4. params 指定限制请求参数的条件
	5. headers 发送的请求中必须包含的请求头

## 第三章：请求参数的绑定

1. 请求参数的绑定说明
	1. 绑定机制
		1. 表单提交的数据都是k=v格式的 username=haha&password=123
		2. SpringMVC的参数绑定过程是把表单提交的请求参数，作为控制器中方法的参数进行绑定的
		3. 要求：提交表单的name和参数的名称是相同的
	2. 支持的数据类型
		1. 基本数据类型和字符串类型
		2. 实体类型（JavaBean）
		3. 集合数据类型（List、map集合等）
2. 基本数据类型和字符串类型
	1. 提交表单的name和参数的名称是相同的
	
	2. 区分大小写
	
	3. 实体类型（JavaBean）
		1. 提交表单的name和JavaBean中的属性名称需要一致
		2. 如果一个JavaBean类中包含其他的引用类型，那么表单的name属性需要编写成：对象.属性 例如：
		address.name
		
	4. 给集合属性数据封装
	
	   1. JSP页面编写方式：list[0].属性
	
	      5.请求参数中文乱码的解决

```xml
	<!-- 配置过滤器，解决中文乱码的问题 -->
<filter>
<filter-name>characterEncodingFilter</filter-name>
<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filterclass>
<!-- 指定字符集 -->
<init-param>
<param-name>encoding</param-name>
<param-value>UTF-8</param-value>
</init-param>
</filter>
<filter-mapping>
<filter-name>characterEncodingFilter</filter-name>
<url-pattern>/*</url-pattern>
</filter-mapping>
```

## 第四章：常用的注解

### @RequestParam注解

1. 作用：把请求中的指定名称的参数传递给控制器中的形参赋值
2. 属性
	1. value：请求参数中的名称
	2. required：请求参数中是否必须提供此参数，默认值是true，必须提供

```java
/**
* 接收请求
* @return
*/
@RequestMapping(path="/hello")
public String sayHello(@RequestParam(value="username",required=false)String name) {
	System.out.println("aaaa");
	System.out.println(name);
	return "success";
}
```

### @RequestBody注解

1. 作用：用于获取请求体的内容（注意：get方法不可以）
2. 属性
	1. required：是否必须有请求体，默认值是true

```java
/**
* 接收请求
* @return
*/
@RequestMapping(path="/hello")
public String sayHello(@RequestBody String body) {
	System.out.println("aaaa");
	System.out.println(body);
	return "success";
}
```

### @PathVariable注解

1. 作用：拥有绑定url中的占位符的。例如：url中有/delete/{id}，{id}就是占位符
2. 属性
	1. value：指定url中的占位符名称
3. Restful风格的URL
	1. 请求路径一样，可以根据不同的请求方式去执行后台的不同方法
	2. restful风格的URL优点
		1. 结构清晰
		2. 符合标准
		3. 易于理解
		4. 扩展方便

```java
<a href="user/hello/1">入门案例</a>
/**
* 接收请求
* @return
*/
@RequestMapping(path="/hello/{id}")
public String sayHello(@PathVariable(value="id") String id) {
	System.out.println(id);
	return "success";
}	
```

### @RequestHeader注解

1. 作用：获取指定请求头的值
2. 属性
	1. value：请求头的名称

```java
@RequestMapping(path="/hello")
public String sayHello(@RequestHeader(value="Accept") String header) {
	System.out.println(header);
	return "success";
}
```

###  @CookieValue注解

1. 作用：用于获取指定cookie的名称的值
2. 属性
	1. value：cookie的名称

```java
@RequestMapping(path="/hello")
public String sayHello(@CookieValue(value="JSESSIONID") String cookieValue) {
	System.out.println(cookieValue);
	return "success";
}
```

### @ModelAttribute注解

1. 作用
	1. 出现在方法上：表示当前方法会在控制器方法执行前线执行。
	2. 出现在参数上：获取指定的数据给参数赋值。
2. 应用场景
	1. 当提交表单数据不是完整的实体数据时，保证没有提交的字段使用数据库原来的数据。

### @SessionAttributes注解

1. 作用：用于多次执行控制器方法间的参数共享
2. 属性
	1. value：指定存入属性的名称
	2. type：用于指定存入的数据类型。

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200529160317.png)