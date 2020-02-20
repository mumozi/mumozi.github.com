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