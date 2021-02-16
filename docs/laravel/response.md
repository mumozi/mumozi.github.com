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

  

