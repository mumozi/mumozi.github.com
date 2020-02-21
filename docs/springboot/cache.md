# 高级-缓存

- pox文件依赖

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200219182749.png)

## 搭建基本环境

- 导入数据库文件 

- 创建javabean封装数据

- 整合mybatis操作数据库

  1. 配置数据源

  2. 使用注解版的MyBatis

     1）、@MapperScan指定需要扫描的mapper接口所在的包

!>使用逆向工程创建mapper等文件（参考mybatis节点）

1. 创建service.EmployeeService

   ```java
   @Service
   public class EmployeeService {
   
       @Resource
       EmployeeMapper employeeMapper
   
       public Employee getEmp(Integer id) {
           System.out.println("查询" + id + "号员工");
           Employee emp = employeeMapper.selectByPrimaryKey(id);
           return emp;
       }
   }
   ```

!>@Resource的作用相当于@Autowired，只不过@Autowired按byType自动注入，而@Resource默认按  byName自动注入罢了。@Resource有两个属性是比较重要的，分是name和type，Spring将@Resource注解的name属性解析为bean的名字，而type属性则解析为bean的类型。所以如果使用name属性，则使用byName的自动注入策略，而使用type属性时则使用byType自动注入策略。如果既不指定name也不指定type属性，这时将通过反射机制使用byName自动注入策略。

2. 创建控制层EmployeeController

   ```java
   package com.mayday.springboot.controller;
   
   import com.mayday.springboot.entity.Employee;
   import com.mayday.springboot.service.EmployeeService;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.web.bind.annotation.GetMapping;
   import org.springframework.web.bind.annotation.PathVariable;
   import org.springframework.web.bind.annotation.RestController;
   
   @RestController
   public class EmployeeController {
   
       @Autowired
       EmployeeService employeeService;
   
       @GetMapping("emp/{id}")
       public Employee getEmployee(@PathVariable("id") Integer id) {
           Employee employee = employeeService.getEmp(id);
           return employee;
       }
   }
   ```

## 开启缓存

### 概念

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200221180702.png)

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200221180718.png)

### 快速体验缓存

 * 			1、开启基于注解的缓存 @EnableCaching
 * 			2、标注缓存注解即可
 * 				@Cacheable
 * 				@CacheEvict
 * 				@CachePut
 * 默认使用的是ConcurrentMapCacheManager==ConcurrentMapCache；
 * 将数据保存在	ConcurrentMap<Object, Object>中
 * 开发中使用缓存中间件；redis、memcached、ehcache；

1.主程序开启缓存注解@EnableCaching

```java
@MapperScan("com.mayday.springboot.dao")
@SpringBootApplication
@EnableCaching
public class SpringbootApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootApplication.class, args);
    }

}
```

2.控制层EmployeeController

```java
package com.mayday.springboot.controller;

import com.mayday.springboot.entity.Employee;
import com.mayday.springboot.service.EmployeeService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class EmployeeController {

    @Autowired
    EmployeeService employeeService;

    @GetMapping("emp/{id}")
    public Employee getEmployee(@PathVariable("id") Integer id) {
        Employee employee = employeeService.getEmp(id);
        return employee;
    }

    @GetMapping("/emp")
    public Employee update(Employee employee) {
        Employee emp = employeeService.updateEmp(employee);

        return emp;
    }

    @GetMapping("/delemp/{id}")
    public String deleteEmp(@PathVariable("id") Integer id) {
        employeeService.delEmp(id);
        return "success";
    }

    @GetMapping("/emp/lastname/{lastName}")
    public Employee getEmpByLastName(@PathVariable("lastName") String lastName) {
        return employeeService.getEmpByLastName(lastName);
    }

}
```

3.注解@Cacheable

