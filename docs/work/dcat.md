# Dcat-Admin常用

## 表格行按钮

```php
//情况一
$grid-&gt;actions(function (Grid\Displayers\Actions $actions) {
                $actions-&gt;append(new EditShop());
            });
//情况二
$grid-&gt;actions(function (Grid\Displayers\Actions $actions) {
                if($actions-&gt;row-&gt;d_status == 0){
                    $actions-&gt;append(new LiquidationEnable('&lt;i class="feather icon-pause-circle"&gt;&lt;/i&gt; 隐藏', 0));
                }else{
                    $actions-&gt;append(new LiquidationEnable('&lt;i class="feather icon-play-circle"&gt;&lt;/i&gt; 显示', 1));
                }

            });
//情况三
$grid-&gt;actions(function (Grid\Displayers\Actions $actions) {
                $url = 'liquidation_sett/'. $actions-&gt;row-&gt;bn.'?order_time='. $actions-&gt;row-&gt;date_time;
                $actions-&gt;append('&lt;a href="' . admin_url($url) .'" class="shop-relation-edit-btn"&gt;&lt;span class="d-none d-sm-inline"&gt;&lt;i class="feather icon-eye"&gt;&lt;/i&gt;  查看&lt;/span&gt;&lt;/a&gt;');
            });
```

## 命令功能

```php
# 目录更新生成
php artisan admin:export-seed
# 目录导入
 php artisan db:seed --class=AdminTablesSeeder
```

## 过滤器常用

```php
//时间处理 
$filter-&gt;whereBetween('add_time', function ($q) {
                    $start = $this-&gt;input['start'] ?? null;
                    $end = $this-&gt;input['end'] ?? null;

                    if ($start !== null) {
                        $q-&gt;where('add_time', '&gt;=', strtotime($start));
                    }

                    if ($end !== null) {
                        $q-&gt;where('add_time', '&lt;=', strtotime($end));
                    }
                })-&gt;datetime(['format' =&gt; 'YYYY-MM-DD']);
//关联多张表查询
 $filter-&gt;where('merchant_no', function ($query) {
                    $merchant = DB::select("select merchant_no from sdb_business_merchant where merchant_name like '%{$this-&gt;input}%'");
                    $merchant_nos = array_column($merchant, 'merchant_no');
                    $query-&gt;whereHas('order', function ($query) use ($merchant_nos){
                        $query-&gt;whereIn('merchant_no', $merchant_nos);
                    });
                }, '商户名称');
                $filter-&gt;where('merchant_shop_no', function ($query) {
                    $merchant = DB::select("select merchant_no from sdb_business_takeout_merchant where shop_name like '%{$this-&gt;input}%'");
                    $merchant_nos = array_column($merchant, 'merchant_no');
                    $query-&gt;whereHas('order', function ($query) use ($merchant_nos){
                        $query-&gt;whereIn('merchant_shop_no', $merchant_nos);
                    });
                }, '门店名称');
```

## 导出表格数据

```php
$grid-&gt;export()-&gt;rows(function (array $rows) {
                foreach ($rows as $index =&gt; &amp;$row) {
                    $row['opening_time'] = !empty($row['opening_time'])?date('Y-m-d', $row['opening_time']):'';
                    $row['shop_city'] = \App\Models\BusinessTakeoutArea::options()[$row['shop_city']];
                    $row['delivery_type'] = BusinessTakeoutMerchant::$deliveryMap[$row['delivery_type']];
                    $row['amount'] = sett_format($row['amount']);
                    
                    $row['order']['code'] = \App\Models\BusinessTakeoutArea::options()[$row['order']['code']];
                    $row['status'] = \App\Models\AftersalesReturnProduct::$statusMap[$row['status']];
                    //取消导出数据科学计数法
                    $row['return_id'] = "\t".$row['return_id'];
                    $row['order_id'] = "\t".$row['order_id'];
                    $row['order']['code'] = \App\Models\BusinessTakeoutArea::options()[$row['order']['code']];
                    $row['status'] = \App\Models\AftersalesReturnProduct::$statusMap[$row['status']];
                    $row['amount'] = sett_format($row['amount']);
                    $merchant =  BusinessMerchant::where('merchant_no', $row['order']['merchant_no'])-&gt;firstOrFail();
                    $row['order']['merchant_no'] = $merchant-&gt;merchant_name;
                    $shop =  BusinessTakeoutMerchant::where('merchant_no', $row['order']['merchant_shop_no'])-&gt;firstOrFail();
                    $row['order']['merchant_shop_no'] = $shop-&gt;shop_name;
                }
                return $rows;
            });
```