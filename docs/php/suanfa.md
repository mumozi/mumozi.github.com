# 常见算法和排序

```php
<?php
/**
 * [query_search 顺序查找]
 * @Author feimao
 * @Date   2019-02-12
 * 对表中的元素排序无要求，但如果表中各个元素的查找概率并不相等，则应先对元素的查找概率进行排序，使表中元素的查找概率由小到大重新排列，以便提高查找概率。
 * @param  [type]     $arr [description]
 * @param  [type]     $v   [查找的值]
 */
function query_search($arr, $v)
{
    foreach ($arr as $key => $value) {
        if ($value == $v) {
            return $key;
        }
    }
    return -1;
}

/**
 * [bin_search 二分查找]
 * @Author feimao
 * @Date   2019-02-12
 * 折半查找的效率比顺序查找高，但折半查找只适用于有序表，且限于顺序存储结构（对链式存储无效）。
 * @param  [type]     $arr [description]
 * @param  [type]     $low [起始位置]0
 * @param  [type]     $top [终点位置]count($arr)-1
 * @param  [type]     $v   [查找值]
 */
function bin_search($arr, $low, $top, $v)
{
    if ($low <= $top) {
        $mid = floor(($low + $top) / 2);
        if ($arr[$mid] == $v) {
            return $mid;
        } elseif ($arr[$mid] < $v) {
            return bin_search($arr, $mid + 1, $top, $v);
        } else {
            return bin_search($arr, $low, $top+-1, $v);
        }
    } else {
        return -1;
    }
}

/**
 * 冒泡排序
 * 在要排序的一组数中，对当前还未排好的序列，从前往后对相邻的两个数依次进行比较和调整，让较大的数往下沉，较小的往上冒。即，每当两相邻的数比较后发现它们的排序与排序要求相反时，就将它们互换。
 */
function _sort($arr)
{
    $count = count($arr) - 1;
    $temp  = 0;

    for ($i = 0; $i < $count; $i++) {

        for ($j = 0; $j < $count - $i; $j++) {
            if ($arr[$j] < $arr[$j + 1]) {
                $temp        = $arr[$j];
                $arr[$j]     = $arr[$j + 1];
                $arr[$j + 1] = $temp;
            }
        }
    }
    return $arr;
}

/**
 * 快速排序
 * 选择一个基准元素，通常选择第一个元素或者最后一个元素。通过一趟扫描，将待排序列分成两部分，一部分比基准元素小，一部分大于等于基准元素。此时基准元素在其排好序后的正确位置，然后再用同样的方法递归地排序划分的两部分。
 */
function quick_sort($arr)
{
    //判断参数是否是一个数组
    if (!is_array($arr)) {
        return false;
    } else {
        //递归出口:数组长度为1，直接返回数组
        $length = count($arr);

        if ($length <= 1) {
            return $arr;
        }

        //数组元素有多个,则定义两个空数组
        $left = $right = array();
        //使用for循环进行遍历，把第一个元素当做比较的对象
        for ($i = 1; $i < $length; $i++) {
            //判断当前元素的大小
            if ($arr[$i] < $arr[0]) {
                $left[] = $arr[$i];
            } else {
                $right[] = $arr[$i];
            }
        }
        //递归调用
        $left  = quick_sort($left);
        $right = quick_sort($right);
        //将所有的结果合并
        return array_merge($left, array($arr[0]), $right);
    }

    /*
    插入排序
    在要排序的一组数中，假设前面的数已经是排好顺序的，现在要把第 n 个数插到前面的有序数中，使得这 n 个数也是排好顺序的。如此反复循环，直到全部排好顺序。
     */
    // 方式一(从大到小排)
    function quiclySort($arr)
    {
        $count = count($arr);
        for ($i = 1; $i < $count; $i++) {
            $tmp = $arr[$i];
            $j   = $i - 1;
            while ($j >= 0 && $tmp > $arr[$j]) {
                $arr[$j + 1] = $arr[$j--];
            }
            $arr[$j + 1] = $tmp;
        }
        return $arr;
    }
// 方式二(从小到大排)
    function insertSort($arr)
    {
        $len = count($arr);
        for ($i = 1; $i < $len; $i++) {
            $tmp = $arr[$i];
            //内层循环控制，比较并插入
            for ($j = $i - 1; $j >= 0; $j--) {
                if ($tmp < $arr[$j]) {
                    //发现插入的元素要大，交换位置，将后边的元素与前面的元素互换
                    $arr[$j + 1] = $arr[$j];
                    $arr[$j]     = $tmp;
                } else {
                    //如果碰到不需要移动的元素，由于是已经排序好是数组，则前面的就不需要再次比较了。
                    break;
                }
            }
        }
        return $arr;
    }

/*
选择排序
在要排序的一组数中，选出最小的一个数与第一个位置的数交换。然后在剩下的数当中再找最小的与第二个位置的数交换，如此循环到倒数第二个数和最后一个数比较为止。
 */
    function selectSort($arr)
    {
//双重循环完成，外层控制轮数，内层控制比较次数
        $len = count($arr);
        for ($i = 0; $i < $len - 1; $i++) {
            //先假设最小的值的位置
            $p = $i;
            for ($j = $i + 1; $j < $len; $j++) {
                //$arr[$p] 是当前已知的最小值
                if ($arr[$p] > $arr[$j]) {
                    //比较，发现更小的,记录下最小值的位置；并且在下次比较时采用已知的最小值进行比较。
                    $p = $j;
                }
            }
            //已经确定了当前的最小值的位置，保存到$p中。如果发现最小值的位置与当前假设的位置$i不同，则位置互换即可。
            if ($p != $i) {
                $tmp     = $arr[$p];
                $arr[$p] = $arr[$i];
                $arr[$i] = $tmp;
            }
        }
        //返回最终结果
        return $arr;
    }
}

```

