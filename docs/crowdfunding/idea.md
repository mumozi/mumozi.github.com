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

