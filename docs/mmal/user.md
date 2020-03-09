# 用户模块

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

   com.mmal.dao.UserMapper

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

(src/main/resources/mappers/UserMapper.xml)

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

## 注册

## 忘记密码

## 提交问题答案

## 重置密码

## 获取用户信息

## 更新用户信息

## 退出登陆

