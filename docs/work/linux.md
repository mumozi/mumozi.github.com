# Linux命令

## 显示当前目录所bai有文件du大小的命令

```shell
du -h 文件名
du -sh 文件名  总结
```

## 查看端口占用情况

```shell
netstat -apn
netstat -tnl
```

## 操作NGINX

```shell
ps -aux | grep nginx

测试Nginx配置文件

　　/usr/sbin/nginx -t

linux nginx启动 重启 关闭命令：

nginx -s reload ：修改配置后重新加载生效
nginx -s reopen ：重新打开日志文件
nginx -t -c /path/to/nginx.conf 测试nginx配置文件是否正确

启动操作 -c参数指定了要加载的nginx配置文件路径
```

## 复制文件夹

```shell
cp -r /WEB/ringierHK/* /web_bak/ringierHK/

或
cp -r /home/packageA/. /home/cp/packageB/
这两种方法效果是一样的。
```

## 常用

```shell
#查看文件（隐藏
ls -al
#查找zip扩展包
yum search php73|grep zip
#重启php
systemctl restart php73-php-fpm
#php安装位置
whereis php
#文件夹复制
cp -r /home/packageA/* /home/cp/packageB/
#删除
rm -rf /home/packageA

#为文件夹daojia_bak更改拥有者为www
chown -R www daojia_bak/.
#为文件夹daojia_bak更改拥有者组为www
chgrp -R www daojia_bak/.

#打包解包  .tar
tar cvf FileName.tar DirName
tar xvf FileName.tar

#清空文件内容
 echo "" > /data/www/daojia/storage/logs/laravel.log 
#查看当前目录下各个文件及目录占用空间大小
du -sh *

#查看nginx路径
ps aux|grep nginx
#查看nginx配置文件路径
/usr/local/nginx/sbin/nginx  -t
#重新加载
/usr/local/nginx/sbin/nginx -s reload
#查看文件
tail -f access.log 
```



```shell
#复制文件夹
cp -rf daojia daojia_20211216

#压缩
zip -q -r 20211216.zip daojia_20211216
```

## 定时[备份数据库](https://zhuanlan.zhihu.com/p/53911429)

```sh
#!/bin/bash
# a ShellScript for auto DB backup and delete old backup
# chmod 777 backup.sh
# 00 04 * * * /data/backup/backup.sh
# 
backupdir=/data/backup
time=`date +%Y%m%d%H%M%S`

# mysqldump -h 10.0.0.1 -uroot -proot test | gzip > $backupdir/test_$time.sql.gz

find $backupdir -name "*.sql.gz" -type f -mtime +7 -exec rm {} \; > /dev/null 2>&1
```

```
ps -ef | grep nginx

 cd  /usr/local/nginx/conf/

 cd sbin/
 ./nginx -s reload

```

```cmd
location / {

....配置

proxy_connect_timeout 300;#延长为300秒，默认60秒

proxy_send_timeout 300;#延长为300秒，默认60秒

proxy_read_timeout 300;#延长为300秒，默认60秒

}
```



## 图片跨域

```shell
 location ~ .*\.(gif|jpg|jpeg|png)$
        {
                add_header 'Access-Control-Allow-Origin' '*';
                add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';
                error_log off;
        }

```

