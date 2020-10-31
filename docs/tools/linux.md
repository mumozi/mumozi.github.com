# 服务器相关



## 宝塔工具部署

```bash
yum install -y wget && wget -O install.sh https://download.youko.me/install/install_6.0.sh && sh install.sh
```

[相关文档](https://www.emapp.cn/290.html)——失败（niginx重写规则失效）

## 小皮工具部署

```bash
Centos安装脚本 
yum install -y wget && wget -O install.sh https://download.xp.cn/install.sh && sh install.sh
```

[相关访问](https://www.xp.cn/linux.html)

### 添加站点

1. 域名或者ip和端口号

2. 修改执行目录

3. 伪静态修改

   ```bash
   &lt;IfModule mod_rewrite.c&gt;
     Options +FollowSymlinks -Multiviews
     RewriteEngine On
   
     RewriteCond %{REQUEST_FILENAME} !-d
     RewriteCond %{REQUEST_FILENAME} !-f
     RewriteRule ^(.*)$ index.php/$1 [QSA,PT,L]
   &lt;/IfModule&gt;
   
   
   location / {
               index index.php index.html error/index.html;
    if (!-e $request_filename) {
              rewrite  ^/(.*)$  /index.php?s=$1  last;
              break;
           }
   }
   ```

   4.重启niginx

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20201031134113.png)

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20201031134236.png)

### vue和TP部署同一台服务器

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20201031134528.png)

**说明：**

> vue打包基本不改（除非跨域需要设置域名及端口号）默认存放服务器80端口（阿里云需开放80）
>
> TP项目直接上传80以外端口号，注意修改数据库配置的三要素（测试可在阿里云开放端口号）

数据库版本不一致的时候需要设置字符集以及编码方式。注意区分（mysql8.0版本需要3G内存以上）

<u>环境部署尽量与本地调试环境一致。</u>