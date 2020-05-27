# 响应数据和结果视图、文件上传、异常处理

## 第一章：响应数据和结果视图

### 返回字符串

Controller方法返回字符串可以指定逻辑视图的名称，根据视图解析器为物理视图的地址。

```java
@RequestMapping(value="/hello")
public String sayHello() {
System.out.println("Hello SpringMVC!!");
// 跳转到XX页面
return "success";
}
```

视图解析器配置

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

### 返回值是void
1. 如果控制器的方法返回值编写成void，执行程序报404的异常，默认查找JSP页面没有找到。
   1. 默认会跳转到@RequestMapping(value="/initUpdate") initUpdate的页面。
2. 可以使用请求转发或者重定向跳转到指定的页面

```java
@RequestMapping(value="/initAdd")
public void initAdd(HttpServletRequest request,HttpServletResponse response) throws Exception {
	System.out.println("请求转发或者重定向");
	// 请求转发
	// request.getRequestDispatcher("/WEB-INF/pages/add.jsp").forward(request,response);
	// 重定向
	// response.sendRedirect(request.getContextPath()+"/add2.jsp");
	response.setCharacterEncoding("UTF-8");
	response.setContentType("text/html;charset=UTF-8");
	// 直接响应数据
	response.getWriter().print("你好");
	return;
}
```

### 返回值是ModelAndView对象
1. ModelAndView对象是Spring提供的一个对象，可以用来调整具体的JSP视图

```java
/**
* 返回ModelAndView对象
* 可以传入视图的名称（即跳转的页面），还可以传入对象。
* @return
* @throws Exception
*/
@RequestMapping(value="/findAll")
public ModelAndView findAll() throws Exception {
ModelAndView mv = new ModelAndView();
// 跳转到list.jsp的页面
mv.setViewName("list");
// 模拟从数据库中查询所有的用户信息
List<User> users = new ArrayList<>();
User user1 = new User();
user1.setUsername("张三");
user1.setPassword("123");
User user2 = new User();
user2.setUsername("赵四");
user2.setPassword("456");
    users.add(user1);
users.add(user2);
// 添加对象
mv.addObject("users", users);
return mv;
}


<%@ page language="java" contentType="text/html; charset=UTF-8"
pageEncoding="UTF-8"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
"http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>
</head>
<body>
<h3>查询所有的数据</h3>
<c:forEach items="${ users }" var="user">
${ user.username }
</c:forEach>
</body>
</html>
```

## SpringMVC框架提供的转发和重定向

### forward请求转发
1. controller方法返回String类型，想进行请求转发也可以编写成

```java
/**
* 使用forward关键字进行请求转发
* "forward:转发的JSP路径"，不走视图解析器了，所以需要编写完整的路径
* @return
* @throws Exception
*/
@RequestMapping("/delete")
public String delete() throws Exception {
System.out.println("delete方法执行了...");
// return "forward:/WEB-INF/pages/success.jsp";
return "forward:/user/findAll";
}
```

### redirect重定向

1. controller方法返回String类型，想进行重定向也可以编写成

```java
/**
* 重定向
* @return
* @throws Exception
*/
@RequestMapping("/count")
public String count() throws Exception {
System.out.println("count方法执行了...");
return "redirect:/add.jsp";
// return "redirect:/user/findAll";
}
```

## ResponseBody响应json数据

1. DispatcherServlet会拦截到所有的资源，导致一个问题就是静态资源（img、css、js）也会被拦截到，从而
    不能被使用。解决问题就是需要配置静态资源不进行拦截，在springmvc.xml配置文件添加如下配置
  1. mvc:resources标签配置不过滤
  1. location元素表示webapp目录下的包下的所有文件
  2. mapping元素表示以/static开头的所有请求路径，如/static/a 或者/static/a/b

