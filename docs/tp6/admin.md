# 用户后台管理模块



## 1.导入后台页面

- 在public\static下建立admin文件夹存放


![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200612161613.png)

- 访问测试`http://tp.cn/static/admin/index.html`


![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200612161738.png)

## 2.设置后台登陆

架构

```php
<?php
namespace app\admin\controller;
use app\BaseController;
use think\facade\View;//注意区分门面
class Login extends BaseController
    {
    public function index() {
        return View::fetch();
    }
}
```



![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200612165109.png)

!>报错解决如下

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200612164950.png)

`composer require topthink/think-view`，TP6选择。

使用模板报驱动错误Driver [Think] not supported.
 原因：
 tp6默认只能支持PHP原生模板，但配置文件`config/view.php`配置使用的却是Think
 办法一：使用tp模板进行think-view安装
 composer require topthink/think-view
 办法二：使用原生模板
 到配置文件config/view.php里把'type'          => 'Think'修改为'type'          => 'php'
 办法三：使用原生模板
 `return View::engine('php')->fetch();`

## 3.设计后台用户表

```sql
CREATE TABLE `mall`.`mall_admin_user`  (
  `id` int(10) NOT NULL AUTO_INCREMENT COMMENT '后台用户主键ID',
  `username` varchar(100) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL COMMENT '用户名',
  `password` char(32) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL COMMENT '用户密码',
  `status` tinyint(1) UNSIGNED NOT NULL DEFAULT 0 COMMENT '用户状态 1正常 0待审核 99删除',
  `create_time` int(10) UNSIGNED NOT NULL DEFAULT 0 COMMENT '创建时间',
  `update_time` int(10) UNSIGNED NOT NULL DEFAULT 0 COMMENT '更新时间',
  `last_login_time` int(10) UNSIGNED NOT NULL DEFAULT 0 COMMENT '最后登录时间',
  `last_login_ip` varchar(100) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL COMMENT '最后登录IP',
  `operate_user` varchar(100) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL COMMENT '操作人',
  PRIMARY KEY (`id`, `username`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;
```

## 4.验证码

### 内置验证码

- 安装组件

```bash
composer require topthink/think-captcha
```

- 页面调用

```html
<div>{:captcha_img()}</div>
```

> 说明：调用的是组件里面helper的函数方法。

**优化源码布局-----增加宽高**

```php
/**
 * @param $id
 * @return string
 */
function captcha_img($id = '', $domid = '', $width = '', $height= ''): string
{
    $src = captcha_src($id);
    //添加宽高适配
    $style = '';
    if($width && $height){
        $style = 'width="'.$width.'" height="'.$height.'"';
    }
    $domid = empty($domid) ? $domid : "id='" . $domid . "'";

    return "<img src='{$src}' $style alt='captcha' " . $domid . " onclick='this.src=\"{$src}?\"+Math.random();' />";
}
```

验证码内容配置

`config\captcha.php`

> 可以单独开启验证规则、具体[查询手册](https://www.kancloud.cn/manual/thinkphp6_0/1246402)。

## 5.后台验证

根目录中间件开启全局session，获取验证码检查函数需要。

```php
<?php
// 全局中间件定义文件
return [
    // 全局请求缓存
    // \think\middleware\CheckRequestCache::class,
    // 多语言加载
    // \think\middleware\LoadLangPack::class,
    // Session初始化
     \think\middleware\SessionInit::class
];

```

## 6.用户名密码验证(废弃，使用TP6规则见11)

- 公共模型对象建立

- 状态码建立
- 验证

<details>

<summary>代码明细</summary>

```php
<?php
namespace app\common\model\mysql;

use think\Model;

class AdminUser extends Model
{
    /**
     * 根据用户名获取后端表的数据
     * @param $username
     * @return array|bool|Model|null
     * @throws \think\db\exception\DataNotFoundException
     * @throws \think\db\exception\DbException
     * @throws \think\db\exception\ModelNotFoundException
     */
    public function getAdminUserByUsername($username) {
        if (empty($username)) {
            return false;
        }

        $where = [
            "username" => trim($username),
        ];

        $result = $this->where($where)->find();
        return $result;
    }
}
```

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
    "controller_not_found" => -4,

    //mysql相关的状态配置
    "mysql" => [
        "table_normal" => 1,//正常
        "table_pedding" => 0,//待审核
        "table_delete" => 99,//删除
    ],

];
```

```php
 public function check() {
        if (!$this->request->isPost()) {
            return show(config("status.error"),"请求方式错误");
        }

        //参数校验 1.原生方式 2.TP6方式
        $username = $this->request->param("username","","trim");
        $password = $this->request->param("password","","trim");
        $captcha = $this->request->param("captcha","","trim");

        if (empty($username)||empty($password)||empty($captcha)){
            return show(config("status.error"),"参数不允许为空");
        }
        //需要验证验证码
        if (!captcha_check($captcha)) {
            return show(config("status.error"),"验证码不正确");
        }

        $adminUserObj  = new AdminUser();
        $adminUser =  $adminUserObj->getAdminUserByUsername($username);
        if (empty($adminUser) || $adminUser->status != config("status.mysql.table_normal")) {
            return show(config("status.error"),"不存在该用户");
        }
        $adminUser = $adminUser->toArray();
        //判断密码是否正确
        if ($adminUser['password'] !=md5($password)) {
            return show(config("status.error"),"密码错误");
        }

        return show(config("status.success"),"登陆成功");
    }
