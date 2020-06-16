# 前端登陆模块

## 架构图

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200614115522.png)

## 用户表设计

```sql
CREATE TABLE `mall`.`mall_user`  (
  `id` int(10) NOT NULL AUTO_INCREMENT COMMENT '主键ID',
  `username` varchar(100) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL COMMENT '用户名',
  `phone_number` varchar(20) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL COMMENT '手机号',
  `password` char(32) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL,
  `ltype` tinyint(1) UNSIGNED NOT NULL DEFAULT 0 COMMENT '登陆方式 默认0手机号登陆 1用户密码登陆',
  `type` tinyint(1) UNSIGNED NOT NULL DEFAULT 0 COMMENT '会话保存天数',
  `sex` tinyint(1) NOT NULL DEFAULT 0 COMMENT '性别',
  `status` tinyint(1) UNSIGNED NOT NULL DEFAULT 0 COMMENT '用户状态 1正常 0待审核 99删除',
  `create_time` int(10) UNSIGNED NOT NULL DEFAULT 0 COMMENT '创建时间',
  `update_time` int(10) UNSIGNED NOT NULL DEFAULT 0 COMMENT '更新时间',
  `operate_user` varchar(100) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL COMMENT '操作人',
  PRIMARY KEY (`id`) USING BTREE,
  INDEX `username`(`username`) USING BTREE,
  INDEX `phone_number`(`phone_number`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;
```

## [开通阿里云短信服务（使用收费）](https://dysms.console.aliyun.com/dysms.htm)

### 1、安装SDK

```bash
 composer require alibabacloud/sdk
```

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200614125952.png)

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200614130027.png)

### 2、阿里云设置(2h人工审核)

- 签名＆模板设置

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200614132355.png)

### 3、api接口查询

- 具体参数设置需要签名模板通过

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200614132731.png)

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200614132828.png)

### 4、api本地调用

- `app\common\lib\sms\AliSms.php`自定义本地发送验证码功能（复制API的demo)
- 抽取参数单独放到`app\api\config\aliyun.php`配置文件
- 

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200614134730.png)

<details>

```php
<?php
//严格模式开启
declare(strict_types=1);
namespace app\common\lib\sms;
use AlibabaCloud\Client\AlibabaCloud;
use AlibabaCloud\Client\Exception\ClientException;
use AlibabaCloud\Client\Exception\ServerException;

class AliSms
{
    /**阿里云发送短信验证码场景
     * @param string $phone
     * @param int $code
     * @return bool
     * @throws ClientException
     */
    public static function sendCode(string $phone, int $code):bool {
        if(empty($phone) || empty($code)) {
            return false;
        }

        AlibabaCloud::accessKeyClient(config("aliyun.access_key_id"), config("aliyun.access_key_secret"))
            ->regionId(config("aliyun.region_id"))
            ->asDefaultClient();

        $templateParam = [
            "code"=>$code,
        ];
        try {
            $result = AlibabaCloud::rpc()
                ->product('Dysmsapi')
                // ->scheme('https') // https | http
                ->version('2017-05-25')
                ->action('SendSms')
                ->method('POST')
                ->host(config("aliyun.host"))
                ->options([
                    'query' => [
                        'RegionId' => "cn-hangzhou",
                        'PhoneNumbers' => $phone,
                        'SignName' => config("aliyun.sign_name"),
                        'TemplateCode' => config("aliyun.template_code"),
                        'TemplateParam' => json_encode($templateParam),
                    ],
                ])
                ->request();
            print_r($result->toArray());
        } catch (ClientException $e) {
            return false;
//            echo $e->getErrorMessage() . PHP_EOL;
        } catch (ServerException $e) {
            return false;
//            echo $e->getErrorMessage() . PHP_EOL;
        }
        return true;
    }
}
```

```php
<?php

return [
    'host' => 'dysmsapi.aliyuncs.com',
    'access_key_id' =>'xxxxx',
    'access_key_secret' =>'xxxxx',
    'region_id' => 'cn-hangzhou',
    'template_code' => 'sms_112121',
    'sign_name' => '商城',
];
```

</details>

## API前后端规范（预定义）

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/image-20200614135204667.png)

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/image-20200614135240562.png)

### 开启路由&定义逻辑方法

- 定义发送短信api路由，用postman工具测试

- 路由层`app\api\route\api.php`