```xml
<!-- 设置静态资源不过滤 -->
<mvc:resources location="/css/" mapping="/css/**"/> <!-- 样式 -->
<mvc:resources location="/images/" mapping="/images/**"/> <!-- 图片 -->
<mvc:resources location="/js/" mapping="/js/**"/> <!-- javascript -->
```

​	2.使用@RequestBody获取请求体数据

```java
/* 获取请求体的数据
* @param body
*/
@RequestMapping("/testJson")
public void testJson(@RequestBody String body) {
System.out.println(body);
}
```

​	3.使用@RequestBody注解把json的字符串转换成JavaBean的对象

```java
/**
* 获取请求体的数据
* @param body
*/
@RequestMapping("/testJson")
public void testJson(@RequestBody Address address) {
System.out.println(address);
}
```

## 第二章：SpringMVC实现文件上传

### 传统方式

```xml
<dependency>
<groupId>commons-fileupload</groupId>
<artifactId>commons-fileupload</artifactId>
<version>1.3.1</version>
</dependency>
<dependency>
<groupId>commons-io</groupId>
<artifactId>commons-io</artifactId>
<version>2.4</version>
</dependency>
```

### SpringMVC上传

```xml
<!-- 配置文件解析器对象，要求id名称必须是multipartResolver -->
<bean id="multipartResolver"
class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
<property name="maxUploadSize" value="10485760"/>
</bean>
```

### SpringMVC跨服务器方式文件上传

```xml
<dependency>
<groupId>com.sun.jersey</groupId>
<artifactId>jersey-core</artifactId>
<version>1.18.1</version>
</dependency>
<dependency>
<groupId>com.sun.jersey</groupId>
<artifactId>jersey-client</artifactId>
<version>1.18.1</version>
</dependency>
```

### 3种方式上传

```java
package cn.itcast.controller;

import com.sun.jersey.api.client.Client;
import com.sun.jersey.api.client.WebResource;
import org.apache.commons.fileupload.FileItem;
import org.apache.commons.fileupload.disk.DiskFileItemFactory;
import org.apache.commons.fileupload.servlet.ServletFileUpload;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.multipart.MultipartFile;

import javax.servlet.http.HttpServletRequest;
import java.io.File;
import java.util.List;
import java.util.UUID;

@Controller
@RequestMapping("/user")
public class UserController {

    /**
     * 跨服务器文件上传
     * @return
     */
    @RequestMapping("/fileupload3")
    public String fileuoload3(MultipartFile upload) throws Exception {
        System.out.println("跨服务器文件上传...");

        // 定义上传文件服务器路径
        String path = "http://localhost:9090/uploads/";

        // 说明上传文件项
        // 获取上传文件的名称
        String filename = upload.getOriginalFilename();
        // 把文件的名称设置唯一值，uuid
        String uuid = UUID.randomUUID().toString().replace("-", "");
        filename = uuid+"_"+filename;

        // 创建客户端的对象
        Client client = Client.create();

        // 和图片服务器进行连接
        WebResource webResource = client.resource(path + filename);

        // 上传文件
        webResource.put(upload.getBytes());

        return "success";
    }

    /**
     * SpringMVC文件上传
     * @return
     */
    @RequestMapping("/fileupload2")
    public String fileuoload2(HttpServletRequest request, MultipartFile upload) throws Exception {
        System.out.println("springmvc文件上传...");

        // 使用fileupload组件完成文件上传
        // 上传的位置
        String path = request.getSession().getServletContext().getRealPath("/uploads/");
        // 判断，该路径是否存在
        File file = new File(path);
        if(!file.exists()){
            // 创建该文件夹
            file.mkdirs();
        }

        // 说明上传文件项
        // 获取上传文件的名称
        String filename = upload.getOriginalFilename();
        // 把文件的名称设置唯一值，uuid
        String uuid = UUID.randomUUID().toString().replace("-", "");
        filename = uuid+"_"+filename;
        // 完成文件上传
        upload.transferTo(new File(path,filename));

        return "success";
    }

    /**
     * 文件上传
     * @return
     */
    @RequestMapping("/fileupload1")
    public String fileuoload1(HttpServletRequest request) throws Exception {
        System.out.println("文件上传...");

        // 使用fileupload组件完成文件上传
        // 上传的位置
        String path = request.getSession().getServletContext().getRealPath("/uploads/");
        // 判断，该路径是否存在
        File file = new File(path);
        if(!file.exists()){
            // 创建该文件夹
            file.mkdirs();
        }

        // 解析request对象，获取上传文件项
        DiskFileItemFactory factory = new DiskFileItemFactory();
        ServletFileUpload upload = new ServletFileUpload(factory);
        // 解析request
        List<FileItem> items = upload.parseRequest(request);
        // 遍历
        for(FileItem item:items){
            // 进行判断，当前item对象是否是上传文件项
            if(item.isFormField()){
                // 说明普通表单向
            }else{
                // 说明上传文件项
                // 获取上传文件的名称
                String filename = item.getName();
                // 把文件的名称设置唯一值，uuid
                String uuid = UUID.randomUUID().toString().replace("-", "");
                filename = uuid+"_"+filename;
                // 完成文件上传
                item.write(new File(path,filename));
                // 删除临时文件
                item.delete();
            }
        }

        return "success";
    }

}

```

