# 用户模块

## 建表准备

```sql
DROP TABLE IF EXISTS `mmall_user`;
CREATE TABLE `mmall_user` (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '用户表id',
  `username` varchar(50) NOT NULL COMMENT '用户名',
  `password` varchar(50) NOT NULL COMMENT '用户密码，MD5加密',
  `email` varchar(50) DEFAULT NULL,
  `phone` varchar(20) DEFAULT NULL,
  `question` varchar(100) DEFAULT NULL COMMENT '找回密码问题',
  `answer` varchar(100) DEFAULT NULL COMMENT '找回密码答案',
  `role` int(4) NOT NULL COMMENT '角色0-管理员,1-普通用户',
  `create_time` datetime NOT NULL COMMENT '创建时间',
  `update_time` datetime NOT NULL COMMENT '最后一次更新时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `user_name_unique` (`username`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=22 DEFAULT CHARSET=utf8;
```



## 登陆

### 公共层准备common

1. 常量类

```java
package com.mmal.common;

/**
 * @description:常量类
 * @author: MayDay
 * @time: 2020/3/9 18:42
 */
public class Const {

    public static final String CURRENT_USER = "currentUser";
}

```

2. 响应code枚举类

```java
package com.mmal.common;

/**
 * @description:code枚举类
 * @author: MayDay
 * @time: 2020/3/9 17:33
 */
public enum ResponseCode {

    SUCCESS(0, "SUCCESS"),
    ERROR(1, "ERROR"),
    NEED_LOGIN(10, "NEED_LOGIN"),
    ILLEGAL_ARGUMENT(2, "ILLEGAL_ARGUMENT");

    private final int code;
    private final String desc;

    ResponseCode(int code, String desc) {
        this.code = code;
        this.desc = desc;
    }

    public int getCode() {
        return code;
    }

    public String getDesc() {
        return desc;
    }
}

```

3. 服务器响应类

```java
package com.mmal.common;

import org.codehaus.jackson.annotate.JsonIgnore;
import org.codehaus.jackson.map.annotate.JsonSerialize;

import java.io.Serializable;

/**
 * @description:
 * @author: MayDay
 * @time: 2020/3/9 17:25
 */
//注解是保证序列化json的时候，如果是null的对象，key也会消失
@JsonSerialize(include = JsonSerialize.Inclusion.NON_NULL)
public class ServerResponse<T> implements Serializable {

    private int status;
    private String msg;
    private T data;

    public ServerResponse(int status, T data) {
        this.status = status;
        this.data = data;
    }

    public ServerResponse(int status, String msg) {
        this.status = status;
        this.msg = msg;
    }

    public ServerResponse(int status) {
        this.status = status;
    }

    public ServerResponse(int status, String msg, T data) {

        this.status = status;
        this.msg = msg;
        this.data = data;
    }

    public int getStatus() {
        return status;
    }

    public String getMsg() {
        return msg;
    }

    public T getData() {
        return data;
    }

    //使之不在json序列化的结果里
    @JsonIgnore
    public boolean isSuccess() {
        return this.status == ResponseCode.SUCCESS.getCode();
    }

    public static <T> ServerResponse<T> createBySuccess() {
        return new ServerResponse<T>(ResponseCode.SUCCESS.getCode());
    }

    public static <T> ServerResponse<T> createBySuccessMessage(String msg) {
        return new ServerResponse<T>(ResponseCode.SUCCESS.getCode(), msg);
    }

    public static <T> ServerResponse<T> createBySuccess(T data) {
        return new ServerResponse<T>(ResponseCode.SUCCESS.getCode(), data);
    }

    public static <T> ServerResponse<T> createBySuccess(String msg, T data) {
        return new ServerResponse<T>(ResponseCode.SUCCESS.getCode(), msg, data);
    }


    public static <T> ServerResponse<T> createByError() {
        return new ServerResponse<T>(ResponseCode.ERROR.getCode(), ResponseCode.ERROR.getDesc());
    }


    public static <T> ServerResponse<T> createByErrorMessage(String errorMessage) {
        return new ServerResponse<T>(ResponseCode.ERROR.getCode(), errorMessage);
    }

    public static <T> ServerResponse<T> createByErrorCodeMessage(int errorCode, String errorMessage) {
        return new ServerResponse<T>(errorCode, errorMessage);
    }
}

```

