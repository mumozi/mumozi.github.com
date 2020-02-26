# 任务

## 异步任务

### 1.服务层

  @Async告诉Spring这是一个异步方法

```java
package com.mayday.task.service;

import org.springframework.scheduling.annotation.Async;
import org.springframework.stereotype.Service;

/**
 * @description:
 * @author: MayDay
 * @time: 2020/2/24 17:41
 */
@Service
public class AsyncService {

    //告诉Spring这是一个异步方法
    @Async
    public void hello() {
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("处理数据中...");
    }
}
```

### 2.控制层

```java
package com.mayday.task.controller;

import com.mayday.task.service.AsyncService;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;

/**
 * @description:
 * @author: MayDay
 * @time: 2020/2/24 17:47
 */
@RestController
public class AsyncController {

    @Resource
    AsyncService asyncService;

    @GetMapping("/hello")
    public String hello() {
        asyncService.hello();
        return "success";
    }
}

```

### 3.执行

@EnableAsync//开启异步注解

```java

@EnableAsync//开启异步注解
@SpringBootApplication
public class TaskApplication {

    public static void main(String[] args) {
        SpringApplication.run(TaskApplication.class, args);
    }

}
```

!>异步处理：前台http://localhost:8080/hello直接显示success，后台控制层在等待3秒之后处理打印结果。

## 定时任务

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200224181140.png)

参考如下案例： @Scheduled(cron ="")

```java
package com.mayday.task.service;

import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Service;

/**
 * @description:
 * @author: MayDay
 * @time: 2020/2/24 18:02
 */
@Service
public class ScheduledService {

    /**
     * second(秒), minute（分）, hour（时）, day of month（日）, month（月）, day of week（周几）.
     * 0 * * * * MON-FRI
     * 【0 0/5 14,18 * * ?】 每天14点整，和18点整，每隔5分钟执行一次
     * 【0 15 10 ? * 1-6】 每个月的周一至周六10:15分执行一次
     * 【0 0 2 ? * 6L】每个月的最后一个周六凌晨2点执行一次
     * 【0 0 2 LW * ?】每个月的最后一个工作日凌晨2点执行一次
     * 【0 0 2-4 ? * 1#1】每个月的第一个周一凌晨2点到4点期间，每个整点都执行一次；
     */
    // @Scheduled(cron = "0 * * * * MON-SAT")
    //@Scheduled(cron = "0,1,2,3,4 * * * * MON-SAT")
    // @Scheduled(cron = "0-4 * * * * MON-SAT")
    @Scheduled(cron = "0/4 * * * * MON-SAT")  //每4秒执行一次
    public void hello() {
        System.out.println("hello...");
    }
}
```
@EnableScheduling//开启定时任务

```java
@EnableAsync//开启异步注解
@EnableScheduling//开启定时任务
@SpringBootApplication
public class TaskApplication {

    public static void main(String[] args) {
        SpringApplication.run(TaskApplication.class, args);
    }

}
```

## 邮件任务

### 1.引入

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-mail</artifactId>
</dependency>
```

### 2.简单发送

```properties
spring.mail.host=smtp.126.com
spring.mail.username=xxxxxxxx@126.com
spring.mail.password=xxxx
#密码是授权码，非登录密码
```

```java
@SpringBootTest
class TaskApplicationTests {

    @Autowired
    JavaMailSenderImpl mailSender;

    @Test
    void contextLoads() {

        SimpleMailMessage message = new SimpleMailMessage();
        message.setSubject("通知");
        message.setText("7点半开会");
        message.setTo("xxxxx@qq.com");
        message.setFrom("xxxxxxxx@126.com");//跟配置文件一致
        mailSender.send(message);
    }

}
```

### 3.复杂邮件

```java
@Test
	public void test02() throws  Exception{
		//1、创建一个复杂的消息邮件
		MimeMessage mimeMessage = mailSender.createMimeMessage();
		MimeMessageHelper helper = new MimeMessageHelper(mimeMessage, true);		
	//邮件设置
	helper.setSubject("通知-今晚开会");
	helper.setText("<b style='color:red'>今天 7:30 开会</b>",true);

	helper.setTo("xxxxx@qq.com");
	helper.setFrom("xxx@qq.com");

	//上传文件
	helper.addAttachment("1.jpg",new File("C:\\Users\\lfy\\Pictures\\Saved Pictures\\1.jpg"));
	helper.addAttachment("2.jpg",new File("C:\\Users\\lfy\\Pictures\\Saved Pictures\\2.jpg"));

	mailSender.send(mimeMessage);

}
```