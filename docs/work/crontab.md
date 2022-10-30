# 定时任务



## `laravel`相关开发

```shell
php artisan make:command updateRecord
php artisan schedule:run
```



## `linux`服务

```shell
service crond start
service crond stop

systemctl start crond.service
systemctl stop crond.service
```



## 创建脚本

```shell
#当前用户创建
crontab -e
#以www用户创建
crontab -u www -e

#创建的脚本内容（结尾换行）
#vi /etc/crontab
* * * * * cd /www/wwwroot/daojia && php artisan schedule:run >> /dev/null 2>&1

#查看任务
crontab -l

#创建的文件地址
cd /var/spool/cron/
#查看日志
tail -f /var/log/cron

#给予日志www写入权限
chown -R www:www storage
chmod -R g+rw storage


```



### 说明：

> 所有用户crontab文件存放的目录,以用户名命名
>
> crontab文件的含义：
>
> 用户所建立的crontab文件中，每一行都代表一项任务，每行的每个字段代表一项设置，它的格式共分为六个字段，前五段是时间设定段，第六段是要执行的命令段，格式如下：
>
> minute hour day month week command
>
> 其中：
>
> minute： 表示分钟，可以是从0到59之间的任何整数。
>
> hour：表示小时，可以是从0到23之间的任何整数。
>
> day：表示日期，可以是从1到31之间的任何整数。
>
> month：表示月份，可以是从1到12之间的任何整数。
>
> week：表示星期几，可以是从0到7之间的任何整数，这里的0或7代表星期日。
>
> command：要执行的命令，可以是系统命令，也可以是自己编写的脚本文件。