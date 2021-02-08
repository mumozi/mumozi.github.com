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