```java
/* @Cacheable： 1、方法运行之前，先去查询Cache（缓存组件），按照cacheNames指定的名字获取；
     * （CacheManager先获取相应的缓存），第一次获取缓存如果没有Cache组件会自动创建。
     * 2、去Cache中查找缓存的内容，使用一个key，默认就是方法的参数；
     * key是按照某种策略生成的；默认是使用keyGenerator生成的，默认使用SimpleKeyGenerator生成key；
     * SimpleKeyGenerator生成key的默认策略；
     * 如果没有参数；key=new SimpleKey()；
     * 如果有一个参数：key=参数的值
     * 如果有多个参数：key=new SimpleKey(params)；
     * 3、没有查到缓存就调用目标方法；
     * 4、将目标方法返回的结果，放进缓存中
     * @Cacheable标注的方法执行之前先来检查缓存中有没有这个数据，默认按照参数的值作为key去查询缓存， 如果没有就运行方法并将结果放入缓存；以后再来调用就可以直接使用缓存中的数据；
     * <p>
     * 核心：
     * 1）、使用CacheManager【ConcurrentMapCacheManager】按照名字得到Cache【ConcurrentMapCache】组件
     * 2）、key使用keyGenerator生成的，默认是SimpleKeyGenerator
     * 几个属性：
     * cacheNames/value：指定缓存组件的名字;将方法的返回结果放在哪个缓存中，是数组的方式，可以指定多个缓存；
     * key：缓存数据使用的key；可以用它来指定。默认是使用方法参数的值  1-方法的返回值
     * 编写SpEL； #i d;参数id的值   #a0  #p0  #root.args[0]
     * getEmp[2]
     * keyGenerator：key的生成器；可以自己指定key的生成器的组件id
     * key/keyGenerator：二选一使用;
     * cacheManager：指定缓存管理器；或者cacheResolver指定获取解析器
     * condition：指定符合条件的情况下才缓存；
     * ,condition = "#id>0"
     * condition = "#a0>1"：第一个参数的值》1的时候才进行缓存
     * unless:否定缓存；当unless指定的条件为true，方法的返回值就不会被缓存；可以获取到结果进行判断
     * unless = "#result == null"
     * unless = "#a0==2":如果第一个参数的值是2，结果不缓存；
     * sync：是否使用异步模式
         …*/
@Cacheable(value = {"emp"}/*,keyGenerator = "myKeyGenerator",condition = "#a0>1",unless = "#a0==2"*/)
public Employee getEmp(Integer id) {
    System.out.println("查询" + id + "号员工");
    Employee emp = employeeMapper.selectByPrimaryKey(id);
    return emp;
}
```

4.注解@CachePut

```java
/**
 * @CachePut：既调用方法，又更新缓存数据；同步更新缓存 修改了数据库的某个数据，同时更新缓存；
 * 运行时机：
 * 1、先调用目标方法
 * 2、将目标方法的结果缓存起来
 * <p>
 * 测试步骤：
 * 1、查询1号员工；查到的结果会放在缓存中；
 * key：1  value：lastName：张三
 * 2、以后查询还是之前的结果
 * 3、更新1号员工；【lastName:zhangsan；gender:0】
 * 将方法的返回值也放进缓存了；
 * key：传入的employee对象  值：返回的employee对象；
 * 4、查询1号员工？
 * 应该是更新后的员工；
 * key = "#employee.id":使用传入的参数的员工id；
 * key = "#result.id"：使用返回后的id
 * @Cacheable的key是不能用#result 为什么是没更新前的？【1号员工没有在缓存中更新】
 */
@CachePut(/*value = "emp",*/key = "#result.id")
public Employee updateEmp(Employee employee) {
    System.out.println("update:" + employee);
    employeeMapper.updateByPrimaryKey(employee);
    return employee;
}
```

5.注解@CacheEvict

```java
/**
 * @CacheEvict：缓存清除 key：指定要清除的数据
 * allEntries = true：指定清除这个缓存中所有的数据
 * beforeInvocation = false：缓存的清除是否在方法之前执行
 * 默认代表缓存清除操作是在方法执行之后执行;如果出现异常缓存就不会清除
 * beforeInvocation = true：
 * 代表清除缓存操作是在方法运行之前执行，无论方法是否出现异常，缓存都清除
 */
@CacheEvict(value = "emp", beforeInvocation = true/*key = "#id",*/)
public void delEmp(Integer id) {
    System.out.println("deleteEmp:" + id);
}
```

6.注解caching

```java
// @Caching 定义复杂的缓存规则
@Caching(
        cacheable = {
                @Cacheable(/*value="emp",*/key = "#lastName")
        },
        put = {
                @CachePut(/*value="emp",*/key = "#result.id"),
                @CachePut(/*value="emp",*/key = "#result.email")
        }
)
public Employee getEmpByLastName(String lastName) {
    return employeeMapper.getEmpByLastName(lastName);
}
```

### 整合redis作为缓存

 * Redis 是一个开源（BSD许可）的，内存中的数据结构存储系统，它可以用作数据库、缓存和消息中间件。
 * 	1、安装redis：使用docker；
 * 	2、引入redis的starter
 * 	3、配置redis
 * 	4、测试缓存   
 * 		原理：CacheManager===Cache 缓存组件来实际给缓存中存取数据
 *		1）、引入redis的starter，容器中保存的是 RedisCacheManager；
 *		2）、RedisCacheManager 帮我们创建 RedisCache 来作为缓存组件；RedisCache通过操作redis缓存数据的
 *		3）、默认保存数据 k-v 都是Object；利用序列化保存；如何保存为json
 *   			1、引入了redis的starter，cacheManager变为 RedisCacheManager；
 *   			2、默认创建的 RedisCacheManager 操作redis的时候使用的是 RedisTemplate<Object, Object>
 *   			3、RedisTemplate<Object, Object> 是 默认使用jdk的序列化机制
 *      4）、自定义CacheManager；