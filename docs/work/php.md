# 常用php笔记

## 导出excel文件取消科学计数

```php
/**
     * 导出明细
     * "\t"是取消科学计数
     * @param $dates
     */
    public function export()
    {
        $dates = request()->input('key');
        $fileName = '结算单明细表';
        $headArr = ['结算单日期','对账单日期','商户号','商户名称','收款账户名','结算银行','结算账户','联行行号','结算周期','结算金额'];
        $results = DB::select('select real_remit_date,a.sett_date,a.bn,a.bn_name,recaccount_name,open_bank_name,mid_accout_num,bank_line,b.remit_days, sett_money    from sdb_liquidation_remit a
left join sdb_liquidation_merchantinfo b on a.bn=b.bn where real_remit_date in ('.$dates.') order by real_remit_date desc');
        foreach ($results as $v) {
            $v->remit_days = 'T+'.$v->remit_days;
            $v->sett_money = sett_format($v->sett_money);
            $v->bn = "\t".($v->bn);
            $v->mid_accout_num =  "\t".($v->mid_accout_num);
            $v->bank_line =  "\t".($v->bank_line);
        }
        excelExport($fileName , $headArr, $results);
    }
```

```javascript
//点击按钮触发颜色变换
$('.date-button').on('click', function (e) {
//     $.each($(".date-button"),function (){
//          $(this).removeClass("btn-primary");
//          $(this).addClass("btn-outline-primary");
//     })
// e.target.className = "btn btn-primary  date-button";
    if(e.target.innerText == "自定义") {
        $('.custom').show();
    }else{
         $('.custom').hide();
    }

    })
```

## html滚动条

```css
.process .sp_img{
        overflow-x:scroll;
        overflow-y: hidden;
        width: 100%;
        white-space:nowrap;
    }
```

## 获取对象并取指定字段为key

```php
convert_arr_key(array_map('get_object_vars', $order_date), 'order_status');
```

## linux操作

```shell
#查看文件（隐藏
ls -al
#查找zip扩展包
yum search php73|grep zip
#重启php
systemctl restart php73-php-fpm
#php安装位置
whereis php
#文件夹复制
cp -r /home/packageA/* /home/cp/packageB/
#删除
rm -rf /home/packageA

#为文件夹daojia_bak更改拥有者为www
chown -R www daojia_bak/.
#为文件夹daojia_bak更改拥有者组为www
chgrp -R www daojia_bak/.

#打包解包  .tar
tar cvf FileName.tar DirName
tar xvf FileName.tar

#清空文件内容
 echo "" > /data/www/daojia/storage/logs/laravel.log 
#查看当前目录下各个文件及目录占用空间大小
du -sh *

#查看nginx路径
ps aux|grep nginx
#查看nginx配置文件路径
/usr/local/nginx/sbin/nginx  -t
#重新加载
/usr/local/nginx/sbin/nginx -s reload
#查看文件
tail -f access.log 
```

## git常用

```shell
git fetch origin master
#先把远程仓库最新状态拉到本地（以master为例）；
git reset --hard origin/master
#将本地仓库重置到远程仓库最新状态（本地将被覆盖）。

```

## 金额格式化2位

```php
'￥'.sprintf("%.2f", round($amount, 2));
```

## 枚举类错误记录

可[参考文档](https://hyperf.wiki/2.2/#/zh-cn/constants)

```php
<?php

declare(strict_types=1);

namespace App\Constants;

use Hyperf\Constants\AbstractConstants;
use Hyperf\Constants\Annotation\Constants;

/**
 * @Constants
 */
class ErrorCode extends AbstractConstants
{
    /**
     * @Message("Server Error！")
     */
    const SERVER_ERROR = 500;

    /**
     * @Message("系统参数错误")
     */
    const SYSTEM_INVALID = 700;
}

```

!> 用户可以使用 `ErrorCode::getMessage(ErrorCode::SERVER_ERROR)` 来获取对应错误信息。

### 异常错误集成

如果单纯使用 `枚举类`，在异常处理的时候，还是不够方便。所以我们需要自己定义异常类 `BusinessException`，当有异常进来，会根据错误码主动查询对应错误信息。

```php
<?php

declare(strict_types=1);

namespace App\Exception;

use App\Constants\ErrorCode;
use Hyperf\Server\Exception\ServerException;
use Throwable;

class BusinessException extends ServerException
{
    public function __construct(int $code = 0, string $message = null, Throwable $previous = null)
    {
        if (is_null($message)) {
            $message = ErrorCode::getMessage($code);
        }

        parent::__construct($message, $code, $previous);
    }
}

```

### 抛出异常

```php
<?php

declare(strict_types=1);

namespace App\Controller;

use App\Constants\ErrorCode;
use App\Exception\BusinessException;

class IndexController extends AbstractController
{
    public function index()
    {
        throw new BusinessException(ErrorCode::SERVER_ERROR);
    }
}

```

## 文件锁机制

```php
 $fp = fopen(dirname(dirname(__FILE__)).'/config/invitation_lock.txt','r');
        //通过排他锁 锁定该过程
        if(flock($fp,LOCK_EX)){
                dump(1111);
            flock($fp,LOCK_UN);
            fclose($fp);
        }
        dd(222);
```

## 执行超时

```php
//在php.ini里面设置 
    max_execution_time = 1800; 
//通过PHP的ini_set 函数设置 
    ini_set("max_execution_time", "1800"); 
//通过set_time_limit 函数设置 
    set_time_limit(1800) ;
```

