# 笔试三

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/IMG_0666(20200713-161519).JPG)

## PHP四大主流框架的优缺点总结

### **ThinkPHP**

**优点：**

1. 易于上手，有丰富的中文文档；
2. 框架的兼容性较强，PHP4和PHP5完全兼容、完全支持UTF8等。
3. 适合用于中小项目的开发

**缺点：**

1. 对Ajax的支持不是很好；
2. 目录结构混乱，需要花时间整理；
3. 上手容易，但是深入学习较难。

### **Yii**

**优点：**

1. 纯OOP
2. 用于大规模Web应用
3. 模型使用方便
4. 开发速度快，运行速度也快。性能优异且功能丰富
5. 使用命令行工具。

**缺点：**

1. 对Model层的指导和考虑较少
2. 文档实例较少
3. 英文太多
4. 要求PHP技术精通，OOP编程要熟练！
5. View并不是理想view，理想中的view可能只是html代码，不会涉及PHP代码。

### **CodeIgniter**

**优点**：

Code Igniter推崇“简单就是美”这一原则。没有花哨的设计模式、没有华丽的对象结构，一切都是那么简单。几行代码就能开始运行，再加几 行代码就可以进行输出。可谓是“大道至简”的典范。  配置简单，全部的配置使用PHP脚本来配置，执行效率高；具有基本的路由功能，能够进行一定程度的路  由；具有初步的Layout功能，能够制作一定程度的界面外观；数据库层封装的不错，具有基本的MVC功能. 快速简洁，代码不多，执行性能高，框架简  单，容易上手，学习成本低，文档详细；自带了很多简单好用的library，框架适合小型应用.

**缺点：**

本身的实现不太理想。内部结构过于混乱，虽然简单易用，但缺乏扩展能力。 把Model层简单的理解为数据库操作. 框架略显简单，只能够满足小型应用，略微不太能够满足中型应用需要.

### **Lavarel 框架**

**优点：**
 Laravel 的设计思想是很先进的，非常适合应用各种开发模式TDD, DDD 和BDD，作为一个框
 架，它准备好了一切，composer 是个php 的未来，没有composer，PHP 肯定要走向没落。
 laravel 最大的特点和优秀之处就是集合了php 比较新的特性，以及[各种各样](https://www.baidu.com/s?wd=各种各样&tn=24004469_oem_dg&rsv_dl=gh_pl_sl_csd)的设计模式，
 Ioc 容器，依赖注入等。
**缺点：**
 基于组件式的框架，所以比较臃肿

## PHP常用数组函数

1.array_key_change_case($arr,$case)：将数组所有的键转化为大写或者小写，$case可设为CASE_LOWER或者CASE_UPPER。

2.array_chunk($arr,$number)：把数组分割为有特定($number)个元素的数组块。

3.array_column($arr,$column)：返回数组中某一个单列的值。

4.array_combine($arr1,$arr2)：合并两个数组为一个新数组，并把$arr1的值作为键，$arr2的值作为值。

5.array_count_values(Array('a','b','c','a','b'))：返回数组中所有值出现的次数，函数执行结果：Array ( [a] => 2 [b] => 2 [c] => 1 )。

6.array_diff($arr1,$arr2)：返回两个数组的差集(只比较键值)

7.array_diff_key($arr1,$arr2)：返回两个数组的差集(只比较键名)，该数组返回在$arr1中，但不在 $arr2中的元素。

8.array_intersect($arr1,$arr2)：比较数组，返回两个数组的交集。

9.array_key_exists($key,$arr)：查询数组中是否存在指定的键名。

10.array_keys($arr)：返回数组中所有的键名，并且组成一个新数组。

11.array_map('myFunction',$arr)：将用户自定义函数作为回调函数作用在数组的每个元素上，返回一个新数组。

12.array_merge($arr1,$arr2,$arr3.....)：合并一个或多个元素为一个新数组，如果两个或更多元素有相同的键名，后者会覆盖前者。

13.array_merge_recursive($arr1,$arr2,$arr3.....)：合并一个或多个元素为一个新数组，如果两个或更多元素有相同的键名，后者不会覆盖前者，而会递归创建一个新数组。

14.array_pop($arr)：删除数组中的最后一个元素(出栈)。

15.array_product(Array(1,2,3))：计算数组中所有值的乘积。

16.array_push($arr,'a','b')：将一个或多个元素插入数组的末尾(入栈)。

17.array_rand($arr,number)：返回一个包含随机键名的数组。

18.array_replace($arr1,$arr2)：后面数组的值替换前面数组的值。

19.array_reverse($arr1,$arr2)：将数组中的元素逆序输出(反转)

20.array_search('a',$arr)：在数组中搜索给定的键名，成功的话返回它的键名。

21.array_shift()：删除数组中的第一个元素，并返回被删除元素的值。

22.array_sum()：返回数组中所有值的和。

23.array_unshift()：在数组开头插入一个或多个元素。

24.array_values()：返回数组中所有的值。

25.array_walk($arr,'myFunction')：对数组中的每个成员应用自定义函数。

26.arsort()：对关联数组按照键值降序排列。

27.asort()：对关联数组按照键值升序排列。

28.current()：返回数组中的当前元素(第一个指针指向的元素)。

29.in_array('a',$arr)：搜索数组中是否存在指定的值。

30.krsort()：把数组按照键名降序排列。

31.ksort()：把数组按照键名降升序序排列。

32.list()：把数组中的值赋给一些变量。list($a,$b,$c) = array(1,2,3)。

## 常用mysql优化方式