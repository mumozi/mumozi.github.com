# 安装-路由

## 1.1 composer安装

### phpstorm安装代码提示插件

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20210130193037.png)

### 安装Laravel执行命令

`composer create-project --prefer-dist laravel/laravel laravel`

在laravel的文件夹中生成laravel文件包

### 命令去访问测试

`php artisan serve`

## 1.2路由的定义

### 方法说明

1. 在路由定义上，我们采用了::get()这个方法，它接受的就是GET 提交；
2. ::post()、::put()、::delete()是表单和Ajax 的提交接受方式；
3. ::any()表示不管你是哪种提交方式，我智能的全部接收响应；
4. ::match()表示接收你指定的提交方式，用数组作为参数传递；

```php
Route::match(['get', 'post'],'index', function () {
	return 'Hello, World!';
});
```

在路由的规则和闭包区域，我们可以设置和传递路由参数；

```php
Route::get('index/{id}', function ($id) {
return 'Hello, World!'.$id;
});
```

## 1.3创建控制器

- 手动创建

- 命令创建

`php artisan make:controller TaskController`

!>报错[laravle7  路由报错说类不存在。Target class [xxx\] does not exist.](https://segmentfault.com/a/1190000027083761)

**解决方案：**

- 方案一：新写法（引入命名空间）

```php
use App\Http\Controllers\UserController;

Route::get('/users', [UserController::class, 'index']);
```

- 方案二（设置默认空间）

  直接到RouteServiceProvider类文件中找$namespace属性。没有就添加，有就把注释打开。

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20210130201803.png)

## 1.4路由参数

- 正则约束

  ```php
  Route::get('task/read/{id}', 'TaskController@read')
  ->where('id', '[0-9]+'); //单个参数
  ->where(['id'=>'[0-9]+', 'name'=>'[a-z]+']); //多个参数
  ```

  > 间作用域全局范围，可以在模型绑定器里设置,模型绑定器路径为：app\Providers\RouteServiceProvider 的boot()方法；
  >
  > 如果id 已经被全局约束，在某个局部你想让它脱离约束，可以如下操作：`...->where('id', '.*');`

## 1.5路由重定向

1. 可以设置访问一个路由的URI，跳转到另一个路由的URI，具体如下：

  ```php
  Route::redirect('index', 'task');
  Route::redirect('index', 'task', 301); //状态码
  ```

2. 还有一个方法，可以直接让路由跳转返回301 状态码而不用设置：

  ```php
  Route::permanentRedirect('index', 'task');
  
  ```

## 1.6视图路由

1. 在使用视图路由之前，我们先要创建一个视图(MVC)中的V 部分；
2. 使用视图路由，有三个参数：1.URI(必)；2.名称(必)；3.参数(选)；

```php
//参数1：URI，localhost:8000/task
//参数2：view，resources/views/task.blade.php
//参数3：传参，{{$id}}
Route::view('task', 'task', ['id'=>10]);
```

2. 对于视图页面的创建，在resources/views 下创建task.blade.php；

  ```php
  {{--静态页面--}}
  task{{$id}}
  ```

3. 当然，也可以使用助手函数view()方法来实现这个功能；

  ```php
  //这里view()的参数1 是视图名称
  //参数2 传参，可选；
  Route::get('task', function () {
  	return view('task', ['id'=>10]);
  });
  ```

4. 也可以将路由直接指向控制器的方法，通过方法实现view()引入视图；(**常用**)

  ```php
  public function index()
  {
  	return view('task', ['id'=>10]);
  }
  ```

## 1.7路由命名及分组

- ### 命名

  1. 给一个制定好的路由进行命名，可以生成URL 地址或进行重定向；

    ```php
    Route::get('task', 'TaskController@index')
    ->name('task.index');
    ```

  2. 在控制器区域，使用助手函数route()来获取路由生成的URL 地址；

    ```php
    //生成url 地址，http://localhost:8000/task
    route('task.index'); PS：URL 是URI 的子集，更多区别请百度；
    ```

  3. route()助手的第二参数为参数，第三参数为是否包含域名URL；

    ```php
    // http://localhost:8000/task?id=10
    route('task.index', ['id'=>10]);
    // /task?id=10
    $url = route('task.index', ['id'=>10], false);PS：如果需要更改成/task/10 模式，路由需要相应更改task/{id}
    ```

  4. 使用redirect()助手结合route()生成一个重定向跳转，注意不要自我死跳；
  //生成重定向
  return redirect()->route('task.index', ['id'=>10]);

