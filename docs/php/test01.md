# 笔试一

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200624172525.png)

## 那么我们禁用Cookie之后，有这么几种方式来实现

- l 设置php.ini配置文件中的`session.use_trans_sid = 1`，或者编译时打开打开了“--enable-trans-sid”选项，让PHP自动跨页传递Session ID。
- l 手动通过URL传值、隐藏表单传递Session ID。
- l 用文件、数据库等形式保存Session ID，在跨页过程中手动调用。

```php
<?php

function arraysSum($arr){
	$result = [];
	foreach ($arr as  $value) {
		array_push($result, array_sum($value));
	}
	return  $result;
}

$arr = [[1,2,3] , [4,5,6], [7,8,9]]; var_dump(arraysSum($arr));
```

4.

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200624173001.png)

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200624173010.png)

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200624173017.png)

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200624173025.png)

```bash
5、find . -type f -size +1024M
　du -sh [dirname|filename]
   当前目录的大小：
　　du -sh .
　　当前目录下个文件或目录的大小：
　　du -sh *
　　显示前10个占用空间最大的文件或目录：
　　du -s * | sort -nr | head
```

6、

```php
<?php
//启动session的初始化
session_start();
//注册session变量，赋值为一个用户的名称
$_SESSION["username"]="skygao";
//注册session变量，赋值为一个用户的ID
$_SESSION["uid"]=1;
?>

```

7、

```php
<?php
class File
{
/**
 *获取某个目录下所有文件
 *@param $path文件路径
 *@param $child 是否包含对应的目录
 */public function getFiles($path, $child = false)
    {
        $files = array();
        if (!$child) {
            if (is_dir($path)) {$dp = dir($path);} else {return null;}
            while ($file = $dp->read()) {if ($file != "." && $file != ".." && is_file($path . $file)) {$files[] = $file;}}$dp->close();} else { $this->scanfiles($files, $path);}return $files;}/**
     *@param $files 结果
     *@param $path 路径
     *@param $childDir 子目录名称
     */public function scanfiles(&$files, $path, $childDir = false)
    {
        $dp = dir($path);while ($file = $dp->read()) {if ($file != "." && $file != "..") {
            if (is_file($path . $file)) { //当前为文件
                $files[] = $file;} else {
//当前为目录
                $this->scanfiles($files[$file], $path . $file . DIRECTORY_SEPARATOR, $file);
            }}}$dp->close();}}

```

8、

**·** **用户输入函数（fopen（），file（），require（）），只能用于调用这些函数有相同脚本的拥有者）**

**·** **创建新文件（限制用户只在该用户下拥有目录下创建文件）** 

**·** **用户调用popen（）systen（） exec（）等脚本，只有脚本处在safe_mode_exec_dir配置指令指定的目录中才可能** 

**·** **加强Http认证，认证脚本拥有这的UID的划入认证领域范围内，此外启用安全模式下，不会设置php_auth** 

**·** **mysql服务器所用的用户名必须与调用mysql_connect()文件的拥有者用户名相同**

**·** **受影响的函数变量以及配置命令达到40个**

9、

```php
上个月第一天:
　echo date('Y-m-d', strtotime(date('Y-m-01') . ' -1 month')); // 计算出本月第一天再减一个月
　上个月最后一天:
　echo date('Y-m-d', strtotime(date('Y-m-01') . ' -1 day')); // 计算出本月第一天再减一天
```

## 补充

1、   写出3种方法$array = ([5] =>[name],[7]=>[key])变成$array = (name,key)

方案一：array_values($array)

方案二：$arr = array();

foreach ($array as $value) {

   array_push($arr,$value)

}

2、   写出字符串种连续最长的部分并输出例如abcjhhhhhhioksss输出hhhhh

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200624173508.png)

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200624173520.png)