```php
<?php


namespace app\api\route;

use think\facade\Route;

Route::rule("smscode","sms/code","POST");

```

- 控制层`app\api\controller\Sms.php`

```php
<?php

//严格模式开启
declare(strict_types=1);
namespace app\api\controller;


use app\BaseController;

class Sms extends BaseController
{
    public function code() :object {
        return show(config("status.success"), "ok");
    }
}
```

- 测试

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200614140218.png)

- 逻辑层`app\common\business\Sms.php`

```php
<?php

//严格模式开启
declare(strict_types=1);
namespace app\common\business;


class Sms
{
    public static function sendCode() :bool {
        return true;
    }
}
```

!>其中严格模式开启`declare(strict_types=1);`

## 验证电话号码场景

- 验证规则
- 调用

```php
<?php


namespace app\api\validate;


use think\Validate;

class User extends Validate
{
    protected $rule = [
        'username' =>'require',
        'phone_number' => 'require',
    ];

    protected $message = [
        'username' => '用户名必须',
        'phone_number' => '电话号码必须',
    ];

    protected $scene = [
        'send_code' => ['phone_number'],
    ];
}
```
- 控制层
```php
public function code() :object {

        $phoneNumber = input('param.phone_number','','trim');
        $data = [
            'phone_number' => $phoneNumber,
        ];
        try {
            validate(\app\api\validate\User::class)->scene('send_code')->check($data);
        }catch (ValidateException $e){
            return show(config("status.error"), $e->getError());
        }

        //调用business层数据
        if (SmsBus::sendCode($phoneNumber)) {
            return show(config("status.success"), "发送验证码成功");
        }
        return show(config("status.error"), "发送验证码失败");
    }
```
- 业务逻辑层调用短信接口
```php
<?php

//严格模式开启
declare(strict_types=1);

namespace app\common\business;
use app\common\lib\sms\AliSms;

class Sms
{
    public static function sendCode(string $phoneNumber) :bool {

        //生成随机的4位或者6位验证码
        $code = rand(100000, 999999);
        $sms = AliSms::sendCode($phoneNumber, $code);
        if ($sms) {

            //验证码存放redis。并且给出一个失效时间 1分钟
        }
        return true;
    }
}
```

- 测试成功

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200615130308.png)

## 开启电话验证码缓存（redis）

- 开启redis缓存服务，默认6379端口

- 从config公共文件拿到缓存复制到应用下
- 修改默认缓存规则
- 添加redis缓存配置

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/image-20200615152146063.png)

```php
<?php

// +----------------------------------------------------------------------
// | 缓存设置
// +----------------------------------------------------------------------

return [
    // 默认缓存驱动
    'default' => env('cache.driver', 'redis'),

    // 缓存连接方式配置
    'stores'  => [
        'file' => [
            // 驱动方式
            'type'       => 'File',
            // 缓存保存目录
            'path'       => '',
            // 缓存前缀
            'prefix'     => '',
            // 缓存有效期 0表示永久缓存
            'expire'     => 0,
            // 缓存标签前缀
            'tag_prefix' => 'tag:',
            // 序列化机制 例如 ['serialize', 'unserialize']
            'serialize'  => [],
        ],
       // 更多的缓存连接
        'redis' => [
            'host'       => '192.168.0.113',
            'port'       => 6379,
            'type'       => 'redis',
        ],
    ],
];

```
- 业务层代码（其中配置单独分离出来）
```php
<?php

//严格模式开启
declare(strict_types=1);
namespace app\common\business;


use app\common\lib\sms\AliSms;

class Sms
{
    public static function sendCode(string $phoneNumber) :bool {

        //生成随机的4位或者6位验证码
        $code = rand(100000, 999999);
        cache(config("redis.code_pre").$phoneNumber, $code, config("redis.code_expire"));

        $sms = AliSms::sendCode($phoneNumber, $code);
        if ($sms) {

            //验证码存放redis。并且给出一个失效时间 1分钟
            //1.php是否又redis扩展
            //2.开启服务
            cache(config("redis.code_pre").$phoneNumber, $code, config("redis.code_expire"));
        }
        return $sms;
    }
}
```

- 测试
- ![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/image-20200615155642006.png)

## 优化-去除common公共方法

- 抽离【生成随机的4位或者6位验证码】功能，单独创建和数字相关的类库方法
- 业务层调用lib下的Num类库
- 控制层传入验证码长度参数

