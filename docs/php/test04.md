# 笔试四

1. ## 简述MVC中push和pull的区别

- Push是控制器把数据处理发送给视图层
- Pull是视图层主动去控制器获取想要的数据。

2. ## 如何优化MYSQL数据库

 选择合适的字段；尽量不使用视图；分库分表，读写分离；设置合适的索引

 

3. ## 关联数组和索引数组的区别

键值不同，关联的key是自己都定义的字符串，索引的key是默认的整数且从0开始

 

4. ## 分别写出以下对应函数

字符串函数：

计算字符串长度：strlen

截取字符串： substr

拼接两个字符串： .

数组函数：

合并2个一维数组: array_merge

获取数组中第一个元素：array_shift

获取数组中最后一个元素： array_pop

 

 

5. ## 简写Ajax

$.post

$.get

 

6. ## isset、empty、is_null的区别

```
isset:判断为null或者未定义的时候返回false
empty：判断为空或者未定义为true
is_null:判断为null为true，未定义报错
```

 

 

7. ## Js如何循环获取多个input框里的数据

```js
var items = $("input");

for(var i=0;i<items.length;i++){

  var val = items[i].value;

}
```



8. ## 写出Git初始上传项目的步骤

```shell
git init 

git remote add origin 远程地址

git add . && git commit –m ‘init’

git push
```



## 9   简述redis 和 memache 缓存的区别

Redis 五种类型，string，list，hash，set，short set ，单线程，运行时分配内存。

Memcache K-V类型，多线程，预分配内存

 

## 10.谈谈之前项目中使用过哪些前端UI框架，优点是什么？

 

Amazeui适配手机跟电脑端，组件很多，而且是面向html5开发的，主要是轻量级使用方便。