## 第三章：SpringMVC的异常处理

### 1. 异常处理思路

Controller调用service，service调用dao，异常都是向上抛出的，最终有DispatcherServlet找异常处理器进
行异常的处理。

### 2. SpringMVC的异常处理

1. 自定义异常类

```java
package cn.itcast.exception;

/**
 * 自定义异常类
 */
public class SysException extends Exception{

    // 存储提示信息的
    private String message;

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }

    public SysException(String message) {
        this.message = message;
    }

}
```

2. 自定义异常处理器

一定配置拦截异常处理器xml（id 名字不可更改）

```xml
 <!--配置异常处理器-->
    <bean id="sysExceptionResolver" class="cn.itcast.exception.SysExceptionResolver"/>
```

自定义异常需要继承mvc的异常处理器

```java
package cn.itcast.exception;

import org.springframework.web.servlet.HandlerExceptionResolver;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * 异常处理器
 */
public class SysExceptionResolver implements HandlerExceptionResolver{

    /**
     * 处理异常业务逻辑
     * @param request
     * @param response
     * @param handler
     * @param ex
     * @return
     */
    public ModelAndView resolveException(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) {
        // 获取到异常对象
        SysException e = null;
        if(ex instanceof SysException){
            e = (SysException)ex;
        }else{
            e = new SysException("系统正在维护....");
        }
        // 创建ModelAndView对象
        ModelAndView mv = new ModelAndView();
        mv.addObject("errorMsg",e.getMessage());
        mv.setViewName("error");
        return mv;
    }

}
```

## 第四章：SpringMVC框架中的拦截器

### 1. 拦截器的概述

1. SpringMVC框架中的**拦截器**用于对处理器进行预处理和后处理的技术。
2. 可以定义**拦截器链**，连接器链就是将拦截器按着一定的顺序结成一条链，在访问被拦截的方法时，拦截器链
中的拦截器会按着定义的顺序执行。
3. 拦截器和过滤器的功能比较类似，有区别
	1. 过滤器是Servlet规范的一部分，任何框架都可以使用过滤器技术。
	2. 拦截器是SpringMVC框架独有的。
	3. 过滤器配置了/*，可以拦截任何资源。
	4. 拦截器只会对控制器中的**方法**进行拦截。
4. 拦截器也是**AOP思想**的一种实现方式
5. 想要自定义拦截器，需要实现HandlerInterceptor接口。

### 2. 自定义拦截器步骤

​	1. 创建类，实现HandlerInterceptor接口，重写需要的方法

```JAVA
package cn.itcast.controller.cn.itcast.interceptor;

import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * 自定义拦截器
 */
