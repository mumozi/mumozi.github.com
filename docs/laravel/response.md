# 响应重定向

## 响应

### 响应设置

1. 路由和控制器处理完业务都会返回一个发送到浏览器的响应：return；

2. 比如字符串会直接输出，而数组则会输出json 格式，本身是Response 对象；

  ```php 
  return [1, 2, 3]; //输出json 格式
  return response([1, 2, 3]); //同上
  return response()->json([1, 2, 3]); //同上
  ```

3. 如果使用response()输出的话，可以设置状态码和响应头信息；

  ```php
  return response('index', 201); //可以设置HTTP 请求状态码
  ```

4. 也可以给HTTP 添加或修改标头，比如将html 解析模式改成文本plain 模式；

  ```php
  return response('<b>index</b>')->header('Content-Type', 'text/plain'); //文本解析模式
  ```

5. 结合上面的响应操作，再结合view()视图功能，显示纯HTML 代码页面；

  ```php
  return response()->view('task', ['id'=>10], 201)
  ->header('Content-Type', 'text/plain');
  ```

###   路由重定向

1. 重定向使用助手函数redirect()的to()方法，注意需要return 才能跳转；
  ```php 
  return redirect()->to('/'); //跳到首页
  return redirect()->to('task'); //跳转到task
  return redirect()->to('task/url'); //跳转到task/url 
  ```
2. 也可以直接使用快捷方式直接进行跳转；
   
  ```php
	return redirect('/'); //跳到首页
      return redirect('task'); //跳转到task
      return redirect('task/url'); //跳转到task/url
  ```


3. redirect()助手有一个对应的facade 模式对象；
   ```php 
     return Redirect::to('/'); //facade 模式，但需要use 引入
	```
4. 使用redirect()的route()方法，可以跳转到指定的命名路由URI；
	```php 
    return redirect()->route('task.index'); //注意和route()方法区别
   ```

5. 使用redirect()的back()方法，可以重定向到上一个页面中；
```php 
return redirect()->back();
return back(); //快捷方式
```
6. 使用redirect()的action()方法，可以直接重定向到控制器方法；
	```php 
return redirect()->action('TaskController@index'); //需注册路由
return redirect()->action('TaskController@index', ['id'=>10]);
	```
7. 使用redirect()的away()方法，跳转到外部链接；
	```php 
return redirect()->away('http://www.baidu.com'); //不带任何编码
	```
	