```php
//生成随机的4位或者6位验证码
        $code = Num::getCode($len);
//============================================//
<?php
//记录＆数据相关的类库
declare(strict_types = 1);
namespace app\common\lib;

class Num
{
    /**获取随机4＆6的验证码
     * @param int $len
     * @return int
     */
    public static function getCode(int $len = 4) : int{
        $code = rand(1000, 9999);
        if ($len == 6) {
            $code = rand(100000, 999999);
        }

        return $code;
    }
}
```



!>思想：代码高度耦合。

## 日志排查启动

- 记录保存在runtime文件夹下对应应用下

```php
Log::info("alisms-sendCode-result".json_encode($result->toArray()));

 Log::error("alisms-sendCode-ClientException".$e->getErrorMessage());

 Log::error("alisms-sendCode-ServerException".$e->getErrorMessage());
```

## Nginx的日志qps分析

```bash
cat tp6.mall.com.access.log | awk '{print $4}' | uniq -c | sort -r
```

## 工厂模式+反射

- 公共lib类`app\common\lib\ClassArr.php`
- 类库里初始化调用插件的数组(例如短信服务接口)
- 编写工厂模式调用（参数判断返回是类库还是静态对象）

```php
<?php


namespace app\common\lib;


class ClassArr
{
    public static function smsClassStat() {
        return [
            'ali' => 'app\common\lib\sms\AliSms',
            'baidu' => 'app\common\lib\sms\BaiduSms',

        ];
    }

    public static function uploadClassStat() {
        return [
          'text' =>  'xxx',
          'image' => 'xxx',
        ];
    }

    public static function initClass($type, $classs, $params = [], $needInstance = false) {
        //如果工厂模式调用的方法是静态的，这里返回类库AliSms
        //非静态则返回对象
        if (!array_key_exists($type, $classs)) {
            return false;
        }
        $className = $classs[$type];
        //new ReflectionClass('A’） => 建立A反射类
        //->newInstanceArgs($args) => 相当于实例化对象
        return $needInstance = true ?(new \ReflectionClass($className))->newInstanceArgs($params) : $className;

    }

}
```

- 业务层代码变更

```php
<?php

//严格模式开启
declare(strict_types=1);
namespace app\common\business;


use app\common\lib\ClassArr;
use app\common\lib\Num;
use app\common\lib\sms\AliSms;

class Sms
{
    public static function sendCode(string $phoneNumber, int $len, string $type = 'ali') :bool {

        //生成随机的4位或者6位验证码
        $code = Num::getCode($len);

        //$sms = AliSms::sendCode($phoneNumber, $code);
        //工厂模式调用反射
        $classStats = ClassArr::smsClassStat();
        $classObj = ClassArr::initClass($type, $classStats);
        $sms = $classObj::sendCode($phoneNumber, $code);

        if ($sms) {

            //验证码存放redis。并且给出一个失效时间 1分钟
            //1.php是否又redis扩展
            //2.开启服务
            cache(config("redis.code_pre").$phoneNumber, $code, config("redis.code_expire"));
        }
        return $sms;
    }
}
```

!>出现报错来源于`app\api\exception\Http.php`，实际定位具体错误到runtime日志里面排查error标记。

> 举一反三其他短信SDK可以参考阿里云实现。另外工厂模式可以分流发短信。

## 前端用户登陆逻辑开发

- 定义登陆场景验证
- 获取控制层参数值进行场景验证
- 异常添加自定义状态码


```php
<?php


namespace app\api\validate;


use think\Validate;

class User extends Validate
{
    protected $rule = [
        'username' =>'require',
        'phone_number' => 'require',
        'code' => 'require|number|min:4',
        'type'=> 'require|in:1,2',
//        'type'=> ["require","in"=>"1,2"],两种不同写法

    ];

    protected $message = [
        'username' => '用户名必须',
        'phone_number' => '电话号码必须',
        'code.require' => '短信验证码必须',
        'code.number' => '短信验证码必须为数字',
        'code.min' => '短信验证码长度不得低于4',
        'type.require' => '类型必须',
        'type.in' => '类型数值错误',
    ];

    protected $scene = [
        'send_code' => ['phone_number'],
        'login' => ['phone_number', 'code', 'type'],
    ];
}
```

- 控制层登陆

