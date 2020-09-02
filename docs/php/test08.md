# 笔试八

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/IMG_0779(20200821-014208).JPG)

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/IMG_0780(20200821-014208).JPG)

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/IMG_0781(20200821-014208).JPG)

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200902114023.png)



```php
<?php

$str = null;
$str2 = false;
echo $str == $str2 ? '相等' : '不相等'; 
//相等
$str3 = '';
$str4 = 0;
echo $str3 == $str4 ? '相等' : '不相等'; 
//相等
$str5 = 0;
$str6 = '0';
echo $str5 === $str6 ? '相等' : '不相等'; 
//不相等
//
//
$a1 = null;
$a2 = false;
$a3 = 0;
$a4 = '';
$a5 = '0';
$a6 = 'null';//不是空
$a7 = array();
$a8 = array(array());//不是空
echo empty($a1) ? '1':'0';
echo empty($a2) ? '1':'0';
echo empty($a3) ? '1':'0';
echo empty($a4) ? '1':'0';
echo empty($a5) ? '1':'0';
echo empty($a6) ? '1':'0';
echo empty($a7) ? '1':'0';
echo empty($a8) ? '1':'0';
//11111010
//
//
$test = 'aaaaaa';
$abc = &$test;
unset($test);
echo '===test==='.$test;
echo '===abc==='.$abc;
//unset  ===test======abc===aaaaaa
//如果在函数中 unset() 一个通过引用传递的变量，则只是局部变量被销毁，而在调用环境中的变量将保持调用 unset() 之前一样的值。


$count = 5;
function get_count(){
	static $count = 0;
	return $count++;
}
echo '==count=='.$count;//5
++$count;
echo '==count2=='.get_count();//0
echo '==count3=='.get_count();//1


$GLOBALS['var1'] = 5;
$var2 = 1;
function get_value(){
	global $var2;
	$var1 = 0;
	return $var2++;
}
get_value();
echo '==var1=='.$var1;//5
echo '==var2=='.$var2;//2


function get_arr($arr){
	unset($arr[0]);
}
$arr1 = array(1, 2);
$arr2 = array(1, 2);
// get_arr(&$arr1);//报错
get_arr($arr2);
echo count($arr1);
echo count($arr2);

```

使用五种以上方式获取一个文件的扩展名。

要求：dir/upload.image.jpg，找出 .jpg 或者 jpg ，

必须使用PHP自带的处理函数进行处理，方法不能明显重复，可以封装成函数

```php
function getExt1($filename)
{
   $arr = explode('.',$filename);
   return array_pop($arr);;
}
//strrchr — 查找指定字符在字符串中的最后一次出现
//该函数返回 haystack 字符串中的一部分，这部分以 needle 的最后出现位置开始，直到 haystack 末尾。 
function getExt2($filename)
{
   $ext = strrchr($filename,'.');
   return $ext;
}

//strrpos — 计算指定字符串在目标字符串中最后一次出现的位置
function getExt3($filename)
{
   $pos = strrpos($filename, '.');
   $ext = substr($filename, $pos);
   return $ext;
}

function getExt4($filename)
{
   $arr = pathinfo($filename);
   $ext = $arr['extension'];
   return $ext;
}

function getExt5($filename)
{
   $str = strrev($filename);
   return strrev(strchr($str,'.',true));
}


```