### 服务层service

1. 用户接口类

```java
package com.mmal.service;

import com.mmal.common.ServerResponse;
import com.mmal.pojo.User;

/**
 * @description:
 * @author: MayDay
 * @time: 2020/3/9 17:19
 */
public interface IUserService {

    ServerResponse<User> login(String username, String password);
}
```

2. 用户接口实现类

   service.Impl

```java
package com.mmal.service.Impl;

import com.mmal.common.ServerResponse;
import com.mmal.dao.UserMapper;
import com.mmal.pojo.User;
import com.mmal.service.IUserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import static org.apache.commons.lang3.StringUtils.EMPTY;

/**
 * @description:
 * @author: MayDay
 * @time: 2020/3/9 17:24
 */
@Service("iUserService")
public class UserServiceImpl implements IUserService {

    @Autowired
    private UserMapper userMapper;

    @Override
    public ServerResponse<User> login(String username, String password) {
        int resultCount = userMapper.checkUsername(username);
        if (resultCount == 0) {
            return ServerResponse.createByErrorMessage("用户名不存在");
        }
        //todo 密码登录md5
        User user = userMapper.selectLogin(username, password);
        if (user == null) {
            return ServerResponse.createByErrorMessage("密码错误");

        }
        user.setPassword(EMPTY);
        return ServerResponse.createBySuccessMessage("登录成功");
    }
}

```

### sql查询方法添加

1. 根据需求手动添加查询方法(手动添加模块dao层)

   `com.mmal.dao.UserMapper`

```java
package com.mmal.dao;

import com.mmal.pojo.User;
import org.apache.ibatis.annotations.Param;

public interface UserMapper {
    int deleteByPrimaryKey(Integer id);

    int insert(User record);

    int insertSelective(User record);

    User selectByPrimaryKey(Integer id);

    int updateByPrimaryKeySelective(User record);

    int updateByPrimaryKey(User record);

    //手动添加sql模块
    int checkUsername(String username);

    User selectLogin(@Param("username") String username, @Param("password") String password);
}
```

2. 对应查询sql编写

(`src/main/resources/mappers/UserMapper.xml`)

```xml
 <!--手动添加sql模块    -->
    <select id="checkUsername" resultType="int" parameterType="string">
        select count(1) from mmall_user
        where username = #{username}
    </select>
    <select id="selectLogin" resultMap="BaseResultMap" parameterType="map">
        select
        <include refid="Base_Column_List"></include>
        from mmall_user
        where username = #{username}
        and password = #{password}
    </select>
```

### 执行登陆controller

```java
package com.mmal.controller.portal;

import com.mmal.common.Const;
import com.mmal.common.ServerResponse;
import com.mmal.pojo.User;
import com.mmal.service.IUserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

import javax.servlet.http.HttpSession;

/**
 * @description:
 * @author: MayDay
 * @time: 2020/3/9 17:12
 */
@RestController
@RequestMapping("/user/")
public class UserController {

    @Autowired
    private IUserService iUserService;

    /**
     * 用户登录
     *
     * @param username
     * @param password
     * @param session
     * @return
     */
    @RequestMapping(value = "login.do", method = RequestMethod.POST)
    public ServerResponse<User> login(String username, String password, HttpSession session) {
        //service-->mybatis-->dao
        ServerResponse<User> response = iUserService.login(username, password);
        if (response.isSuccess()) {
            session.setAttribute(Const.CURRENT_USER, response.getData());
        }
        return response;
    }
}

```



## 用户名验证

流程说明：controller调用接口层方法，接口实现类定义方法明细，返回值均调用公共服务器返回类，其中常量均单独定义在const类中。

!>const类分组使用内部接口类interface。

### 调用层

src/main/java/com/mmal/controller/portal/UserController.java

```java
/**
     * 校验数据
     *
     * @param str
     * @param type
     * @return
     */
    @RequestMapping(value = "check_valid.do", method = RequestMethod.GET)
    public ServerResponse<String> checkValid(String str, String type) {
        return iUserService.checkValid(str, type);
    }
```

### 接口类

```java
package com.mmal.service;

import com.mmal.common.ServerResponse;
import com.mmal.pojo.User;

/**
 * @description:
 * @author: MayDay
 * @time: 2020/3/9 17:19
 */
public interface IUserService {

    ServerResponse<User> login(String username, String password);

    ServerResponse<String> register(User user);

    ServerResponse<String> checkValid(String str, String type);
}

```

