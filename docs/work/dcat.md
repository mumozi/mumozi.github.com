# Dcat-Admin常用



## 表格行按钮

```php
//情况一
$grid->actions(function (Grid\Displayers\Actions $actions) {
                $actions->append(new EditShop());
            });
//情况二
$grid->actions(function (Grid\Displayers\Actions $actions) {
                if($actions->row->d_status == 0){
                    $actions->append(new LiquidationEnable('<i class="feather icon-pause-circle"></i>&nbsp;隐藏', 0));
                }else{
                    $actions->append(new LiquidationEnable('<i class="feather icon-play-circle"></i>&nbsp;显示', 1));
                }

            });
//情况三
$grid->actions(function (Grid\Displayers\Actions $actions) {
                $url = 'liquidation_sett/'. $actions->row->bn.'?order_time='. $actions->row->date_time;
                $actions->append('<a href="' . admin_url($url) .'" class="shop-relation-edit-btn"><span class="d-none d-sm-inline"><i class="feather icon-eye"></i>&nbsp;&nbsp;查看</span></a>');
            });
```

## 命令功能

```shell
# 目录更新生成
php artisan admin:export-seed
# 目录导入
 php artisan db:seed --class=AdminTablesSeeder


```

## 过滤器常用

```php
//时间处理 
$filter->whereBetween('add_time', function ($q) {
                    $start = $this->input['start'] ?? null;
                    $end = $this->input['end'] ?? null;

                    if ($start !== null) {
                        $q->where('add_time', '>=', strtotime($start));
                    }

                    if ($end !== null) {
                        $q->where('add_time', '<=', strtotime($end));
                    }
                })->datetime(['format' => 'YYYY-MM-DD']);
//关联多张表查询
 $filter->where('merchant_no', function ($query) {
                    $merchant = DB::select("select merchant_no from sdb_business_merchant where merchant_name like '%{$this->input}%'");
                    $merchant_nos = array_column($merchant, 'merchant_no');
                    $query->whereHas('order', function ($query) use ($merchant_nos){
                        $query->whereIn('merchant_no', $merchant_nos);
                    });
                }, '商户名称');
                $filter->where('merchant_shop_no', function ($query) {
                    $merchant = DB::select("select merchant_no from sdb_business_takeout_merchant where shop_name like '%{$this->input}%'");
                    $merchant_nos = array_column($merchant, 'merchant_no');
                    $query->whereHas('order', function ($query) use ($merchant_nos){
                        $query->whereIn('merchant_shop_no', $merchant_nos);
                    });
                }, '门店名称');
//下拉选择自定义查询
$filter->where('spec_status_text',function ($query){
                    if($this->input > 0)
                    {
                        $query->where('spec_desc', '!=', 's:0:"";');
                    }else{
                        $query->where('spec_desc', '=', 's:0:"";')->orWhere('spec_desc', '=', null);
                    }
                }, '商品规格')->select(\App\Models\Goods::$typeMap);
```

## 导出表格数据

> 导出中所有数据都是原始数据，需要重新处理

```php
$grid->export()->rows(function (array $rows) {
                foreach ($rows as $index => &$row) {
                    $in = empty(unserialize($row['spec_desc']))?0:1;
                    $category = BusinessTakeoutMainBusiness::find($row['shop']['main_category']);
                    $row['shop']['category'] = $category->full_business ?? '-';
                    $row['price'] = sett_format($row['price']);
                    $row['store'] = intval($row['store']);
                    $row['goods_status'] = \App\Models\Goods::$statusMap[$row['goods_status']];
                    $row['spec_status_text'] = \App\Models\Goods::$typeMap[$in];
                }
                return $rows;
            });
```

> 科学计数法

```php
$grid->export()->rows(function (array $rows) {
                foreach ($rows as $index => &$row) {
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
                    $merchant =  BusinessMerchant::where('merchant_no', $row['order']['merchant_no'])->firstOrFail();
                    $row['order']['merchant_no'] = $merchant->merchant_name;
                    $shop =  BusinessTakeoutMerchant::where('merchant_no', $row['order']['merchant_shop_no'])->firstOrFail();
                    $row['order']['merchant_shop_no'] = $shop->shop_name;
                }
                return $rows;
            });
```

## 放大图片失效（跨域问题

