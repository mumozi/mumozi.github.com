# 项目启动

## 预装环境

实战环境（版本可低）

idea版本：2019.3

mysql版本：8.0.19   （这里使用的是docker安装）

navcat：15

JDK：1.8

mvn:3.0以上

tomcat：9.0

## 项目初始化

### 1.导入mmal.sql

### 2.idea创建项目

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200304225819.png)

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200304230010.png)

创建java主目录以及测试目录

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200304231036.png)

### 3.tomcat配置

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200304234537.png)

添加war

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200304234634.png)

!>出现打印信息乱码，变更编码方式tomcat/conf/logging.properties

```shell
java.util.logging.ConsoleHandler.encoding = GBK
# java.util.logging.ConsoleHandler.encoding = UTF-8
```

4.配置成功

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200305000657.png)

### 4.git初始化

创建README.md和.gitignore(明细如下)

```shell
*.class

#package file
*.war
*.ear

#kdiff3 ignore
*.org

#maven ignore
target/

#eclipse ignore
.settings/
.project
.classpath

#idea
.idea/
/idea/
*.ipr
*.iml
*.iws

#temp file
*.log
*.cache
*.patch
*.tmp


#system ignore
.DS_Store
Thumbs.db
```

cmd执行

```shell
#本地仓库初始化提交
git init 
git add .
git commit -am "init project"
git push
#提交远程仓库
git remote add origin git@gitee.com:xxx
git branch
git push -u origin master
#强制推送
git push -u -f origin master
#开始分支版本
git checkout -b v1.0 origin/master
#推送分支到远程
git push origin HEAD -u

```

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200305115616.png)

## 项目结构包设置

common：公共包

controller：控制包

dao：跟DB交互

service：服务包

util：工具类包

vo：value object

pojo：简单的数据库对象

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200305125700.png)

## mybatis配置

### pox.xml加入插件

```xml
 <plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>1.4.0</version>
                <configuration>
                    <verbose>true</verbose>
                    <overwrite>true</overwrite>
                </configuration>
                <executions>
                    <execution>
                        <id>Generate MyBatis Artifacts</id>
                        <goals>
                            <goal>generate</goal>
                        </goals>
                    </execution>
                </executions>
                <dependencies>
                    <dependency>
                        <groupId>org.mybatis.generator</groupId>
                        <artifactId>mybatis-generator-core</artifactId>
                        <version>1.4.0</version>
                    </dependency>
                </dependencies>
            </plugin>
```

