# 笔试⑨

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/IMG_0794(20200828-055452).JPG)

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/IMG_0795(20200828-055452).JPG)



```php
<?php
/**
 * [verifyEmail description]
 * @param  string $str 邮箱字符串
 * @return boolean
 */
function verifyEmail($str){
    // $pattern = '/^\w[-\w.+]*@([A-Za-z0-9][-A-Za-z0-9]+\.)+[A-Za-z]{2,14}$/';
    //@前面的字符可以是英文字母和._- ，._-不能放在开头和结尾，且不能连续出现
    $pattern = '/^[a-z0-9]+([._-][a-z0-9]+)*@([0-9a-z]+\.[a-z]{2,14}(\.[a-z]{2})?)$/i';
    if(preg_match($pattern,$str)){
        return true;
    }else{
        return false;
    }
}
//测试验证邮箱函数的功能
$str = 'Te-st.1@test.com.cn';
if(verifyEmail($str)){
    echo '电子邮箱格式合法';
}else{
    echo '电子邮箱格式不合法';
}
```

```php
<?php


/**
 * 生成随机字符串，数字，大小写字母随机组合
 *
 * @param int $length  长度
 * @param int $type    类型，1 纯数字，2 纯小写字母，3 纯大写字母，4 数字和小写字母，5 数字和大写字母，6 大小写字母，7 数字和大小写字母
 */
function random($length = 6, $type = 1)
{
    // 取字符集数组
    $number = range(0, 9);
    $lowerLetter = range('a', 'z');
    $upperLetter = range('A', 'Z');
    // 根据type合并字符集
    if ($type == 1) {
        $charset = $number;
    } elseif ($type == 2) {
        $charset = $lowerLetter;
    } elseif ($type == 3) {
        $charset = $upperLetter;
    } elseif ($type == 4) {
        $charset = array_merge($number, $lowerLetter);
    } elseif ($type == 5) {
        $charset = array_merge($number, $upperLetter);
    } elseif ($type == 6) {
        $charset = array_merge($lowerLetter, $upperLetter);
    } elseif ($type == 7) {
        $charset = array_merge($number, $lowerLetter, $upperLetter);
    } else {
        $charset = $number;
    }
    $str = '';
    // 生成字符串
    for ($i = 0; $i < $length; $i++) {
        $str .= $charset[mt_rand(0, count($charset) - 1)];
        // 验证规则
        if ($type == 4 && strlen($str) >= 2) {
            if (!preg_match('/\d+/', $str) || !preg_match('/[a-z]+/', $str)) {
                $str = substr($str, 0, -1);
                $i = $i - 1;
            }
        }
        if ($type == 5 && strlen($str) >= 2) {
            if (!preg_match('/\d+/', $str) || !preg_match('/[A-Z]+/', $str)) {
                $str = substr($str, 0, -1);
                $i = $i - 1;
            }
        }
        if ($type == 6 && strlen($str) >= 2) {
            if (!preg_match('/[a-z]+/', $str) || !preg_match('/[A-Z]+/', $str)) {
                $str = substr($str, 0, -1);
                $i = $i - 1;
            }
        }
        if ($type == 7 && strlen($str) >= 3) {
            if (!preg_match('/\d+/', $str) || !preg_match('/[a-z]+/', $str) || !preg_match('/[A-Z]+/', $str)) {
                $str = substr($str, 0, -2);
                $i = $i - 2;
            }
        }
    }
    return $str;
}

echo random(6,7);
```