### 接口实现类

src/main/java/com/mmal/service/Impl/UserServiceImpl.java

```java
    /**
     * 校验数据
     *
     * @param str  参数值
     * @param type 参数名
     * @return
     */
    public ServerResponse<String> checkValid(String str, String type) {
        if (StringUtils.isNotBlank(type)) {
            //开始校验
            if (Const.USERNAME.equals(type)) {
                int resultCount = userMapper.checkUsername(str);
                if (resultCount > 0) {
                    return ServerResponse.createByErrorMessage("用户名已存在");
                }
            }
            if (Const.EMAIL.equals(type)) {
                int resultCount = userMapper.checkEmail(str);
                if (resultCount > 0) {
                    return ServerResponse.createByErrorMessage("email已存在");
                }
            }
        } else {
            return ServerResponse.createByErrorMessage("参数错误");
        }
        return ServerResponse.createByErrorMessage("校验成功");
    }
```

!>`isNotBlank`方法会过滤空格判定;`isNotNull`方法中单空格判定有值，`"  "`前者返回false后者返回true。

### 常量定义

```java
package com.mmal.common;

/**
 * @description:常量类
 * @author: MayDay
 * @time: 2020/3/9 18:42
 */
public class Const {

    //登陆当前用户常量
    public static final String CURRENT_USER = "currentUser";

    //用户常量
    public static final String EMAIL = "email";
    public static final String USERNAME = "username";

    //内部接口类用来分组
    public interface Role {
        int ROLE_CUSTOMER = 0;//普通用户
        int ROLE_ADMIN = 1;//管理员
    }

}

```



## 注册

### 接口实现类

```java
    /**
     * 注册
     *
     * @param user
     * @return
     */
    public ServerResponse<String> register(User user) {
        ServerResponse<String> validResponse = this.checkValid(user.getUsername(), Const.USERNAME);
        if (!validResponse.isSuccess()) {
            return validResponse;
        }
        validResponse = this.checkValid(user.getEmail(), Const.EMAIL);
        if (!validResponse.isSuccess()) {
            return validResponse;
        }
        //设置用户角色等级
        user.setRole(Const.Role.ROLE_CUSTOMER);
        //MD5加密
        user.setPassword(MD5Util.MD5EncodeUtf8(user.getPassword()));
        int resultCount = userMapper.insert(user);
        if (resultCount == 0) {
            return ServerResponse.createByErrorMessage("注册失败");
        }
        return ServerResponse.createByErrorMessage("注册成功");
    }

```

### MD5加密

1. 工具类调用

```java
package com.mmal.util;

import java.security.MessageDigest;

/**
 * Created by geely
 */
public class MD5Util {

    private static String byteArrayToHexString(byte b[]) {
        StringBuffer resultSb = new StringBuffer();
        for (int i = 0; i < b.length; i++)
            resultSb.append(byteToHexString(b[i]));

        return resultSb.toString();
    }

    private static String byteToHexString(byte b) {
        int n = b;
        if (n < 0)
            n += 256;
        int d1 = n / 16;
        int d2 = n % 16;
        return hexDigits[d1] + hexDigits[d2];
    }

    /**
     * 返回大写MD5
     *
     * @param origin
     * @param charsetname
     * @return
     */
    private static String MD5Encode(String origin, String charsetname) {
        String resultString = null;
        try {
            resultString = new String(origin);
            MessageDigest md = MessageDigest.getInstance("MD5");
            if (charsetname == null || "".equals(charsetname))
                resultString = byteArrayToHexString(md.digest(resultString.getBytes()));
            else
                resultString = byteArrayToHexString(md.digest(resultString.getBytes(charsetname)));
        } catch (Exception exception) {
        }
        return resultString.toUpperCase();
    }

    public static String MD5EncodeUtf8(String origin) {
        origin = origin + PropertiesUtil.getProperty("password.salt", "");
        return MD5Encode(origin, "utf-8");
    }


    private static final String hexDigits[] = {"0", "1", "2", "3", "4", "5",
            "6", "7", "8", "9", "a", "b", "c", "d", "e", "f"};

}

```