public class MyInterceptor1 implements HandlerInterceptor{

    /**
     * 预处理，controller方法执行前
     * return true 放行，执行下一个拦截器，如果没有，执行controller中的方法
     * return false不放行
     * @param request
     * @param response
     * @param handler
     * @return
     * @throws Exception
     */
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("MyInterceptor1执行了...前1111");
        // request.getRequestDispatcher("/WEB-INF/pages/error.jsp").forward(request,response);
        return true;
    }

    /**
     * 后处理方法，controller方法执行后，success.jsp执行之前
     * @param request
     * @param response
     * @param handler
     * @param modelAndView
     * @throws Exception
     */
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("MyInterceptor1执行了...后1111");
        // request.getRequestDispatcher("/WEB-INF/pages/error.jsp").forward(request,response);
    }

    /**
     * success.jsp页面执行后，该方法会执行
     * @param request
     * @param response
     * @param handler
     * @param ex
     * @throws Exception
     */
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("MyInterceptor1执行了...最后1111");
    }

}

```

2. 在springmvc.xml中配置拦截器类

```xml
<!--配置拦截器-->
    <mvc:interceptors>
        <!--配置拦截器-->
        <mvc:interceptor>
            <!--要拦截的具体的方法-->
            <mvc:mapping path="/user/*"/>
            <!--不要拦截的方法
            <mvc:exclude-mapping path=""/>
            -->
            <!--配置拦截器对象-->
            <bean class="cn.itcast.controller.cn.itcast.interceptor.MyInterceptor1" />
        </mvc:interceptor>

        <!--配置第二个拦截器-->
        <mvc:interceptor>
            <!--要拦截的具体的方法-->
            <mvc:mapping path="/**"/>
            <!--不要拦截的方法
            <mvc:exclude-mapping path=""/>
            -->
            <!--配置拦截器对象-->
            <bean class="cn.itcast.controller.cn.itcast.interceptor.MyInterceptor2" />
        </mvc:interceptor>
    </mvc:interceptors>

    <!-- 开启SpringMVC框架注解的支持 -->
    <mvc:annotation-driven />
```

### 3. HandlerInterceptor接口中的方法

1. preHandle方法是controller方法执行前拦截的方法
	1. 可以使用request或者response跳转到指定的页面
	2. return true放行，执行下一个拦截器，如果没有拦截器，执行controller中的方法。
	3. return false不放行，不会执行controller中的方法。
2. postHandle是controller方法执行后执行的方法，在JSP视图执行前。
	1. 可以使用request或者response跳转到指定的页面
	2. 如果指定了跳转的页面，那么controller方法跳转的页面将不会显示。
3. postHandle方法是在JSP执行后执行
	1. request或者response不能再跳转页面了

### 4. 配置多个拦截器

```java
package cn.itcast.controller.cn.itcast.interceptor;

import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * 自定义拦截器
 */
public class MyInterceptor2 implements HandlerInterceptor{

    /**
     * 预处理，controller方法执行前
     * return true 放行，执行下一个拦截器，如果没有，执行controller中的方法
     * return false不放行
     * @param request
     * @param response
     * @param handler
     * @return
     * @throws Exception
     */
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("MyInterceptor1执行了...前2222");
        // request.getRequestDispatcher("/WEB-INF/pages/error.jsp").forward(request,response);
        return true;
    }

    /**
     * 后处理方法，controller方法执行后，success.jsp执行之前
     * @param request
     * @param response
     * @param handler
     * @param modelAndView
     * @throws Exception
     */
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("MyInterceptor1执行了...后2222");
        // request.getRequestDispatcher("/WEB-INF/pages/error.jsp").forward(request,response);
    }

    /**
     * success.jsp页面执行后，该方法会执行
     * @param request
     * @param response
     * @param handler
     * @param ex
     * @throws Exception
     */
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("MyInterceptor1执行了...最后2222");
    }

}

```