```

</details>

!>其中关于对象判断应用的就是对象本身，实际操作的数据需要toArray().

## 7.数据更新以及session处理

- 后台用户模型类增加保存方法
- 当前登陆信息更新到数据库mysql（登陆ip、时间等等）
- 登陆类处理保存当前用户登陆信息到session中`session(config("admin.session_admin"), $adminUser);`其中admin状态码单独放在这个应用下。
- 其中保存数据mysql增加try-catch

<details>

```php
    /**更新信息
     * @param $id
     * @param $data
     * @return AdminUser|bool
     */
    public function updateById($id, $data) {
        $id = intval($id);
        if (empty($id) || empty($data) || !is_array($data)) {
            return false;
        }

        $where = [
            'id' => $id,
        ];

        return $this->where($where)->save($data);
    }
```

```php
  public function check() {
        if (!$this->request->isPost()) {
            return show(config("status.error"),"请求方式错误");
        }

        //参数校验 1.原生方式 2.TP6方式
        $username = $this->request->param("username","","trim");
        $password = $this->request->param("password","","trim");
        $captcha = $this->request->param("captcha","","trim");

        if (empty($username)||empty($password)||empty($captcha)){
            return show(config("status.error"),"参数不允许为空");
        }
        //需要验证验证码
        if (!captcha_check($captcha)) {
            return show(config("status.error"),"验证码不正确");
        }

        try {
            $adminUserObj  = new AdminUser();
            $adminUser =  $adminUserObj->getAdminUserByUsername($username);
            if (empty($adminUser) || $adminUser->status != config("status.mysql.table_normal")) {
                return show(config("status.error"),"不存在该用户");
            }
            $adminUser = $adminUser->toArray();
            //判断密码是否正确
            if ($adminUser['password'] !=md5($password)) {
                return show(config("status.error"),"密码错误");
            }

            //需要记录信息到mysql表里
            $updateData = [
                'last_login_time' => time(),
                'last_login_ip' => request()->ip(),
                'update_time' =>time(),
            ];
            $res = $adminUserObj->updateById($adminUser['id'], $updateData);

            if (empty($res)) {
                return show(config("status.error"),"登陆失败");
            }

        }catch (\Exception $e){
            //todo 记录日志$e->getMessage();
            return show(config("status.error"),"内部异常，登陆失败");
        }

//记录session
        session(config("admin.session_admin"), $adminUser);

        return show(config("status.success"),"登陆成功");
    }
```

</details>

## 8.登陆流程

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200613210333.png)

## 9.按base处理登陆流程

- 后台控制器继承AdminBase，在这里面添加处理验证是否登陆。

```php
<?php


namespace app\admin\controller;


use app\BaseController;
use think\exception\HttpResponseException;

class AdminBase extends BaseController
{
    public $adminUser = null;
    public function initialize()
    {
        parent::initialize(); // TODO: Change the autogenerated stub

        //判断登陆
        if (empty($this->isLogin())) {
            return $this->redirect(url("login/index"), 302);
        }

    }

    /**判断是否登陆
     * @return bool
     */
    public function isLogin(){
        $this->adminUser = session(config("admin.session_admin"));
        if (empty($this->adminUser)) {
            return false;
        }
        return true;
    }

    public function redirect(...$args) {
        throw new HttpResponseException(redirect(...$args));
    }
}
```

>跳转对象实例化。

- 登陆控制器中也要初始化判断，防止一直重定向登陆页面。

```php
class Login extends AdminBase
{
    public function initialize()
    {
        if ($this->isLogin()) {
            return $this->redirect(url("index/index"));
        }
    }
}
```

- 退出登陆控制器。

>redirect（）方法在继承控制器子类可以直接调用。

```php
<?php


namespace app\admin\controller;


use think\facade\View;