## 忘记密码

### 接口实现类

```java
/**
     * 通过用户名查找忘记密码的问题
     *
     * @param username
     * @return
     */
    public ServerResponse<String> selectQuestion(String username) {
        ServerResponse<String> validResponse = this.checkValid(username, Const.USERNAME);
        if (validResponse.isSuccess()) {
            //用户不存在，校验成功说明数据库不存在此用户名
            return ServerResponse.createByErrorMessage("用户不存在");
        }
        String question = userMapper.selectQuestionByUsername(username);
        if (StringUtils.isNotBlank(question)) {
            return ServerResponse.createBySuccess(question);
        }
        return ServerResponse.createByErrorMessage("找回密码的问题是空的");
    }

    /**
     * 检查问题答案
     *
     * @param username
     * @param question
     * @param answer
     * @return
     */
    public ServerResponse<String> checkAnswer(String username, String question, String answer) {
        int resultCount = userMapper.checkAnswer(username, question, answer);
        if (resultCount > 0) {
            //说明问题以及问题答案是这个用户的，并且是正确的。
            String forgetToken = UUID.randomUUID().toString();
            TokenCache.setKey("token_" + username, forgetToken);
            return ServerResponse.createBySuccess(forgetToken);
        }
        return ServerResponse.createByErrorMessage("问题的答案错误");
    }

```

### 公共类——缓存token

```java
package com.mmal.common;


import com.google.common.cache.CacheBuilder;
import com.google.common.cache.CacheLoader;
import com.google.common.cache.LoadingCache;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.util.concurrent.TimeUnit;

/**
 * @description:
 * @author: MayDay
 * @time: 2020/3/10 17:53
 */
public class TokenCache {
    private static Logger logger = LoggerFactory.getLogger(TokenCache.class);
 public static final String TOKEN_PREFIX = "token_";
    //LRU算法
    private static LoadingCache<String, String> localCache = CacheBuilder.newBuilder().initialCapacity(1000).maximumSize(10000).expireAfterAccess(12, TimeUnit.HOURS)
            .build(new CacheLoader<String, String>() {
                //默认的数据加载实现，当调用get取值的时候，如果key没有对应的值，就调用这个方法进行加载
                @Override
                public String load(String s) throws Exception {
                    return null;
                }
            });

    public static void setKey(String key, String value) {
        localCache.put(key, value);
    }

    public static String getKey(String key) {
        String value = null;
        try {
            value = localCache.get(key);
            if ("null".equals(value)) {
                return null;
            }
            return value;
        } catch (Exception e) {
            logger.error("localCache get error", e);
        }
        return null;
    }
}

```

### sql查询

```java
String selectQuestionByUsername(String username);

    int checkAnswer(@Param("username") String username, @Param("question") String question, @Param("answer") String answer);
```

```xml
    <select id="selectQuestionByUsername" resultType="String" parameterType="string">
        select question
        from mmall_user
        where username=#{username}
    </select>

    <select id="checkAnswer" resultType="int" parameterType="map">
        select count(1) from mmall_user
        where username= #{username}
        and question = #{question}
        and answer = #{answer}
    </select>
```

### 控制层

```java
  /**
     * 忘记密码查找问题
     *
     * @param username
     * @return
     */
    @RequestMapping(value = "forget_get_question.do", method = RequestMethod.GET)
    public ServerResponse<String> forgetGetQuestion(String username) {
        return iUserService.selectQuestion(username);
    }

    /**
     * 校验问题答案
     *
     * @param username
     * @param question
     * @param answer
     * @return
     */
    @RequestMapping(value = "forget_check_answer.do", method = RequestMethod.GET)
    public ServerResponse<String> forgetCheckAnswer(String username, String question, String answer) {
        return iUserService.checkAnswer(username, question, answer);
    }
```

### 登陆忘记密码重置密码

```java
/**
     * 重置密码
     *
     * @param username
     * @param passwordNew
     * @param forgetToken
     * @return
     */
    public ServerResponse<String> forgetRestPassword(String username, String passwordNew, String forgetToken) {
        return iUserService.forgetRestPassword(username, passwordNew, forgetToken);
    }
```

#### 实现类

