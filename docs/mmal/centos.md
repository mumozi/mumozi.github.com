# 环境安装配置

## centos版本

`cat /etc/redhat-release`

CentOS Linux release 7.2.1511 (Core)

## JDK安装

` yum search java | grep -i --color jdk`

```shell
yum install -y java-1.8.0-openjdk java-1.8.0-openjdk-devel
#或者如下命令，安装jdk1.8.0的所有文件
yum install -y java-1.8.0-openjdk*
```

查看是否成功

`java -version`

配置环境变量

默认安装路径在`/usr/lib/jvm`

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200304105216.png)

`/etc/profile`编辑添加以下命令

```shell
# set java environment  
JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.242.b08-0.el7_7.x86_64
PATH=$PATH:$JAVA_HOME/bin  
CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar  
export JAVA_HOME  CLASSPATH  PATH 
```

配置生效 `source  /etc/profile`

查看是否生效

```shell
 echo $JAVA_HOME
 echo $PATH
 echo $CLASSPATH
```

## tomcat安装

```shell
cd /usr/local/src/
wget http://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/tomcat-8/v8.5.51/bin/apache-tomcat-8.5.51.tar.gz
yum -y install wget

```

!>进入官网找对应版本地址下载https://tomcat.apache.org

```shell
tar -zxvf apache-tomcat-8.5.51.tar.gz
```

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200304150143.png)

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200304150325.png)

```shell
 <Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" URIEncoding="UTF-8"/>

```

启动

```shell
cd bin
./startup.sh 
```



## vsftpd安装

`yum -y install vsftpd

##	nginx反向代理

`yum -y install gcc zlib zlib-devel pcre-devel openssl epenssl-devel`

添加CentOS 7 Nginx yum资源库,打开终端,使用以下命令:

```shell
sudo rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
```

在你的CentOS 7 服务器中使用`yum`命令从Nginx源服务器中获取来安装Nginx：

```shell
sudo yum install -y nginx
```

刚安装的Nginx不会自行启动。运行Nginx:

```
sudo systemctl start nginx.service
```

## CentOS 7 开机启动Nginx

```
sudo systemctl enable nginx.service
```

## Nginx配置信息

网站文件存放默认目录

```shell
/usr/share/nginx/html
```

网站默认站点配置

```shell
/etc/nginx/conf.d/default.conf
```

自定义Nginx站点配置文件存放目录

```shell
/etc/nginx/conf.d/
```

Nginx全局配置

```shell
/etc/nginx/nginx.conf
```

Nginx启动

```shell
nginx
```

   Nginx重启

```shell
nginx -s reload
```

   Nginx 检查配置文件

```shell
nginx -t
```

### 建立并生成conf

建立文件

```shell
cd /etc/nginx/conf.d
vi www.mmal.com.conf
```

```shell
server {
listen 80;
autoindex on;
server_name mmall.com www.mmall.com;
access_log /usr/local/nginx/logs/access.log combined;
index index.html index.htm index.jsp index.php;
if ( $query_string ~* ".*[\;'\<\>].*" ){
        return 404;
        }

location = / {
        root /product/front/mmall_fe/dist/view;
        index index.html;
}

location ~ .*\.html$ {
        root /product/front/mmall_fe/dist/view;
        index index.html;
}

location / {
        proxy_pass http://127.0.0.1:8080/;
        }

location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|flv|ico)$ {
        proxy_pass http://127.0.0.1:8080;
        expires 30d;
        }

location ~ .*\.(js|css)?$ {
        proxy_pass http://127.0.0.1:8080;
        expires 7d;
        }
}

```

重启服务

```shell
cd /usr/sbin
sudo ./nginx -s reload
```

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200304142739.png)

