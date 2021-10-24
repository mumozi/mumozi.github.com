# php常用方法库

```php
 <?php
// 应用公共文件
use PhpOffice\PhpSpreadsheet\Spreadsheet;
use PhpOffice\PhpSpreadsheet\Writer\Xlsx;

/**
 * [jcode description]
 * @Author sugar
 * @date   2021-03-04
 * @param  [type]     $code [description]
 * @param  [type]     $data [description]
 * @param  string     $url  [description]
 * @return [type]           [description]
 */
function jcode($code, $data = null, $url= '')
{
    if(is_array($code)){
        return json($code);
    }
    return json(['code' => $code, 'url' => $url, 'msg' => $data]);
}

/**
 * [jerror 格式化输出json返回]
 * @Author sugar
 * @date   2020-11-03
 * @param  string     $mess [description]
 * @param  integer    $id   [description]
 * @param  [type]     $data [description]
 * @return [type]           [description]
 */
function jerror($mess = 'failed', $id = 1, $data = null)
{
    return json(['state' => $id, 'mess' => $mess, 'data' => $data]);
}

function jsuccess($data = null, $mess = 'success')
{
    return json(['state' => 0, 'mess' => $mess, 'data' => $data]);
}

/**
 * 获取客户端IP地址
 * @param integer $type 返回类型 0 返回IP地址 1 返回IPV4地址数字
 * @param boolean $adv 是否进行高级模式获取（有可能被伪装）
 * @return mixed
 */
function get_client_ip($type = 0, $adv = false)
{
    $type      = $type ? 1 : 0;
    static $ip = null;
    if ($ip !== null) {
        return $ip[$type];
    }

    if ($adv) {
        if (isset($_SERVER['HTTP_X_FORWARDED_FOR'])) {
            $arr = explode(',', $_SERVER['HTTP_X_FORWARDED_FOR']);
            $pos = array_search('unknown', $arr);
            if (false !== $pos) {
                unset($arr[$pos]);
            }

            $ip = trim($arr[0]);
        } elseif (isset($_SERVER['HTTP_CLIENT_IP'])) {
            $ip = $_SERVER['HTTP_CLIENT_IP'];
        } elseif (isset($_SERVER['REMOTE_ADDR'])) {
            $ip = $_SERVER['REMOTE_ADDR'];
        }
    } elseif (isset($_SERVER['REMOTE_ADDR'])) {
        $ip = $_SERVER['REMOTE_ADDR'];
    }
    // IP地址合法验证
    $long = sprintf("%u", ip2long($ip));
    $ip   = $long ? array($ip, $long) : array('0.0.0.0', 0);
    return $ip[$type];
}

//MD5加密盐值
function md5_salt($str, $salt = 'fycloud')
{
    return md5(md5($str) . $salt);
}

/**
 * 系统非常规MD5加密方法
 * @param  string $str 要加密的字符串
 * @return string
 * @author jry <598821125@qq.com>
 */
function user_md5($str, $auth_key)
{
    return !$str ? false : md5(sha1($str) . $auth_key);
}

//生成随机字符串，ext决定是否需要后边的标点符号部分
function rand_str($length = 16, $char = true, $num = true, $ext = false)
{
    // 密码字符集，可任意添加你需要的字符
    $chars = '';
    if ($char) {
        $chars .= 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ';
    }

    if ($num) {
        $chars .= '0123456789';
    }

    if ($ext) {
        $chars .= '!@#$%^&*()-_ []{}<>~`+=,.;:/?|';
    }

    $rand_str = '';
    for ($i = 0; $i < $length; $i++) {
        // 这里提供两种字符获取方式
        // 第一种是使用 substr 截取$chars中的任意一位字符；
        // 第二种是取字符数组 $chars 的任意元素
        // $rand_str .= substr($chars, mt_rand(0, strlen($chars) – 1), 1);
        $rand_str .= $chars[mt_rand(0, strlen($chars) - 1)];
    }
    return $rand_str;
}

/**
 * @param $data 需要处理前缀的数组
 * @param $prefix   需要添加的前缀名称
 */
function procPrefix($data, $prefix = '', $UntreatedPrefix = [])
{
    $result = [];
    if ($prefix === '') {
        return $data;
    }

    foreach ($data as $key => $value) {
        //增加一个判断，如果前端传来的数据增加了前缀就不做处理，没有加前缀才做处理
        $str = strstr($key, $prefix);
        if ($str || in_array($key, $UntreatedPrefix)) {
            $result[$key] = $value;
            continue;
        }
        $result["{$prefix}{$key}"] = $value;
    }
    return $result;
}