```php
<?php

declare(strict_types=1);
namespace app\api\controller;


use app\api\validate\User;
use app\BaseController;

class Login extends BaseController
{
    public function index() : object {
        $phoneNumber = $this->request->param("phone_number", "" , "trim");
        $code = input("param.code", 0, "intval");
        $type = input("param.type", 0, "intval");
        //参数校验
        $data = [
            'phone_number' => $phoneNumber,
            'code' => $code,
            'type' =>  $type,
        ];

        $validate = new User();
        if(!$validate->scene("login")->check($data)){
            return show(config("status.error"),$validate->getError());
        }


        return show(config("status.success"),$validate->getError());
    }
}
```

- 业务层`app\common\business\User.php`
- 添加用户模型层`app\common\model\mysql\User.php`

```php
<?php


namespace app\common\business;
use app\common\model\mysql\User as UserMode;
use think\Exception;

class User
{
    public $userObj = null;
    public function __construct()
    {
        $this->userObj = new UserMode();
    }

    public function login($data) {
        $redisCode = cache(config("redis_code_pre").$data['phone_number']);
        if (empty($redisCode) || $redisCode != $data['code']){
            throw new Exception("不存在该验证码", config("status.code_not_found"));
        }
    }
}
```

- 数据模型类

```php
<?php


namespace app\common\model\mysql;


use think\Model;

class User extends Model
{

    /**自动生成写入时间
     * @var bool
     */
    protected $autoWriteTimestamp = true;
    /**
     * 根据手机号获取用户表的数据
     * @param $phoneNumber
     * @return array|bool|Model|null
     * @throws \think\db\exception\DataNotFoundException
     * @throws \think\db\exception\DbException
     * @throws \think\db\exception\ModelNotFoundException
     */
    public function getUserByPhoneNumber($phoneNumber) {
        if (empty($phoneNumber)) {
            return false;
        }

        $where = [
            "phone_number" => trim($phoneNumber),
        ];

        $result = $this->where($where)->find();
        return $result;
    }

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

}
```

!>`  protected $autoWriteTimestamp = true;`自动开启时间

- 异常补充获取code码

```php
<?php
namespace app\api\exception;

use http\Exception;
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
        //补充异常获取
        if($e instanceof \think\Exception){
            return show($e->getCode(), $e->getMessage());
        }

        if(method_exists($e, "getStatusCode")){
            $httpStatus = $e->getStatusCode();
        } else{
            $httpStatus = $this->httpStatus;
        }
        // 添加自定义异常处理机制
        return show(config("status.error"), $e->getMessage(), [], $httpStatus);
    }
}
```

- 报错检查

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200616012805.png)

根据提示查找出问题，这里是由于数据库字段默认值未设置缘故。

检查成功需要关闭错误异常，优化报错信息。`throw new Exception("数据库内部异常");`对外不暴露内部错误信息。

```php
<?php


namespace app\common\business;
use app\common\model\mysql\User as UserMode;
use think\Exception;

class User
{
    public $userObj = null;
    public function __construct()
    {
        $this->userObj = new UserMode();
    }

    public function login($data) {
        $redisCode = cache(config("redis.code_pre").$data['phone_number']);
        if (empty($redisCode) || $redisCode != $data['code']){
            throw new Exception("不存在该验证码", config("status.code_not_found"));
        }
        //需要去判断表里是否有用户信息
        //生成token
        $user = $this->userObj->getUserByPhoneNumber($data['phone_number']);

        if (!$user) {
            $userName = "sugar商-".$data['phone_number'];
            $userData = [
                'username' => $userName,
                'phone_number' => $data['phone_number'],
                'type' => $data['type'],
                'status' => config("status.mysql.table_normal"),
            ];
            try {
                $this->userObj->save($userData);
                $userId = $this->userObj->id;
            }catch ( \Exception $e){
                throw new Exception("数据库内部异常");
            }

        }
        return true;
    }
}
```

## redis+token组合

- 定义公共类库str，获取登陆token
- 业务层获取token并存到缓存(设置过期时间)
- 控制层取得返回结果

`app\common\lib\Str.php`

```php
<?php


namespace app\common\lib;


class Str
{
    /**生成所需的token
     * @param $string
     * @return string
     */
    public static function getLoginToken($string) {
        //生成token
        //生成一个不会重复的字符串
        $str = md5(uniqid(md5(microtime(true)),true));
        return sha1($str.$string);
    }
}
```

- 业务层