```java
 /**
     * 重置密码
     *
     * @param username
     * @param passwordNew
     * @param forgetToken
     * @return
     */
    public ServerResponse<String> forgetRestPassword(String username, String passwordNew, String forgetToken) {
        if (StringUtils.isBlank(forgetToken)) {
            return ServerResponse.createByErrorMessage("参数错误，token需要传递");
        }

        ServerResponse<String> validResponse = this.checkValid(username, Const.USERNAME);
        if (validResponse.isSuccess()) {
            //用户不存在，校验成功说明数据库不存在此用户名
            return ServerResponse.createByErrorMessage("用户不存在");
        }

        String token = TokenCache.getKey(TokenCache.TOKEN_PREFIX + username);
        if (StringUtils.isBlank(token)) {
            return ServerResponse.createByErrorMessage("token无效或者过期");
        }

        if (StringUtils.equals(forgetToken, token)) {
            String md5Password = MD5Util.MD5EncodeUtf8(passwordNew);
            int rowCount = userMapper.updatePasswordByUsername(username, md5Password);
            if (rowCount > 0) {
                return ServerResponse.createBySuccessMessage("修改密码成功");
            } else {
                return ServerResponse.createByErrorMessage("token错误，请重新获取重置密码的token");
            }
        }
        return ServerResponse.createByErrorMessage("修改密码失败");
    }
```

`int updatePasswordByUsername(@Param("username") String username, @Param("passwordNew") String passwordNew);`

```xml
 <update id="updatePasswordByUsername" parameterType="map">
    update mmall_user
    SET password = #{passwordNew},update_time = now()
    where username = #{username}
    </update>
```



## 提交问题答案

## 重置密码

### 登陆成功重置密码

```java
 /**
     * 登陆状态下重置密码
     *
     * @param passwordOld
     * @param passwordNew
     * @param user
     * @return
     */
    public ServerResponse<String> resetPassword(String passwordOld, String passwordNew, User user) {
        //防止横向越权，要校验一下这个用户的旧密码，一定要指定是这个用户，因为我们会查询一个count（1），如果不指定id，那么结果就是true，count>0
        int resultCount = userMapper.checkPassword(MD5Util.MD5EncodeUtf8(passwordOld), user.getId());
        if (resultCount == 0) {
            return ServerResponse.createByErrorMessage("旧密码错误");
        }

        user.setPassword(MD5Util.MD5EncodeUtf8(passwordNew));
        int updateCount = userMapper.updateByPrimaryKeySelective(user);
        if (updateCount > 0) {
            return ServerResponse.createBySuccessMessage("密码更新成功");
        }
        return ServerResponse.createByErrorMessage("密码更新失败");
    }
```

#### 业务层

```java
/**
     * 登陆状态下重置密码
     *
     * @param session
     * @param passwordOld
     * @param passwordNew
     * @return
     */
    @RequestMapping(value = "reset_password.do", method = RequestMethod.GET)
    public ServerResponse<String> resetPassword(HttpSession session, String passwordOld, String passwordNew) {
        User user = (User) session.getAttribute(Const.CURRENT_USER);
        if (user == null) {
            return ServerResponse.createByErrorMessage("用户未登录");
        }
        return iUserService.resetPassword(passwordOld, passwordNew, user);
    }
```

`int checkPassword(@Param("password") String password, @Param("userId") Integer userId);`

```xml
<select id="checkPassword" resultType="int" parameterType="map">
        SELECT
        count(1)
        from mmall_user
        where password = #{password}
        and id = #{userId}
    </select>
```



## 获取用户信息

```java
 /**
     * 获取当前登陆用户信息（密码空）
     *
     * @param session
     * @return
     */
    @RequestMapping(value = "get_info.do", method = RequestMethod.POST)
    public ServerResponse<User> get_info(HttpSession session) {
        User currentUser = (User) session.getAttribute(Const.CURRENT_USER);
        if (currentUser == null) {
            return ServerResponse.createByErrorCodeMessage(ResponseCode.NEED_LOGIN.getCode(), "未登录,需要强制登录status=10");
        }
        return iUserService.getInfo(currentUser.getId());
    }
```

```java
 /**
     * 获取用户信息
     *
     * @param userId
     * @return
     */
    public ServerResponse<User> getInfo(Integer userId) {
        User user = userMapper.selectByPrimaryKey(userId);
        if (user == null) {
            return ServerResponse.createByErrorMessage("找不到当前用户");
        }
        user.setPassword(EMPTY);
        return ServerResponse.createBySuccess(user);
    }
```