- ### 分组

  1. 路由分组功能是为了让大量路由共享路由属性，包括中间件、命名空间等；

     ```php
     //一个空的分组路由
     Route::group([], function () {
     Route::get('index/{id}', function ($id) {
     	return 'index'.$id;
     });
     Route::get('task/{id}', function ($id) {
     	return 'task'.$id;
     });
     });
     ```

     2. 可以将中间件作用域路由分组中，有两种写法，至于中间件？后续章节讲解；

     ```php
     //引入中间件，方法一
     Route::group(['middleware'=>'中间名'], function () {});
     //引入中间件，方法二
     Route::middleware(['中间件'])->group(function () {});
     ```

     3. 可以设置路由路径前缀，通过prefix 来设置，也有两种方法，具体如下：

     ```php
     //引入路由前缀，方法一
     Route::group(['prefix'=>'api'],function () {});
     //引入路由前缀，方法二
     Route::prefix('api')->group(function () {});
     ```

     4. 可以设置子域名，从而限定路由可执行的域名，有两种方法，具体如下：

     ```php
     //引入子域名，方法一
     Route::group(['domain'=>'127.0.0.1'], function () {});
     //引入子域名，方法二
     Route::domain('127.0.0.1')->group(function () {});
     ```

     5. 可以设置命名空间，让命名空间分配给控制器，让其得以访问，具体如下：

        ```php
        //命名空间，方法一
        Route::group(['namespace'=>'Admin'],function () {});
        //命名空间，方法二
        Route::namespace('Admin')->group(function () {});
        PS：在Controller 目录下创建Admin 目录，再其目录下创建的控制器命名空间如下：
        namespace App\Http\Controllers\Admin;
        ```

     6. 可以设置名称前缀，方式两种，也可以嵌套，具体如下：

        ````php
           //名称前缀，方式一
           Route::group(['as'=>'task.'], function () {
           Route::get('task', 'TaskController@index')->name('index');
           Route::get('task/url', 'TaskController@url');
           });
           //名称前缀，方式二
           Route::name('task.')->group( function () {});
           //生成URL
           $url = route('task.index');
           return $url;
           //嵌套方式命名前缀
           Route::name('task.')->group(function () {
           Route::name('abc.')->group(function () {
               Route::get('task', 'TaskController@index')->name('index');
});
        Route::get('task/url', 'TaskController@url');
        });
        //生成URL
        $url = route('task.abc.index');
        return $url;
        ````

## 1.8单行为控制器

- 创建

```php
php artisan make:controller OneController --invokable
    
    //手工创建
class OneController extends Controller
{
public function __invoke()
{
return '单行为控制器';
}
}
```

单行为控制器，路由定义就不需要指定特定的方法，指定控制器即可；

单行为控制器只是语义上的单行为，并没有限制创建更多方法访问；
`Route::get('one', 'OneController');`

## 1.9路由回退

1. 如果我们跳转到了一个不存在路由时，会产生404 错误，体验不佳；
2. 可以使用回退路由，让不存在的路由自动跳转到你指定的页面去；
3. 注意：由于执行顺序问题，必须把回退路由放在所有路由的最底部；
```php
Route::fallback(function () {
return redirect('/');
});
```
4. 当然，你也可以制作一个自己的404 页面，用回退路由加载这个页面；
```php
Route::fallback(function () {
return view('404');
});
```

## 2.0当前路由

我们可以通过使用::current()系列方法，来获取当前路由的访问信息；
```php
Route::get('index', function () {
	//当前路由信息
	dump(Route::current());
	//返回当前路由的名称
	return Route::currentRouteName();
	//返回当前路由指向的方法
	return Route::currentRouteAction();
})->name('localhost.index');
```