```js
//Helpers.js
// 预览图片
    previewImage(src, width, title) {
        let Dcat = this.dcat,
            img = new Image(),
            win = this.isset(window.top) ? top : window,
            clientWidth = Math.ceil(win.screen.width * 0.6),
            clientHeight = Math.ceil(win.screen.height * 0.8);
        ……
       
   //dcat-app.js
        //修改win获取的窗口
        // win = this.isset(window.top) ? top : window,
        // win =  window, -------------a=window
        
        {key:"previewImage",value:function(e,t,n){var r=this.dcat,o=new Image,a=window,i=Math.ceil(.6*a.screen.width),s=Math.ceil(.8*a.screen.height);o.style.display="none",o.style.height="auto",o.style.width=t||"100%",o.src=e,document.body.appendChild(o),r.loading(),o.onload=function(){r.loading(!1);var t=this.width,a=this.height,c=t>i?i:t,l=Math.ceil(c*(a/t));l=l>s?s:l,(n=n||e.split("/").pop()).length>50&&(n=n.substr(0,50)+"..."),layer.open({type:1,shade:.2,title:!1,maxmin:!1,shadeClose:!0,closeBtn:2,content:$(o),area:[c+"px",l+"px"],skin:"layui-layer-nobg",end:……
        
```

## 表单验证规则

```php
//2位小数且大于0小于等于5
$form->text('score','门店评分')->width(7, 3)->rules('numeric|regex:/^[0-9]+(.[0-9]{1,2})?$/|min:0.01|max:5.00', [
                'regex' => '门店评分最多允许2位小数',
                'min' => '门店评分必须大于0',
                'max' => '门店评分必须小于等于5',
            ]);
//数字且0-100
$step->text('service_fee_ratio', '商户服务费比例(%)')->required()->rules('numeric|min:0|max:100', [
                        'numeric' => '商户服务费比例必须为数字',
                        'min' => '商户服务费比例不能小于0',
                        'max' => '商户服务费比例必须小于100',
                    ])->type('number')
                        ->attribute('min', 0)
                        ->attribute('max', 100);
```

## 树形表格，点击没有下一级报错

```php
//原版
return Grid::make() 
//方案一
return (new Grid())->render()
//方案二
return Grid::make()->render(); 
//方案三（不推荐）
//重写Grid类继承,移除魔术方法返回
```

![QQ图片20210609113208](image\QQ图片20210609113208.png)

![QQ图片20210609113215](image\QQ图片20210609113215.png)

![QQ图片20210609113223](image\QQ图片20210609113223.png)

## 视图中ajax请求

```php
<script>
    Dcat.ready(function () {
        $('#comment-btn').on('click', function (e) {
            e.preventDefault();
            let return_id = $('#return_id').val();
            let comment = $('#comment').val();
            console.log(comment);
            if (!comment) {
                Dcat.swal.error('请填写同意退款理由');
                return false;
            }
            let self = this;
            $(self).buttonLoading().removeClass('waves-effect');
            $.ajax({
                url: 'agree',
                dataType: 'json',
                method: 'POST',
                data: {comment: comment,return_id:return_id},
                success: function (res) {
                    if (res.code == 1) {
                        Dcat.success(res.msg);
                        Dcat.reload('{{admin_url('aftersales')}}');
                    }else{
                        Dcat.error(res.msg, null, {timeOut: 3000});
                    }

                },
                error: function (a, b, c) {
                    Dcat.error('服务器出错啦', null, {timeOut: 3000});
                },
                complete: function () {
                    $(self).buttonLoading(false);
                }
            });
        });
    });
</script>
```

## 控制器中ajax请求

```php
  $res = json_encode(array_merge($arr_search,array_values($arr)));
        Admin::script(
            <<<JS
            //数据获取
var res = JSON.parse(JSON.stringify($res));
if(res.length > 0){
    for(var i = 0; i < res.length; i++) {
        $('a[data-key="'+res[i]+'"] i').remove();
        $('a[data-key="'+res[i]).removeClass('grid-table-grid-load-children');
    }
}
$('a[data-depth="1"] i').remove();
$('a[data-depth="1"]').removeClass('grid-table-grid-load-children');
            //触发事件
            $('select.field_d_jump_type').on('change', function (e) {
        var id = e.target.value;
        if(id>0){
            $('[name="d_pric_name"]').val(types[0][id]);
            $('[name="d_jump_url"]').hide();
        }else{
             $('[name="d_pric_name"]').val('');
            $('[name="d_jump_url"]').show();
        }

    })
            //工具按钮
            $grid->tools('<button id="clearCache" class="btn btn-primary">清除缓存</button>');
            //按钮触发事件
        $('#clearCache').on('click', function (e) {
        $('#clearCache').buttonLoading().removeClass('waves-effect');
            $.ajax({
                url: '$url',
                dataType: 'json',
                method: 'get',
                success: function (res) {
                    Dcat.success('清除成功');
                    // if (res.code == 1) {
                    //     Dcat.success(res.msg);
                    //     Dcat.reload('{{admin_url('aftersales')}}');
                    // }else{
                    //     Dcat.error(res.msg, null, {timeOut: 3000});
                    // }

                },
                error: function (a, b, c) {
                    Dcat.success('清除成功');
                },
                complete: function () {
                    $('#clearCache').buttonLoading(false);
                }
            });

    })
JS
        );
```