```php
$token = Str::getLoginToken($data['phone_number']);
        $redisData = [
            'id' => $userId,
            'username' => $userName,
        ];

        $res = cache(config("redis.token_pre").$token, $redisData);

        return $res ? ["token" => $token, "username" => $userName] : false;
```

- 设置过期时间

```php
<?php


namespace app\common\lib;


class Time
{
    /**用户登陆到期时间
     * @param $type 1=>7天，2=>30天，默认2
     * @return float|int
     */
    public static function userLoginExpiresTime($type = 2){
        $type = !in_array($type, [1,2]) ? 2 : $type;
        if ($type == 1){
            $day = $type * 7;
        }elseif ($type == 2) {
            $day = $type * 30;
        }
        return $day * 24 * 3600;
    }
}
```



```php
$res = cache(config("redis.token_pre").$token, $redisData, Time::userLoginExpiresTime($data['type']));
```

## 优化代码登陆

- 判断是否post请求(可以加在路由判断里面)
- 登陆逻辑请求加一个try

```php
if(!$this->request->isPost()){
            return show(config("status.error"),"非法请求");
        }


        try {
            $result = (new \app\common\business\User())->login($data);
        }catch (\Exception $e){
            return show($e->getCode(),$e->getMessage());
        }

```

利用AuthBase处理登陆拦截器

- 区分登陆拦截器AuthBase&未登录访问拦截器ApiBase
- 登陆是继承未登录的ApiBase
- 判断用户是否登陆
  - token是否存在
  - cache缓存中是否存在redis的token
  - 未登录提示，注意需要父类**重写show方法**

```php
<?php

/**
 * apiBase 相当于api下的公共控制器。如果不需要登陆场景继承这个控制器
 * 需要登陆继承auth控制器
 */
namespace app\api\controller;


use app\BaseController;
use think\exception\HttpResponseException;

class ApiBase extends BaseController
{
    protected function initialize()
    {
        parent::initialize(); // TODO: Change the autogenerated stub
    }

    //补充
    public function show(...$args){
       throw new HttpResponseException(show(...$args));
    }
}
```

```php
<?php


namespace app\api\controller;



class AuthBase extends ApiBase
{
    public $userId = 0;
    public $username = "";
    public $accessToken = "";

    protected function initialize()
    {
        parent::initialize(); // TODO: Change the autogenerated stub
        $this->accessToken = $this->request->header("access-token");
        if(!$this->accessToken || !$this->isLogin()){
            return $this->show(config("status.not_login"), "没有登陆");
        }
    }

    /**判断用户是否登陆
     * @return bool
     */
    public function isLogin() {
        $userInfo = cache(config("redis.token_pre").$this->accessToken);
        if(!$userInfo){
            return false;
        }
        if (!empty($userInfo['id']) && !empty($userInfo['username'])) {
            $this->userId = $userInfo['id'];
            $this->username = $userInfo['username'];
            return true;
        }
        return false;
    }
}
```

```php
//ApiBase抓取异常
        if($e instanceof \think\exception\HttpResponseException){
            return parent::render($request,  $e);
        }
```

- 测试

![image-20200616133015585](login.assets/image-20200616133015585.png)

## 资源路由&restful用户信息

### 获取用户信息