function formatTree($list, $lv = 0, $title = 'ma_name')
{
    $formatTree = [];
    foreach ($list as $key => $val) {
        $title_prefix = '';
        for ($i = 0; $i < $lv; $i++) {
            $title_prefix .= "|---";
        }
        $val['lv']         = $lv;
        $val['namePrefix'] = $lv == 0 ? '' : $title_prefix;
        $val['showName']   = $lv == 0 ? $val[$title] : $title_prefix . $val[$title];
        if (!array_key_exists('children', $val)) {
            array_push($formatTree, $val);
        } else {
            $child = $val['children'];
            unset($val['children']);
            array_push($formatTree, $val);
            $middle     = formatTree($child, $lv + 1, $title); //进行下一层递归
            $formatTree = array_merge($formatTree, $middle);
        }
    }
    return $formatTree;
}

/**
 * 根据配置类型解析配置
 * @param  string $type  配置类型
 * @param  string  $value 配置值
 */
function parse_attr($value, $type = '')
{
    switch ($type) {
        default:
            //callback:callable:param
            //param为字符串，建议不带引号，暂不支持数组
            if (strpos($value, 'callback') === 0) {
                $value_explode = explode(':', $value);
                if (count($value_explode) == 2) {
                    list($flag, $func_name) = $value_explode;
                    $func_param             = '';
                } elseif (count($value_explode) == 3) {
                    list($flag, $func_name, $func_param) = $value_explode;
                }

                //防止参数被引号包裹出错
                $func_param = trim($func_param, "'\"");
                //callable形式如为D('Admin/User')->select
                if (strpos($func_name, '->')) {
                    $func_arr   = explode('->', $func_name);
                    $model_name = trim($func_arr[0], "D('\")");
                    $call_arr[] = db($model_name);
                    $call_arr[] = $func_arr[1];
                    return call_user_func($call_arr, $func_param);
                } else {
                    //callable形式如time
                    return call_user_func($func_name, $func_param);
                }
            }

            //function(){}匿名函数,暂不支持
            if (strpos($value, 'function') === 0) {
                return;
            }

            //解析"1:1:1\r\n2:3:4"格式字符串为数组
            $array = preg_split('/[,;\r\n]+/', trim($value, ",;\r\n"));
            if (strpos($value, ':')) {
                $value = array();
                foreach ($array as $val) {
                    $tmp = explode(':', $val);
                    switch (count($tmp)) {
                        case '2':
                            list($k, $v) = $tmp;
                            $value[$k]   = $v;
                            break;
                        case '3':
                            $value[$tmp[0]] = $tmp;
                            break;
                    }
                }
            } else {
                $value = $array;
            }

            break;
    }
    return $value;
}

/**
 * 循环删除目录和文件
 * @param string $dir_name
 * @return bool
 */
function delete_dir_file($dir_name)
{
    $result = false;
    if (is_dir($dir_name)) {
        if ($handle = opendir($dir_name)) {
            while (false !== ($item = readdir($handle))) {
                if ($item != '.' && $item != '..') {
                    if (is_dir($dir_name . DIRECTORY_SEPARATOR . $item)) {
                        delete_dir_file($dir_name . DIRECTORY_SEPARATOR . $item);
                    } else {
                        unlink($dir_name . DIRECTORY_SEPARATOR . $item);
                    }
                }
            }
            closedir($handle);
            if (rmdir($dir_name)) {
                $result = true;
            }
        }
    }

    return $result;
}

/**
 * excel表格导出
 * @param string $fileName 文件名称
 * @param array $headArr 表头名称
 * @param array $data 要导出的数据
 */

function excelExport($fileName = '', $headArr = [], $data = [])
{
    //默认128M，这里设置1024G预防大数据导出失败
    ini_set('memory_limit','1024M');
    $fileName .= "_" . date("Y_m_d", time()) . ".xls";
    $spreadsheet = new Spreadsheet();
    $objPHPExcel = $spreadsheet->getActiveSheet();
    $key = ord("A"); // 设置表头
    $key2 = ord("@");
    foreach ($headArr as $v) {
        if ($key > ord("Z")) {
            $key2 += 1;
            $key = ord("A");
            $colum = chr($key2) . chr($key);
        } else {
            if ($key2 >= ord("A")) {
                $colum = chr($key2) . chr($key);
            } else {
                $colum = chr($key);
            }
        }
        $objPHPExcel->setCellValue($colum . '1', $v);
        $key += 1;
    }
    $column = 2;
    foreach ($data as $key => $rows) {
        // 行写入
        $span = ord("A");
        $span2 = ord("@");
        foreach ($rows as $keyName => $value) {
            // 列写入
            if ($span > ord("Z")) {
                $span2 += 1;
                $span = ord("A");
                $j = chr($span2) . chr($span);
            } else {
                if ($span2 >= ord("A")) {
                    $j = chr($span2) . chr($span);
                } else {
                    $j = chr($span);
                }
            }
            $objPHPExcel->setCellValue($j . $column, $value);
            $span++;
        }
        $column++;
    }
    ob_end_clean();
    header('Content-Type: application/vnd.ms-excel');
    header('Content-Disposition: attachment;filename="' . $fileName . '"');
    header('Cache-Control: max-age=0');
    $writer = new Xlsx($spreadsheet);
    $writer->save('php://output');
    //删除清空：
    $spreadsheet->disconnectWorksheets();
    unset($spreadsheet);
    exit;
}