## 更新用户信息

### 服务层

```java
    /**
     * 更新个人信息
     *
     * @param user
     * @return
     */
    public ServerResponse<User> updateInfo(User user) {
        //username是不能更新的
        //email也是要进行一个校验，校验是否存在且不是挡球用户的
        int resultCount = userMapper.checkEmailByUserId(user.getEmail(), user.getId());
        if (resultCount > 0) {
            return ServerResponse.createByErrorMessage("email已存在，请更换email再尝试更新");
        }
        User updateUser = new User();
        updateUser.setId(user.getId());
        updateUser.setEmail(user.getEmail());
        updateUser.setPhone(user.getPhone());
        updateUser.setQuestion(user.getQuestion());
        updateUser.setAnswer(user.getAnswer());

        int updateCount = userMapper.updateByPrimaryKeySelective(updateUser);
        if (updateCount > 0) {
            return ServerResponse.createBySuccessMessage("更新个人信息成功");
        }
        return ServerResponse.createByErrorMessage("更新个人信息失败");
    }

```

### 业务层

```java
    /**
     * 个人信息更新
     *
     * @param session
     * @param user
     * @return
     */
    @RequestMapping(value = "update_info.do", method = RequestMethod.GET)
    public ServerResponse<User> updateInfo(HttpSession session, User user) {
        User currentUser = (User) session.getAttribute(Const.CURRENT_USER);
        if (currentUser == null) {
            return ServerResponse.createByErrorMessage("用户未登录");
        }
        user.setId(currentUser.getId());
        ServerResponse<User> response = iUserService.updateInfo(user);
        if (response.isSuccess()) {
            session.setAttribute(Const.CURRENT_USER, response.getData());
        }
        return response;
    }
```



## 退出登陆

### 控制层

```java
 /**
     * 退出登陆
     *
     * @param session
     * @return
     */
    @RequestMapping(value = "logout.do", method = RequestMethod.GET)
    public ServerResponse<String> logout(HttpSession session) {
        session.removeAttribute(Const.CURRENT_USER);
        return ServerResponse.createBySuccess();
    }
```

## 调试

### 问题一

`Caused by: java.lang.IllegalStateException: Cannot convert value of type 'com.github.pagehelper.PageHelper' to required type 'org.apache.ibatis.plugin.Interceptor' for property 'plugins[0]': no matching editors or conversion strategy found`

```xml
<!-- 分页插件 -->
<property name="plugins">
    <array>
        <bean class="com.github.pagehelper.PageInterceptor">
            <!--                    <property name="properties">-->
            <!--                        <value>-->
            <!--                            dialect=mysql-->
            <!--                        </value>-->
            <!--                    </property>-->
        </bean>
    </array>
</property>
```

### 问题2

`org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter': Cannot create inner bean 'org.springframework.http.converter.json.MappingJacksonHttpMessageConverter#0' of type [org.springframework.http.converter.json.MappingJacksonHttpMessageConverter] while setting bean property 'messageConverters' with key [1]; nested exception is org.springframework.beans.factory.CannotLoadBeanClassException: Cannot find class [org.springframework.http.converter.json.MappingJacksonHttpMessageConverter] for bean with name 'org.springframework.http.converter.json.MappingJacksonHttpMessageConverter#0' defined in ServletContext resource [/WEB-INF/dispatcher-servlet.xml]; nested exception is java.lang.ClassNotFoundException: org.springframework.http.converter.json.MappingJacksonHttpMessageConverter
	`

```xml
 <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
                <property name="supportedMediaTypes">
                    <list>
                        <value>application/json;charset=UTF-8</value>
                    </list>
                </property>
            </bean>
```

```xml
<dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-core</artifactId>
            <version>2.10.2</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.10.2</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-annotations</artifactId>
            <version>2.10.2</version>
        </dependency>
```

## 接口测试

### tomcat

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200317150414.png)

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200317150458.png)

### 接口文档

[跳转](https://gitee.com/imooccode/happymmallwiki/wikis/门户_用户接口?sort_id=9917)

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200424101612.png)

### 浏览器

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200424101646.png)

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200424101850.png)



!>test接口时候要保存每一条接口url，方便管理后续调试。