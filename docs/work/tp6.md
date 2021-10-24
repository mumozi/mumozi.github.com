## tp6 框架运行流程（生命周期）



1、应用入口文件
2、注册自动加载文件
3、实例化系统应用基础类 think\App
4、执行 Http 的 run 方法

> 获取当前请求对象实例保存在容器。
> 初始化 HTTP 应用
> 监听 AppInit（应用初始化）事件
> 监听 HttpRun 事件
> 监听 RouteLoaded 事件
> 监听 HttpEnd 事件

![mpiKcHVi8Q.png!large](tp6.assets/mpiKcHVi8Q.png!large.png)