// 判断是否是移动端
function ismobile()
{
    // 如果有HTTP_X_WAP_PROFILE则一定是移动设备
    if (isset ($_SERVER['HTTP_X_WAP_PROFILE']))
        return true;
    //此条摘自TPM智能切换模板引擎，适合TPM开发
    if (isset ($_SERVER['HTTP_CLIENT']) && 'PhoneClient' == $_SERVER['HTTP_CLIENT'])
        return true;
    //如果via信息含有wap则一定是移动设备,部分服务商会屏蔽该信息
    if (isset ($_SERVER['HTTP_VIA']))
        //找不到为flase,否则为true
        return stristr($_SERVER['HTTP_VIA'], 'wap') ? true : false;
    //判断手机发送的客户端标志,兼容性有待提高
    if (isset ($_SERVER['HTTP_USER_AGENT'])) {
        $clientkeywords = array(
            'nokia', 'sony', 'ericsson', 'mot', 'samsung', 'htc', 'sgh', 'lg', 'sharp', 'sie-', 'philips', 'panasonic', 'alcatel', 'lenovo', 'iphone', 'ipod', 'blackberry', 'meizu', 'android', 'netfront', 'symbian', 'ucweb', 'windowsce', 'palm', 'operamini', 'operamobi', 'openwave', 'nexusone', 'cldc', 'midp', 'wap', 'mobile'
        );
        //从HTTP_USER_AGENT中查找手机浏览器的关键字
        if (preg_match("/(" . implode('|', $clientkeywords) . ")/i", strtolower($_SERVER['HTTP_USER_AGENT']))) {
            return true;
        }
    }
    //协议法，因为有可能不准确，放到最后判断
    if (isset ($_SERVER['HTTP_ACCEPT'])) {
        // 如果只支持wml并且不支持html那一定是移动设备
        // 如果支持wml和html但是wml在html之前则是移动设备
        if ((strpos($_SERVER['HTTP_ACCEPT'], 'vnd.wap.wml') !== false) && (strpos($_SERVER['HTTP_ACCEPT'], 'text/html') === false || (strpos($_SERVER['HTTP_ACCEPT'], 'vnd.wap.wml') < strpos($_SERVER['HTTP_ACCEPT'], 'text/html')))) {
            return true;
        }
    }
    return false;
}

/**
 * @param $arr
 * @param $key_name
 * @return array
 * 将数据库中查出的列表以指定的 id 作为数组的键名
 */
function convert_arr_key($arr, $key_name)
{
    $arr2 = array();
    foreach ($arr as $key => $val) {
        $arr2[$val[$key_name]] = $val;
    }
    return $arr2;
}

/**
 * 日期选择
 * @param $key
 * @param string $start
 * @param string $end
 * @return string
 */
function date_sql($key, $field , $start = '', $end = ''): string
{
    $sql = ' ';
    switch ($key) {
        case '1':
            $sql = " WHERE ".$field." = curdate()";
            break;
        case '2':
            $start = getStartDate(7, '-');
            $sql = " WHERE ".$field." >= '".$start."' and ".$field." < curdate()";
            break;
        case '3':
            $start = getStartDate(30, '-');
            $sql = " WHERE ".$field." >= '".$start."' and ".$field." < curdate()";
            break;
        case '4':
            if(empty($start)){
                if(!empty($end)){
                    $sql = " WHERE ".$field." <= '".$end."'";
                }else{
                    $sql = " WHERE ".$field." = curdate()";
                }
            }else{
                if(empty($end)){
                    $sql = " WHERE ".$field." >= '".$start."'";
                }else{
                    $sql = " WHERE ".$field." >= '".$start."' and ".$field." <= '".$end."'";
                }
            }
            break;
    }
    return $sql;
}

/**
 * 指定范围的开始日期
 * @param int $interval 日期范围
 * @param string $type 取值类型，-：获取之前日期；+：获取之后的日期
 * @return string
 */
function getStartDate(int $interval,string $type) :string
{
    return date('Y-m-d', strtotime("{$type}{$interval} day"));
}

/**
 * [sett_format 金额格式化]
 * 四舍五入计数法
 * @Author star
 * @date   2021-04-29
 * @param  [type]     $money  [金额]
 * @param  string     $bool   [是否添加负号]
 * @param  string     $symbol [是否添加'¥']
 * @return [type]
 */
function sett_format($money,bool $bool = false,bool $symbol = false)
{
    if(empty($money) || $money==0){
        return '-';
    }
    $res = sprintf("%.2f", round($money, 2));
    if ($bool) {
        $res = '-' . $res;
    }

    if ($symbol) {
        if ($res > 0) {
            $res = '￥' . $res;
        } else {
            $res = '-￥' . abs($res);
        }
    }
    return $res;
}
```



