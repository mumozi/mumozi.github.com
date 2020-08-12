# 笔试六

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/IMG_0764(20200811-155645).JPG)

## 1.什么是面向对象，主要特征

## 2.对json数据格式的理解

```php
1、首先，所有的数据（data）最终都可以分解成三种类型：

　　a) 标量，就是一个单独的字符串或数字；

　　b) 序列，若干个相关连的数据按照一定的顺序连接在一起，又叫数组或者列表；

　　c) 映射，也就是名/值对，又叫散列或者字典；

难怪有人说，有了数组和对象就可以存储一切数据了。

2、json就是一种数据交换格式，规范如下：

　　a) 并列的数据之间用逗号（"，"）分隔；

　　b) 映射用冒号（"："）表示；

　　c) 并列数据的集合（数组）用方括号("[]")表示；

　　d) 映射的集合（对象）用大括号（"{}"）表示。

3、在php中，有两个函数处理json数据：json_encode()和json_decode()；前者用于编码，后者用于解码。

　　a) Json_encode()；

　　　　i. 将一个关联数组转换为json：

　　　　$arr = array ('a'=>1,'b'=>2,'c'=>3,'d'=>4,'e'=>5);
　　　　echo json_encode($arr);

　　　　结果为：

　　　　{"a":1,"b":2,"c":3,"d":4,"e":5}（变成了对象格式）

　　　　ii. 将一个对象转化为json：

　　　　$obj->body = 'another post';

　　　　$obj->id = 21;

　　　　$obj->approved = true;

　　　　$obj->favorite_count = 1;

　　　　$obj->status = NULL;

　　　　echo json_encode($obj);

　　　　结果为：

　　　　{
　　　　　　"body":"another post",

　　　　　　"id":21,

　　　　　　"approved":true,

　　　　　　"favorite_count":1,

　　　　　　"status":null
　　　　}

　　　　iii. 将一个索引数组转化为json：

　　　　$arr = Array('one', 'two', 'three');

　　　　echo json_encode($arr);

　　　　结果为：

　　　　["one","two","three"]

　　　　iv. 如果你需要将"索引数组"强制转化成"对象"，可以这样写json_encode( (object)$arr )；或者：

　　　　json_encode ( $arr, JSON_FORCE_OBJECT )；

　　　　v. Php类转化为json

　　　　class Foo {

　　　　　　const ERROR_CODE = '404';

　　　　　　public $public_ex = 'this is public';

　　　　　　private $private_ex = 'this is private!';

　　　　　　protected $protected_ex = 'this should be protected';
　　　　　　public function getErrorCode() {

　　　　　　return self::ERROR_CODE;

　　　　　　}

　　　　}

　　　　现在，对这个类的实例进行json转换：

　　　　$foo = new Foo;

　　　　$foo_json = json_encode($foo);

　　　　echo $foo_json;

　　　　输出结果是

　　　　{"public_ex":"this is public"}

　　　　除了公开变量（public），其他东西（常量、私有变量、方法等等）都遗失了。

　　b) Json_decode()：

　　用于将json文本转换为相应的PHP数据结构。下面是一个例子：

　　$json = '{"foo": 12345}';
　　$obj = json_decode($json);

　　print $obj->{'foo'}; // 12345

　　通常情况下，json_decode()总是返回一个PHP对象，而不是数组。比如：

　　$json = '{"a":1,"b":2,"c":3,"d":4,"e":5}';
　　var_dump(json_decode($json));

　　结果就是生成一个PHP对象：

　　　　object(stdClass)#1 (5) {

　　　　["a"] => int(1)
　　　　["b"] => int(2)
　　　　["c"] => int(3)
　　　　["d"] => int(4)
　　　　["e"] => int(5)

　　　　}

　　如果想要强制生成PHP关联数组，json_decode()需要加一个参数true：

　　　　$json = '{"a":1,"b":2,"c":3,"d":4,"e":5}';
　　　　var_dump(json_decode($json),true);

　　结果就生成了一个关联数组：

　　array(5) {

　　　　 ["a"] => int(1)
　　 　　["b"] => int(2)
　　 　　["c"] => int(3)


　　 　　["d"] => int(4)
　　 　　["e"] => int(5)

　　}
```



### 9.存储过程

存储过程是一个预编译的SQL语句，优点是允许模块化的设计，就是说只需创建一次，以后在该程序中就可以调用多次。如果某次操作需要执行多次SQL，使用存储过程比单纯SQL语句执行要快。 调用： 1）可以用一个命令对象来调用存储过程。 2）可以供外部程序调用，比如：java程序。

### 8时间处理函数

`date ( string $format [, int $timestamp ] )`

格式化一个本地时间／日期。 timestamp 是可选的，默认值为 time()。

getdate()  取得日期／时间信息

date_default_timezone_set() 设定默认时区。

date_default_timezone_get() 返回默认时区。

strtotime()  将任何字符串的日期时间描述解析为 Unix 时间戳

mktime(hour,minute,second,month,day,year)

## date_default_timezone_set() 设定默认时区。

## date_default_timezone_get() 返回默认时区。

## strtotime()  将任何字符串的日期时间描述解析为 Unix 时间戳

## mktime(hour,minute,second,month,day,year)

返回一个日期的 Unix时间戳

strftime()  根据区域设置格式化本地时间／日期

gettimeofday()返回当前时间信息

microtime()返回当前时间的微秒数0.50587000 1521792423

time()返回当前时间的 Unix时间戳

checkdate: 验证日期的正确性。

bool checkdate ( int $month , int $day , int $year )