# SQL常见问题

## 同步数据库错误

### 报错信息

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/image-20201012163505815.png)

!>这是因为当前的MySQL不支持datetime为0的情况。*NO_ZERO_DATE、NO_ZERO_IN_DATE*，把这两个设置去掉。

```sql
-- 查看全局sql_mode:
select @@global.sql_mode;
-- 更新sql_mode
-- 去除*NO_ZERO_DATE、NO_ZERO_IN_DATE*
set  @@global.sql_mode = "ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION"

```

## 命令导入导出

```shell
远程导出：
mysqldump -P [端口] -h [IP] -u [用户] -p test>d:\test.sql

例如：
mysqldump -P 3306 -h 192.168.1.224 -u root -p test>d:\test.sql


远程导入：
mysql -h[IP] -P[端口] -u[用户] -p test<c:\test.sql

例如：
mysql -h 192.168.1.224 -P 3306 -u root -p test<c:\test.sql 
mysql -uroot -p ringier_hk -f  <h:\ringier.sql

source h:/wll/ringier.sql
```

