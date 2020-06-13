# 多应用和架构

## 多应用模式

### 1.开启多应用

```bash
composer require topthink/think-multi-app
```

### 2.[路由配置](https://www.kancloud.cn/manual/thinkphp6_0/1037495)

多应用下单独建立route文件夹

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200611090437.png)

<details>

<summary>代码明细</summary>

```php
<?php


namespace app\demo\route;
use think\facade\Route;

Route::rule("test","index/hello","GET");
```

```php
<?php


namespace app\demo\controller;


use app\BaseController;

class index extends BaseController
{
public function hello() {
    return time();
}
}
```

</details>

## 架构分层

!>Tips：`halt()`等同于`dump();die;`

### 1.模型放在公共目录下，区分类型调用

app----common

​	|----business业务逻辑层

​	|----lib基础库

​	|----model模型

​          | -------es

​          | -------mysql

​          | -------redis

### 2.异常处理

- 根目录下`ExceptionHandle.php`,适合API模式（全局

```php
    /**
     * Render an exception into an HTTP response.
     *
     * @access public
     * @param \think\Request   $request
     * @param Throwable $e
     * @return Response
     */
    public function render($request, Throwable $e): Response
    {
        // 添加自定义异常处理机制
        return show(config("status.error"), $e->getMessage());
        // 其他错误交给系统处理
        //return parent::render($request, $e);
    }
```

- 多应用下单文件夹

定义Http.php文件，然后定义这个模块下跳转的错误信息（通过容器Provider定义文件）

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200611125305.png)

 ```php
<?php
namespace app\demo\exception;

use think\exception\Handle;
use think\Response;
use Throwable;

Class Http extends Handle {

    public $httpStatus = 500;

    /**
     * Render an exception into an HTTP response.
     *
     * @access public
     * @param \think\Request   $request
     * @param Throwable $e
     * @return Response
     */
    public function render($request, Throwable $e): Response
    {
        // 添加自定义异常处理机制
        return show(config("status.error"), $e->getMessage(), $this->httpStatus);
    }
}
 ```

```php
<?php
use app\ExceptionHandle;
use app\Request;

// 容器Provider定义文件
return [
    'think\exception\Handle' => "app\\demo\\exception\\Http",
];
```

- 单方法设置报错

  ```php
      /**
       * Render an exception into an HTTP response.
       *
       * @access public
       * @param \think\Request   $request
       * @param Throwable $e
       * @return Response
       */
      public function render($request, Throwable $e): Response
      {
          if(method_exists($e, "getStatusCode")){
              $httpStatus = $e->getStatusCode();
          } else{
              $httpStatus = $this->httpStatus;
          }
          // 添加自定义异常处理机制
          return show(config("status.error"), $e->getMessage(), [], $httpStatus);
      }
  ```

  使用案例

  ```php
  use app\BaseController;
  use think\exception\HttpException;
  
  class E extends BaseController
  {
      public function index()
      {
          throw new HttpException(404, "找不到相应数据");
      }
  }
  ```

  ### 3.中间件配置

  定义`demo/middleware/Checkd.php`[中间件](https://www.kancloud.cn/manual/thinkphp6_0/1037515)，然后定义这个模块下中间件调用地址的配置（通过中间件`demo/middleware.php`定义文件）

  ```php
  <?php
  // 此应用下中间件定义文件
  return [
      app\demo\middleware\Check::class
  ];
  ```

  ```php
  class Check
  {
      public function handle($request, \Closure $next)
      {
          $request->type ="axc";
          return $next($request);
      }
  }
  ```

  !>如果在[路由里绑定中间件配置文件](https://www.kancloud.cn/manual/thinkphp6_0/1127743)，则此路由下不走单应用下配置文件。