class Logout extends AdminBase
{
    public function index() {
        //清除session
        session(config("admin.session_admin"), null);
        //执行跳转
        return redirect(url("login/index"));

    }
}
```

## 10.中间件处理

- 后台控制器继承AdminBase，在这里面**关闭**处理验证是否登陆。

```php
class AdminBase extends BaseController
{
    public $adminUser = null;
    public function initialize()
    {
        parent::initialize(); // TODO: Change the autogenerated stub

        //判断登陆 切换中间件
       /* if (empty($this->isLogin())) {
            return $this->redirect(url("login/index"), 302);
        }*/

    }
```

- 单独应用下开启中间件类(`app\admin\middleware.php`)

```php
<?php
return [
    // Session初始化
     \think\middleware\SessionInit::class,
    app\admin\middleware\Auth::class,
];
```

- ~~创建控制登陆的中间件类~~(`app\admin\middleware\Auth.php`)

```php
<?php


namespace app\admin\middleware;


class Auth
{
    public function handle($request, \Closure $next)
    {

        $response = $next($request);

        if (empty(session(config("admin.session_admin"))) && $request->controller() !="Login") {
            return redirect(url("login/index"));
        }

        return $response;
    }

    public function end(){

    }
}
```

!>优化：后置中间件变前置中间件.

```php
 public function handle($request, \Closure $next)
    {
        if (empty(session(config("admin.session_admin"))) && !preg_match("/login/", $request->pathinfo())) {
            return redirect(url("login/index"));
        }
        //前置处理
        $response = $next($request);

        return $response;
    }
```

## 11.优化-----TP6验证规则

```php
<?php


namespace app\admin\validate;


use think\Validate;

class AdminUser extends Validate
{
    protected $rule = [
        'username' => 'require',
        'password' =>  'require',
        'captcha' => 'require|checkCaptcha',
    ];

    protected $message = [
        'username' => '用户名必须',
        'password' => '密码必须',
        'captcha' => '验证码必须',
    ];

    protected function checkCaptcha($value, $rule, $data =[]){
        if(!captcha_check($value)){
            return "您输入的验证码不正确";
        }
        return true;
    }

}
```



```php
$data = [
            'username' => $username,
            'password' => $password,
            'captcha' => $captcha,
        ];
        //验证规则TP6处理
        $validate = new \app\admin\validate\AdminUser();
        if (!$validate->check($data)) {
            return show(config("status.error"),$validate->getError());
        }
```

## 12.优化-----业务代码抽离到business层

- 业务逻辑全抽离（静态方法）
- 有关于数据查询可以单独分离（静态方法）

```php
<?php


namespace app\admin\business;
use app\common\model\mysql\AdminUser as AdminUserModel;
use think\Exception;

class AdminUser
{
    public static function login($data) {
        try {
            $adminUser = getAdminUserByUsername($data['username']);
            if(empty($adminUser)){
                throw new Exception("不存在该用户");
            }
            //判断密码是否正确
            if ($adminUser['password'] !=md5($data['password'])) {
                throw new Exception("密码错误");
                //return show(config("status.error"),"密码错误");
            }

            //需要记录信息到mysql表里
            $updateData = [
                'last_login_time' => time(),
                'last_login_ip' => request()->ip(),
                'update_time' =>time(),
            ];
            $res = $adminUserObj->updateById($adminUser['id'], $updateData);

            if (empty($res)) {
                throw new Exception("登陆失败");
                //return show(config("status.error"),"登陆失败");
            }

        }catch (\Exception $e){
            //todo 记录日志$e->getMessage();
            throw new Exception("内部异常，登陆失败");
            //return show(config("status.error"),"内部异常，登陆失败");
        }

//记录session
        session(config("admin.session_admin"), $adminUser);
        return true;
        //return show(config("status.success"),"登陆成功");
    }

    /**通过用户名获取用户信息
     * @param $username
     * @return bool
     * @throws \think\db\exception\DataNotFoundException
     * @throws \think\db\exception\DbException
     * @throws \think\db\exception\ModelNotFoundException
     */
    public static function getAdminUserByUsername($username){
        $adminUserObj  = new AdminUserModel();
        $adminUser =  $adminUserObj->getAdminUserByUsername($username);
        if (empty($adminUser) || $adminUser->status != config("status.mysql.table_normal")) {
            return false;
            //throw new Exception("不存在该用户");
            //return show(config("status.error"),"不存在该用户");
        }
        $adminUser = $adminUser->toArray();
        return $adminUser;
    }
}
```

```php
try {
            $result = AdminUser::login($data);
        }catch (Exception $e){
            return show(config("status.error"),$e->getMessage());
        }

        if ($result) {
            return show(config("status.success"),"登陆成功");
        }else{
            return show(config("status.error"),"登陆失败");
        }
```

