# 环境搭建

## 搭建目标

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200421103244.png)

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200421103357.png)

## 项目搭建

### 1.主工程

- 首先要确定一点，在IDEA中一个Project相等于eclipse中的workspace，那么，首先我们创建一个普通的maven项目，然后把src删掉。
- 点击当前的maven项目，新建module，名称为**crowdfunding01-admin-parent**，同上一个项目一致，将module下的src目录删除，如此反复，新建三个项目

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200421104827.png)

### 2.结构

**crowdfunding01-admin-parent**

​            artifactId：crowdfunding01-admin-parent

​            packaging：**pom**

**crowdfunding02-admin-webui**

​            artifactId：crowdfunding02-admin-webui

​            packaging：**war**

**crowdfunding03-admin-component**

​            artifactId：crowdfunding03-admin-component

​            packaging：jar

**crowdfunding04-admin-entity**

​            artifactId：crowdfunding04-admin-entity

​            packaging：jar

**crowdfunding05-common-util**

​            artifactId：crowdfunding05-common-util

​            packaging：jar

**crowdfunding06-common-reverse**

​            artifactId：crowdfunding06-common-reverse

​            packaging：jar

!>因为IDEA在新建maven项目的时候不支持选择打包的方式，但是默认的打包方式是jar包，所有需要打包成pom或者war包的时候可以自己手动在项目中的pom文件中添加.`<packaging>war</packaging>`

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200421175319.png)

### 3.添加依赖（多module之间）

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200421175538.png)

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200421180341.png)

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200421183210.png)

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
#git push
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

### 5.数据库

```sql
DROP TABLE IF EXISTS `t_admin`;
CREATE TABLE `t_admin`  (
  `id` int(0) NOT NULL AUTO_INCREMENT,
  `login_acct` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL COMMENT '登陆账号',
  `user_pswd` char(32) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL COMMENT '登陆密码',
  `user_name` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL COMMENT '昵称',
  `email` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL COMMENT '邮件地址',
  `create_time` datetime(0) NOT NULL COMMENT '创建时间',
  `update_time` datetime(0) NOT NULL COMMENT '更新时间',
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;
```

### 6.逆向工程

1. pox.xml加入插件

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

   2. 在resource目录下创建generatorConfig.xml文件

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE generatorConfiguration
           PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
           "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
   <generatorConfiguration>
       <!-- mybatis-generator:generate -->
       <!--指定特定数据库的jdbc驱动jar包的位置 -->
       <context id="maydayTables" targetRuntime="MyBatis3">
           <commentGenerator>
               <!-- 是否去除自动生成的注释 true:是;false:否 -->
               <property name="suppressAllComments" value="true"/>
           </commentGenerator>
   
           <!--数据库连接的信息：驱动类、连接地址、用户名、密码 -->
           <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                           connectionURL="jdbc:mysql://192.168.0.108:3306/project_crowd?useSSL=false&amp;serverTimezone=Asia/Shanghai&amp;characterEncoding=utf-8"
                           userId="root"
                           password="123456">
           </jdbcConnection>
   
           <!-- 默认false，把JDBC DECIMAL 和 NUMERIC 类型解析为 Integer，为 true时把JDBC DECIMAL
               和 NUMERIC 类型解析为java.math.BigDecimal -->
           <javaTypeResolver>
               <property name="forceBigDecimals" value="false"/>
           </javaTypeResolver>
   
           <!-- targetProject:生成Entity类的路径 -->
           <javaModelGenerator targetProject=".\src\main\java"
                               targetPackage="com.mayday.crowd.entity">
               <!-- enableSubPackages:是否让schema作为包的后缀 -->
               <property name="enableSubPackages" value="false"/>
               <!-- 从数据库返回的值被清理前后的空格 -->
               <property name="trimStrings" value="true"/>
           </javaModelGenerator>
   
           <!-- targetProject:XxxMapper.xml映射文件生成的路径 -->
           <sqlMapGenerator targetProject=".\src\main\java"
                            targetPackage="com.mayday.crowd.mapper">
               <!-- enableSubPackages:是否让schema作为包的后缀 -->
               <property name="enableSubPackages" value="false"/>
           </sqlMapGenerator>
   
           <!-- targetPackage：Mapper接口生成的位置 -->
           <javaClientGenerator type="XMLMAPPER"
                                targetProject=".\src\main\java"
                                targetPackage="com.mayday.crowd.mapper">
               <!-- enableSubPackages:是否让schema作为包的后缀 -->
               <property name="enableSubPackages" value="false"/>
           </javaClientGenerator>
   
           <!-- 数据库表名字和我们的entity类对应的映射指定 -->
           <table tableName="t_admin" domainObjectName="Admin"/>
   
       </context>
   </generatorConfiguration>
   ```

!> "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd"报错红字，只需要添加外部声明就行了。IDEA红色提示里选Fetch external resource

3. 执行成功后生成以下文件

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200423222436.png)

4.工具文件存放

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200423231744.png)