### resource目录下创建generatorConfig.xml文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
    <!--导入属性配置-->
    <properties resource="datasource.properties"></properties>

    <!--指定特定数据库的jdbc驱动jar包的位置-->
    <classPathEntry location="${db.driverLocation}"/>

    <context id="default" targetRuntime="MyBatis3">

        <!-- optional，旨在创建class时，对注释进行控制 -->
        <commentGenerator>
            <property name="suppressDate" value="true"/>
            <property name="suppressAllComments" value="true"/>
        </commentGenerator>

        <!--jdbc的数据库连接 -->
        <jdbcConnection
                driverClass="${db.driverClassName}"
                connectionURL="${db.url}"
                userId="${db.username}"
                password="${db.password}">
        </jdbcConnection>


        <!-- 非必需，类型处理器，在数据库类型和java类型之间的转换控制-->
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>


        <!-- Model模型生成器,用来生成含有主键key的类，记录类 以及查询Example类
            targetPackage     指定生成的model生成所在的包名
            targetProject     指定在该项目下所在的路径
        -->
        <!--<javaModelGenerator targetPackage="com.mmall.pojo" targetProject=".\src\main\java">-->
        <javaModelGenerator targetPackage="com.mmall.pojo" targetProject="./src/main/java">
            <!-- 是否允许子包，即targetPackage.schemaName.tableName -->
            <property name="enableSubPackages" value="false"/>
            <!-- 是否对model添加 构造函数 -->
            <property name="constructorBased" value="true"/>
            <!-- 是否对类CHAR类型的列的数据进行trim操作 -->
            <property name="trimStrings" value="true"/>
            <!-- 建立的Model对象是否 不可改变  即生成的Model对象不会有 setter方法，只有构造方法 -->
            <property name="immutable" value="false"/>
        </javaModelGenerator>

        <!--mapper映射文件生成所在的目录 为每一个数据库的表生成对应的SqlMap文件 -->
        <!--<sqlMapGenerator targetPackage="mappers" targetProject=".\src\main\resources">-->
        <sqlMapGenerator targetPackage="mappers" targetProject="./src/main/resources">
            <property name="enableSubPackages" value="false"/>
        </sqlMapGenerator>

        <!-- 客户端代码，生成易于使用的针对Model对象和XML配置文件 的代码
                type="ANNOTATEDMAPPER",生成Java Model 和基于注解的Mapper对象
                type="MIXEDMAPPER",生成基于注解的Java Model 和相应的Mapper对象
                type="XMLMAPPER",生成SQLMap XML文件和独立的Mapper接口
        -->

        <!-- targetPackage：mapper接口dao生成的位置 -->
        <!--<javaClientGenerator type="XMLMAPPER" targetPackage="com.mmall.dao" targetProject=".\src\main\java">-->
        <javaClientGenerator type="XMLMAPPER" targetPackage="com.mmall.dao" targetProject="./src/main/java">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false" />
        </javaClientGenerator>


        <table tableName="mmall_shipping" domainObjectName="Shipping" enableCountByExample="false" enableUpdateByExample="false" enableDeleteByExample="false" enableSelectByExample="false" selectByExampleQueryId="false"></table>
        <table tableName="mmall_cart" domainObjectName="Cart" enableCountByExample="false" enableUpdateByExample="false" enableDeleteByExample="false" enableSelectByExample="false" selectByExampleQueryId="false"></table>
        <table tableName="mmall_cart_item" domainObjectName="CartItem" enableCountByExample="false" enableUpdateByExample="false" enableDeleteByExample="false" enableSelectByExample="false" selectByExampleQueryId="false"></table>
        <table tableName="mmall_category" domainObjectName="Category" enableCountByExample="false" enableUpdateByExample="false" enableDeleteByExample="false" enableSelectByExample="false" selectByExampleQueryId="false"></table>
        <table tableName="mmall_order" domainObjectName="Order" enableCountByExample="false" enableUpdateByExample="false" enableDeleteByExample="false" enableSelectByExample="false" selectByExampleQueryId="false"></table>
        <table tableName="mmall_order_item" domainObjectName="OrderItem" enableCountByExample="false" enableUpdateByExample="false" enableDeleteByExample="false" enableSelectByExample="false" selectByExampleQueryId="false"></table>
        <table tableName="mmall_pay_info" domainObjectName="PayInfo" enableCountByExample="false" enableUpdateByExample="false" enableDeleteByExample="false" enableSelectByExample="false" selectByExampleQueryId="false"></table>
        <table tableName="mmall_product" domainObjectName="Product" enableCountByExample="false" enableUpdateByExample="false" enableDeleteByExample="false" enableSelectByExample="false" selectByExampleQueryId="false">
            <columnOverride column="detail" jdbcType="VARCHAR" />
            <columnOverride column="sub_images" jdbcType="VARCHAR" />
        </table>
        <table tableName="mmall_user" domainObjectName="User" enableCountByExample="false" enableUpdateByExample="false" enableDeleteByExample="false" enableSelectByExample="false" selectByExampleQueryId="false"></table>


        <!-- geelynote mybatis插件的搭建 -->
    </context>
</generatorConfiguration>
```

同时在当前面目录下创建datasource.properties

```properties
db.driverLocation=C:\\Users\\xxx\\.m2\\\repository\\mysql\\mysql-connector-java\\8.0.19\\mysql-connector-java-8.0.19.jar
db.driverClassName=com.mysql.cj.jdbc.Driver
db.url=jdbc:mysql://192.168.31.222:3306/mmal?useSSL=false&serverTimezone=Asia/Shanghai&characterEncoding=utf-8
db.username=root
db.password=123456
```

!>maven工具栏未出现org.mybatis.generator工具，请在pox文件中注释`<pluginManagement>``plugins` 下的 `plugin` 是真实使用的，而 `pluginManagement` 下的 `plugins` 下的 `plugin` 则仅仅是一种声明，子项目中可以对 `pluginManagement` 下的 `plugin` 进行信息的选择、继承、覆盖等。

!>db.url地址栏中`&`符号在xml原始文件引用时需要替换`&amp;`

```xml
<jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                        connectionURL="jdbc:mysql://192.168.179.128:3306/jdbc?useSSL=false&amp;serverTimezone=Asia/Shanghai&amp;characterEncoding=utf-8"
                        userId="root"
                        password="123456">
        </jdbcConnection>
```

### 执行插件生成文件

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200305140935.png)

### 更改mapper.xml所有时间

对于创建时间create_time 和update_time 我们统一使用now()函数，更新sql不需要使用now替换创建时间

 额外插件跳转

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200305143659.png)

### 分页

```xml
<!--        mybatis分页插件-->
        <dependency>
            <groupId>com.github.pagehelper</groupId>
            <artifactId>pagehelper</artifactId>
            <version>5.1.11</version>
        </dependency>
```

## 工具补充

### idea补充

防止spring bean注入报错（实际运行没错）变更等级error为warning

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200309153148.png)

### 谷歌网页插件

 **restlet client**