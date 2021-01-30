# 安装

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