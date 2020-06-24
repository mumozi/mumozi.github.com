# php7新特性

## 一、标量类型和返回值类型

代码中通过指定 strict_types的值（1或者0），1表示严格类型校验模式，作用于函数调用和返回语句；0表示弱类型校验模式。
 当值为1时，必须按照设置的类型来定义或输出此类型的值。

## 二、三元运算符

```
$site = isset($_GET['site']) ? $_GET['site'] : '菜鸟教程';`
 变为`$site = $_GET['site'] ?? '菜鸟教程';
```

## 三、太空船运算符（整形、浮点型、字符串都可以比较）

```
<==>`, `$a 和 $b，如果 $a 小于、等于或大于 $b时，它分别返回-1、0或1
```

## 四、常量数组

在 PHP 5.6 中仅能通过 const 定义常量数组，PHP 7 可以通过 define() 来定义。

```php
<?php
// 使用 define 函数来定义数组
define('sites', [
   'Google',
   'Runoob',
   'Taobao'
]);

print(sites[1]);//Runoob

```

## 五、匿名类

PHP 7 支持通过 new class 来实例化一个匿名类，这可以用来替代一些"用后即焚"的完整类定义。

```php
 <?php
interface Logger {
   public function log(string $msg);
}

class Application {
   private $logger;

   public function getLogger(): Logger {
      return $this->logger;
   }

   public function setLogger(Logger $logger) {
      $this->logger = $logger;
   }  
}

$app = new Application;
// 使用 new class 创建匿名类
$app->setLogger(new class implements Logger {
   public function log(string $msg) {
      print($msg);
   }
});

$app->getLogger()->log("我的第一条日志");
?> 

```

## 六、闭包（closure）Closure::call()

PHP 7 的 Closure::call() 有着更好的性能，将一个闭包函数动态绑定到一个新的对象实例并调用执行该函数。

```php
 <?php
class A {
    private $x = 1;
}

// PHP 7 之前版本定义闭包函数代码
$getXCB = function() {
    return $this->x;
};

// 闭包函数绑定到类 A 上
$getX = $getXCB->bindTo(new A, 'A'); 

echo $getX();//1
print(PHP_EOL);

// PHP 7+ 代码
$getX = function() {
    return $this->x;
};
echo $getX->call(new A);//1
?> 

```

## 七、unserialize()

PHP 7 增加了可以为 unserialize() 提供过滤的特性，可以防止非法数据进行代码注入，提供了更安全的反序列化数据。

## 八、intlchar()

PHP 7 通过新的 IntlChar 类暴露出 ICU 中的 Unicode 字符特性。这个类自身定义了许多静态方法用于操作多字符集的 unicode 字符。
 ICU拓展`inlt`快速实现汉字转拼音以及按拼音首字母分组排序http://www.php.cn/php-weizijiaocheng-340398.html

## 九、CSPRNG随机数

PHP 7 通过引入几个 CSPRNG 函数提供一种简单的机制来生成密码学上强壮的随机数。
 `random_bytes()` - 加密生存被保护的伪随机字符串。
 `random_int()` - 加密生存被保护的伪随机整数。

## 十、use语句

PHP 7 可以使用一个 use 从同一个 namespace 中导入类、函数和常量：

```php
<?php
class MathOperations 
{
   protected $n = 10;

   // 求余数运算，除数为 0，抛出异常
   public function doOperation(): string
   {
      try {
         $value = $this->n % 0;
         return $value;
      } catch (DivisionByZeroError $e) {
         return $e->getMessage();
      }
   }
}

$mathOperationsObj = new MathOperations();
print($mathOperationsObj->doOperation()); 

```

## 十二、intdiv()

PHP 7 新增加了 intdiv() 函数，接收两个参数，返回值为第一个参数除于第二个参数的值并取整。

## 十三、Session 选项

php7 以前，我们使用 session 前都是要先代用 session_strat() 函数来初始化的，但这个函数是没有参数可以传的，session 的配置都在 php.ini 文件中。
 在 php7 后 session_start() 可以接受一个 array 作为参数， 用来覆盖 php.ini 文件中设置的会话配置选项。

```php
session_start([
   'cache_limiter' => 'private', //在读取完毕会话数据之后马上关闭会话存储文件
    'cookie_lifetime'=>3600,   //SessionID在客户端Cookie储存的时间，默认是0，代表浏览器一关闭SessionID就作废
    'read_and_close'=>true   //在读取完会话数据之后， 立即关闭会话存储文件，不做任何修改
]);
$_SESSION['name']='quan';
echo $_SESSION['name'];


```

## 十四、废弃特性

1、在 PHP4 中类中的函数可以与类名同名，这一特性在 PHP7 中被废弃，同时会发出一个 E_DEPRECATED 错误。当方法名与类名相同，且类不在命名空间中，同时PHP5的构造函数（__construct）不存在时，会产生一个 E_DEPRECATED 错误。

2、以静态的方式调用非静态方法

3、password_hash() 随机因子选项
 函数原 salt 量不再需要由开发者提供了。函数内部默认带有 salt 能力，无需开发者提供 salt 值。

4、capture_session_meta SSL 上下文选项
 废弃了 "capture_session_meta" SSL 上下文选项。 在流资源上活动的加密相关的元数据可以通过 stream_get_meta_data() 的返回值访问。

## 十五、移除的扩展

ereg（匹配函数）、mssql(数据库)、mysql、sybase_ct（数据库）

## 十六、移除的SAPI

在PHP生命周期的各个阶段，一些与服务相关的操作都是通过SAPI接口实现
 aolserve、apach、apache_hook、apache2filte、caudiu、continuit、isap、milte、nsap、phttp、pi3we、roxe、thttp、tu、web、

