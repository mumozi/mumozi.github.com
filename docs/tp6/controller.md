# TP5和6差异以及控制器

## TP5和6差异

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200609160815.png)

## 控制处理

### 1.后端json输出

```php
 /**
     * 获取\think\response\Json对象实例
     * @param mixed $data    返回的数据
     * @param int   $code    状态码
     * @param array $header  头部
     * @param array $options 参数
     * @return \think\response\Json
     */
    function json($data = [], $code = 200, $header = [], $options = []): Json
    {
        return Response::create($data, 'json', $code)->header($header)->options($options);
    }
/**
*应用
*/
 public function abc(){
        $rest = [
            "status" =>1,
            "message" =>"ok"
        ];
        $token = [
            "Token" => "e235fk55"
        ];
        return json($rest,202, $token);
    }
```

### 2.获取参数值&判断请求

```php
 /**
     * 获取当前请求的参数
     * @access public
     * @param  string|array $name 变量名
     * @param  mixed        $default 默认值
     * @param  string|array $filter 过滤方法
     * @return mixed
     */
    public function param($name = '', $default = null, $filter = '')
        
        
class Index extends BaseController{
         public function request() {
            dump($this->request->param());
            dump($this->request->param("a",1,"intval"));//方案一
        }
}
   
use think\Request;
//不需要继承base
class Demo
{
    public function index(Request $request){
        dump($request->param("abc"));//方案二
        $request->isAjax();
        $request->isPost();

        dump(input("abc"));//方案三

        request()->param("abc");//方案四
    }
}
use think\facade\Request;
Request::param("abc");//方案五
```

!>`$this->request`跟`$this->request()`区别，前一个是容器绑定后一个是当前对象类实例。

### 3.杜绝无效请求

为了体现API模块报错信息，使用魔术方法重写__call,包含模块重写和类方法重写。

### 4.通用化API数据格式数据

- 公共方法可配置在`app\common.php`里

```php
<?php
// 应用公共文件

/**
 * 通用化API数据格式输出
 * @param $status
 * @param string $message
 * @param array $data
 * @param int $httpStatus
 * @return \think\response\Json
 */
function show($status, $message = "error", $data = [], $httpStatus = 200)
{
    $result = [
        "status" => $status,
        "data" => $message,
        "result" => $data
    ];
    return json($result, $httpStatus);
}
```

- 业务状态码可单独配置在`app\config\status.php`里

```php
<?php
/**
 * 该文件存放业务状态码的配置
 */

return [
    "success" => 1,
    "error" => 0,
    "not_login" => -1,
    "user_is_register" => -2,
    "action_not_found" => -3,
    "controller_not_found" => -4
];
```

使用案例：

```php
<?php
namespace app\controller;
class Error
{
    public function __call($name, $arguments)
    {
        // TODO: Implement __call() method.
        return show(config("status.controller_not_found"), "找不到该控制器", null, 400);
        //return show(config("status.action_not_found"), "找不到该{$name}方法" ,null, 404);
    }

}
```

### 5.数据库连接

- 根目录复制`example.env`变成`.env` 修改数据连接

```bash
APP_DEBUG = true

[APP]
DEFAULT_TIMEZONE = Asia/Shanghai

[DATABASE]
TYPE = mysql
HOSTNAME = 127.0.0.1
DATABASE = test
USERNAME = root
PASSWORD = root
HOSTPORT = 3306
CHARSET = utf8
DEBUG = true

[LANG]
default_lang = zh-cn
```

- 测试

```php
<?php
namespace app\controller
    
use app\BaseController;
//use think\facade\Db;

class Data extends BaseController
{
    public function index()
    {
        //通过门获取
        //$user = Db::table("user")->where("id",1)->find();
        //通过容器获取
        $user = app("db")->table("user")->where("id",1)->find();
        dump($user);
    }
}
```

### 6.sql查询

```php
$user = Db::table("user")
    ->where("id",1)
    ->fetchSql()//显示查询语句
    ->find();
//另一种方式（打印最后查询语句）
echo Db::getLastSql();die;
```

!>具体CRUD操作实例方法见[官方文档](https://www.kancloud.cn/manual/thinkphp6_0/1037533)

### 7.模型查询

首先Molde继承，然后引入实际继承类

```php
$user = User::find(1);//对象实例
dump($user->toArray());//打印出结果数组
```

!>具体CRUD操作实例方法见[官方文档](https://www.kancloud.cn/manual/thinkphp6_0/1037585)