## 改写ajax（ajaxsubmit）请求过滤同名参数

```php
Admin::script(
		<<<JS
(function ($) {
            //备份jquery的ajax方法
            var _ajax = $.ajax;

            //重写jquery的ajax方法
            $.ajax = function (opt) {
                //备份opt中error和success方法
                var fn = {
                    error: function (XMLHttpRequest, textStatus, errorThrown) { },
                    success: function (data, textStatus) { }
                }
                // var sessionuserid = "";
                // if (typeof parent.getSessionID == "function") {
                //     sessionuserid = parent.getSessionID();
                // }
                let query_arr = opt.data.split("&");
                let result = {};
                query_arr.map(function(value) {
                    let query = value.split("=");
                    result[query[0]] = query[1];
                })
                opt.data = '';
                for (let key in result) {
                    opt.data += key+'='+result[key]+'&';
                }
                opt.data = opt.data.substr(0, opt.data.length - 1);
                console.log(opt.data)
                if (opt.data && !opt.extraData) {   //普通ajax提交
                    // opt.data.sessionuserid = sessionuserid;
                   console.log('===================ajax=====>>>>>>>'+opt.data)
                    fn.data = opt.data;
                } else if (opt.data && opt.extraData) { //ajaxSubmit提交
                    // opt.data = "sessionuserid=" + sessionuserid + "&" + opt.data;
                   // console.log('===================ajaxSubmit=====>>>>>>>'+opt.data)
                    // opt.extraData.sessionuserid = sessionuserid;
                }
                if (opt.error) {
                    fn.error = opt.error;
                }
                if (opt.success) {
                    fn.success = opt.success;
                }
                //扩展增强处理
                var _opt = $.extend(opt, {
                    error: function (XMLHttpRequest, textStatus, errorThrown) {
                        //错误方法增强处理

                        fn.error(XMLHttpRequest, textStatus, errorThrown);
                    },
                    success: function (data, textStatus) {
                        //成功回调方法增强处理

                        fn.success(data, textStatus);
                    }
                });
                _ajax(_opt);
            };
        })(jQuery);
JS
        );

//方案二：

Admin::js('js/ajaxhook.min.js');
Admin::script(
    <<<JS
//启用前必须销毁，否则报错
ah.unProxy();
ah.proxy({
            //请求发起前进入
            onRequest: (config, handler) => {
                // console.log(config.url)
                console.log(config)
                if(config.body == null){
                    if (config.url.indexOf('&') >0) {
                        let url_arr = config.url.split("?");
                        config.url = url_arr[0]+'?'+format_query(url_arr[1]);
                    }
                }else{
                  if ((typeof config.body=='string') && config.body.constructor==String && config.body.indexOf('&') >0) {
                      config.body = format_query(config.body);
                  }
                }
                handler.next(config);
            },
            //请求发生错误时进入，比如超时；注意，不包括http状态码错误，如404仍然会认为请求成功
            onError: (err, handler) => {
                handler.next(err)
            },
            //请求成功后进入
            onResponse: (response, handler) => {
                handler.next(response)
            }
        })

        function format_query(s) {
               let query_arr = s.split("&");
               let result = {};
               query_arr.map(function(value) {
                   let query = value.split("=");
                   result[query[0]] = query[1];
               })
               s = '';
               for (let key in result) {
                   s += key+'='+result[key]+'&';
               }
               return s.substr(0, s.length - 1);
        }
JS
);
```

## 表格批量操作按钮功能（重写）

```php
public function actionScript()
    {
        $warning = __('No data selected!');

        return <<<JS
function (data, target, action) {
    var key = {$this->getSelectedKeysScript()}

    if (key.length === 0) {
        Dcat.warning('{$warning}');
        return false;
    }
    // 设置主键为复选框选中的行ID数组
    action.options.key = JSON.stringify(key);
}
JS;
    }


public function handle(Request $request)
    {
        // 获取选中的 ID 数组
        $keys = json_decode($this->getKey());

        // 获取请求参数
        $action = $request->get('action');

        try {
            DB::transaction(function () use ($keys, $action) {
                foreach ($keys as $key){
                    $hotsearch = BusinessTakeoutHomePageFloorHotsearch::find($key);
                    $hotsearch->hs_status = $action;
                    $hotsearch->save();
                }
            });
        } catch (\Exception $e) {
            return $this->response()->error('服务器出错啦~');
        }

        $message = $action == 1 ? '热搜标签停用成功' : '热搜标签启用成功';
        return $this->response()->success($message)->refresh();
    }
```