- [资源路由](https://www.kancloud.cn/manual/thinkphp6_0/1037501)

- 根据id获取用户信息【model-》business-》controller】·
- `Route::resource('user', 'User');`

```php
/**通过ID获取用户数据
     * @param $id
     * @return array|bool|Model|null
     * @throws \think\db\exception\DataNotFoundException
     * @throws \think\db\exception\DbException
     * @throws \think\db\exception\ModelNotFoundException
     */
    public function getUserById($id){
        $id = intval($id);
        if (!$id) {
            return false;
        }
        return $this->find($id);
    }
```

```php
/**返回正常用户数据
     * @param $id
     * @return array
     * @throws \think\db\exception\DataNotFoundException
     * @throws \think\db\exception\DbException
     * @throws \think\db\exception\ModelNotFoundException
     */
    public function getNormalUserById($id){
        $user = $this->userObj->getUserById($id);
        if(!$user || $user->status != config("status.mysql.table_normal")){
            return [];
        }
        return $user->toArray();
    }
```

```php
<?php


namespace app\api\controller;
use app\common\business\User as UserBis;

class User extends AuthBase
{
    public function index(){
        $user = (new UserBis())->getNormalUserById($this->userId);

        //预处理防止暴露其他信息
        $result = [
            "id" => $this->userId,
            "username" => $user["username"],
            "sex" => $user["sex"],
        ];
        return show(config("status.success"), "ok", $result);
    }
}
```

!>预处理数据很好的保护数据，把需要的数据单独包装扔出。

- 测试

![image-20200616135152386](login.assets/image-20200616135152386.png)

### 更新用户信息

- 控制层获取参数预处理

- <details>
  ```php
  /**PUT
       * @return \think\response\Json
       */
      public function update(){
          $username = input("param.username", "", "trim");
          $sex = input("param.sex", "", "intval");
  
          $data = [
              'username' => $username,
              'sex' => $sex,
          ];
      
          $validate = (new UserVal())->scene("update_user");
          if(!$validate->check($data)) {
              return show(config("status.error"), $validate->getError());
          }
          $userBisObj = new UserBis();
          $user = $userBisObj->update($this->userId, $data);
          if (!$user) {
              return show(config("status.error"), "更新失败");
          }
           //todo 用户名被修改，redis数据同步更改
          return show(config("status.success"), "ok");
      }
  ```

  </details>
```
  
- 场景验证抛出异常

- 调用业务层更新方法

  - 判断是否存在该用户（通过id获取用户`$user = $this->getNormalUserById($id);`
  - 检查用户名是否存在（通过用户名获取`$userResult = $this->getNormalUserByUsername($data['username']);`
  - 更新`$this->userObj->updateById($id, $data);`

```php
<?php


namespace app\api\validate;


use think\Validate;

class User extends Validate
{
    protected $rule = [
        'username' =>'require',
        'phone_number' => 'require',
        'code' => 'require|number|min:4',
        'type'=> 'require|in:1,2',
//        'type'=> ["require","in"=>"1,2"],两种不同写法
        'sex' => ["require","in"=>"0,1,2"]

    ];

    protected $message = [
        'username' => '用户名必须',
        'phone_number' => '电话号码必须',
        'code.require' => '短信验证码必须',
        'code.number' => '短信验证码必须为数字',
        'code.min' => '短信验证码长度不得低于4',
        'type.require' => '类型必须',
        'type.in' => '类型数值错误',
        'sex.require' => '性别必须',
        'sex.in' => '性别数值错误',
    ];

    protected $scene = [
        'send_code' => ['phone_number'],
        'login' => ['phone_number', 'code', 'type'],
        'update_user' => ['username', 'sex'],
    ];
}
```

```php
public function update($id, $data){
        $user = $this->getNormalUserById($id);
        if (!$user) {
            throw new \think\Exception("不存在该用户");
        }
        //检查用户名是否存在
        $userResult = $this->getNormalUserByUsername($data['username']);
        if ($userResult && $userResult['id']!=$id) {
            throw new \think\Exception("该用户已经存在，请重新设置");
        }
       return $this->userObj->updateById($id, $data);
    }
```

```php
public function getUserByUsername($username){
        if (empty($username)) {
            return false;
        }
        $where = [
            'username' => $username,
        ];

        return $this->where($where)->find();
    }
```

## 退出登陆

- 清除缓存

```php
<?php


namespace app\api\controller;


class Logout extends AuthBase
{
    public function index() {
        //删除redis token缓存
        $res = cache(config("redis.token_pre").$this->accessToken, NULL);
        if($res){
            return show(config("status.success"), "退出登陆成功");
        }
        return show(config("status.error"), "退出登陆失败");
    }
}
```

## 仓库上传

```bash
git init
git remote add origin https://github.com/mumozi/tp6_mall.git
git add  -A && git commit -m 'init'
git push origin master
```

- [项目地址](https://github.com/mumozi/tp6_mall)

## vue前端安装

- `cnpm install`安装插件
- 修改配置地址如图
- 启动项目`npm run serve`

![image-20200616153224748](login.assets/image-20200616153224748.png)

![image-20200616153500471](login.assets/image-20200616153500471.png)

![image-20200616153514360](login.assets/image-20200616153514360.png)

- 测试

![image-20200616160929272](login.assets/image-20200616160929272.png)

**bug修复**，`method not exists:app\api\controller\Login->controller()`

这个问题在于默认控制器下的方法设置错误，修改为index即可；

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/image-20200616160842202.png)

