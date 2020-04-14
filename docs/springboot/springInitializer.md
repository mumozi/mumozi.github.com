# 使用Spring Initializer快速创建Spring Boot项目

IDE都支持使用Spring的项目创建向导快速创建一个Spring Boot项目；

选择我们需要的模块；向导会联网创建Spring Boot项目；

!> 需要联网

## IDEA

1. 创建项目时选择Spring Initializr

   ![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200412193103.png)

   

2. 完善项目信息

   ![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200412194927.png)

   !> 出现 **Artifact contains illegal characters** 是因为`Artifact`中使用了大写，只能是全小写，单词之间用`-`分隔

3. 选择需要的starter

   ![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200412195003.png)

4. 创建完成后 不要的文件可以删除

   ![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200413165943.png)





## Eclipse

1. 需要安装插件，或者使用STS版本

   ![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200413170838.png)

   ![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200413170853.png)

2. 创建项目时选择Spring Starter Project

   ![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200413171114.png)

3. 完善信息

4. 选择需要的选择需要的starter

   

**默认生成的Spring Boot项目**

- 主程序已经生成好了，我们只需要完成我们自己的逻辑
- `resources`文件夹中目录结构
  - `static`：保存所有的静态资源； js、css、images；
  - `templates`：保存所有的模板页面；（Spring Boot默认jar包使用嵌入式的Tomcat，`默认`不支持JSP页面）；可以使用模板引擎（freemarker、thymeleaf）；
  - `application.properties`：Spring Boot应用的配置文件；可以修改一些默认设置；

