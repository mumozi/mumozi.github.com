# ECSTORE 商派电子商务软件

## 1.文档

- [文档手册](https://www.hnqss.cn/html/ecos/doc.html)

## 2.流程

`app\base   cmd update    php5`

### 		1.描述文件

#### app.xml

> 修改: **app/notebook/app.xml**，使**notebook**依附于**desktop**。

```xml
<app version="1.0">
    <name>xxxx管理功能</name>
    <description>xxxx管理</description>
    <author>
        <name>star</name>
        <email>star@etonepay.com</email>
        <url>www.etonepay.com</url>
    </author>
    <version>0.1</version>
    <license>license</license>
    <depends>
        <app>desktop</app>
        <app>ectools</app>
    </depends>
</app>
```

#### desktop.xml

> - 添加后台菜单描述文件: **app/notebook/desktop.xml**

```xml
<desktop>
    <permissions>
        <permission id="cargoflow_supplier" display='true'>供应商管理</permission>
        <permission id="cargoflow_analysis" display='true'>报表</permission>
    </permissions>
    <workground name="货流" id="cargoflow.wrokground.cargoflow" controller="admin_cargoflow" action="index" order='400'>
        <menugroup name="供应商管理">
            <menu app='cargoflow' controller='admin_supplier' action='index' permission='cargoflow_supplier' display='true' order='410' >供应商列表</menu>
        </menugroup>
        。
        。
        。
    </workground>
    <workground name="报表" id="cargoflow.wrokground.analysis" controller="admin_analysis" action="stock" order='410'>
        <menugroup name="报表">
            <menu controller='admin_analysis' action='stock' permission='cargoflow_analysis' display='true' order='60'>库存报表</menu>
            <menu controller='admin_analysis' action='sale' permission='cargoflow_analysis' display='true' order='70'>销售报表</menu>
            <menu controller='admin_analysis' action='days' permission='cargoflow_analysis' display='true' order='80'>门店收银日报</menu>
        </menugroup>
    </workground>
</desktop>

```

#### services.xml

1. **services**是app之间扩展的重要方式。
2. 我们将**services**和**service**看作是**插槽**与**插头**的关系。一个**插槽**可以**插**多个**插头**。
3. 每个app下都会有一个**services.xml**的文件，来描述本app的**services**,以及与本app相关的app的**services**。
4. 可以用**kernel::service('service_name')**获取一个最优先的服务者，或者用**kernel::servicelist('service_name')**获取所有服务提供者的列表。

> 注册服务

```xml
<services>
    <service id="desktop_finder.cargoflow_mdl_supplier">
        <class>cargoflow_finder_supplier</class>
    </service>
    .
    .
    .
</services>
```

### 2.为finder添加查看编辑

```php
desktop_controller:finder($model_object_name, $params) -> void
    类型:
    $model_object_name = string()
    $params =  array(
        'title' => string(),
        'actions' =>  $actions,
        'use_buidinin_set_tag' => bool(), //default: false
        'use_buildin_filter' => bool(), //default: false
        'use_buildin_tagedit' =>  bool(), //default: true
        'use_buildin_set_tag' => bool(), //default: false
        'use_buildin_export' => bool(), //default: false
        'use_buildin_import' => bool(), //default: false
        'allow_detail_popup'=> bool(), //default: false
        'use_view_tab' => bool(), //default: false
        'max_actions' => int(), //defaut: 7
        'delete_confirm_tip' => string(), //default: ''
        'filter' => array())
    $actions = array(
        array(
               'label' => string(),
            'icon' => string(),
            'href' => string))

    参数描述:
        $params: 参数
            * title, finder列表名称
        * actions, 操作面板上的动作
            * use_buildin_set_tag, 是否可以设置tag
            * use_buildin_tagedit, 是否可以tag编辑
            * use_buildin_export, 是否支持导出.
            * use_buildin_import, 是否支持导入
            * use_buildin_filter, 是否支持过滤器
            * allow_detail_popup, 是否支持行数据详细页面弹出
        * use_view_tab, 是否使用finder显示标签
            如果选择开启finder显示标签, 继承desktop_controller的类需要重载 _views 函数
        * max_actions, finder面板支持最大的action数量

    $actions: 操作面板上的动作
        * label 列名称
        * icon 图标
        * href 链接地址

    函数描述:
        输出finder列表

desktop_controller:_views() -> $show_menus
    类型:
        $show_menus = array(
        array(
            'filter' => string(),
        'addon' => int(),
        'href' => string())

    参数描述:
    $show_menus
        * filter 标签过滤器
        * addon 过滤出的数量
        * href 连接地址, 链接地址需要加入 view参数, view参数为, 本标签在
          $show_menus数组里的 key 值,例如:
          index.php?app=notebook&ctl=admin_notebook&act=index&view=1

    函数描述:
        如果在用户控制里调用 finder 函数,use_view_tab设置为true时, finder输出时会
        调用_views()以显示 finder的标签.

```

### 3.命名规则

#### 业务逻辑类


> - 业务逻辑库类名: base_application_dbtable
> - 业务逻辑库存放目录: app/base/lib/application
> - 业务逻辑库文件名: dbtable.php
> - 业务逻辑库完整文件名: app/base/lib/application/dbtable.php
> - 控制器所在app: base

#### 数据库类

> 表名: sdb_b2c_sales_rule_goods
>
> 数据库表名前缀: sdb_
>
> 文件名: sales_rule_goods.php
>
> 存放目录名: app/b2c/dbschema
>
> 完整文件名: app/b2c/dbschema/sales_rule_goods.php
>
> 对应的model类名: b2c_mdl_sales_rule_goods
>
> 所属app: b2c

#### 控制器类


> 控制器类名: b2c_ctl_admin_goods_cat
>
> 控制器存放目录: app/b2c/controller/admin/goods
>
> 控制器文件名: brand.php
>
> 完整的控制器文件名: app/b2c/controller/admin/goods/cat.php
>
> 控制器所在app: b2c

### 4.重组finder列表数据返回

> 在默认的finder列表显示数据中，
>
> 1）finder显示的数据统计(共N条)是由对应的model中的count方法获取。
>
> 2）finder显示的数据是由对应model中的getlist方法获取。
>
> 3）finder列表title是根据对应model通过get_schema放来获取对应的dbschema的结构

<details>


```php
class b2c_mdl_analysis_productsale extends dbeav_model{
    public function count($filter=null){
        $sql = 'SELECT count(*) as _count FROM (SELECT I.goods_id FROM '.
            kernel::database()->prefix.'b2c_orders as O LEFT JOIN '.
            kernel::database()->prefix.'b2c_order_items as I ON O.order_id=I.order_id LEFT JOIN '.
            kernel::database()->prefix.'b2c_goods as G ON G.goods_id=I.goods_id WHERE '.
            'O.disabled=\'false\' and O.pay_status!=\'0\' and '.$this->_filter($filter).' Group By I.goods_id) as tb';
        $row = $this->db->select($sql);
        return intval($row[0]['_count']);
    }

    public function getlist($cols='*', $filter=array(), $offset=0, $limit=-1, $orderType=null){
        $sql = 'SELECT I.goods_id as rownum,G.name as pname,G.bn as bn,sum(I.nums) as saleTimes,sum(I.amount) as salePrice,I.goods_id FROM '.
            kernel::database()->prefix.'b2c_orders as O LEFT JOIN '.
            kernel::database()->prefix.'b2c_order_items as I ON O.order_id=I.order_id LEFT JOIN '.
            kernel::database()->prefix.'b2c_goods as G ON G.goods_id=I.goods_id WHERE '.
            'O.disabled=\'false\' and O.pay_status!=\'0\' and G.goods_id!=\'0\' and '.$this->_filter($filter).' Group By I.goods_id';

        if($orderType)$sql.=' ORDER BY '.(is_array($orderType)?implode($orderType,' '):$orderType);

        $rows = $this->db->selectLimit($sql,$limit,$offset);
        $this->tidy_data($rows, $cols);
        foreach($rows as $key=>$val){
            $rows[$key]['rownum'] = (string)($offset+$key+1);
            $sql = 'SELECT sum(number) as refund_num FROM '.
                kernel::database()->prefix.'b2c_reship_items as I LEFT JOIN '.
                kernel::database()->prefix.'b2c_products as P ON P.product_id=I.product_id '.
                'WHERE P.goods_id='.$val['rownum'];
            $row = $this->db->select($sql);
            $rows[$key]['refund_num'] = intval($row[0]['refund_num']);
            $rows[$key]['refund_ratio'] = isset($val['saleTimes'])?number_format($rows[$key]['refund_num']/$val['saleTimes'],2):0;
            $image = $this->app->model('goods')->dump($val['rownum'],'image_default_id,udfimg,thumbnail_pic');
            $rows[$key]['image_default_id'] = $image['image_default_id'];
            $rows[$key]['udfimg'] = $image['udfimg'];
            $rows[$key]['thumbnail_pic'] = $image['thumbnail_pic'];
            
        }
        return $rows;
    }

    public function get_reship_list($cols='*', $filter=array(), $offset=0, $limit=-1, $orderType=null){
        if(isset($filter['time_from']) && $filter['time_from']){
            $filter['time_from'] = strtotime($filter['time_from']);
            $filter['time_to'] = strtotime($filter['time_to'])+86400;
        }
        $sql = 'SELECT product_id,I.product_name as pname,sum(I.number) as saleTimes FROM '.
            kernel::database()->prefix.'b2c_reship as R LEFT JOIN '.
            kernel::database()->prefix.'b2c_reship_items as I ON R.reship_id=I.reship_id WHERE '.
            'R.disabled="false" and R.t_begin>='.intval($filter['time_from']).' and R.t_begin<'.intval($filter['time_to']).' Group By I.product_id';

        if($orderType)$sql.=' ORDER BY '.(is_array($orderType)?implode($orderType,' '):$orderType);

        $rows = $this->db->selectLimit($sql,$limit,$offset);
        $this->tidy_data($rows, $cols);
        foreach($rows as $key=>$val){
            $goods = $this->app->model('products')->dump($val['product_id'],'goods_id');
            $image = $this->app->model('goods')->dump($goods['goods_id'],'image_default_id,udfimg,thumbnail_pic');
            $rows[$key]['image_default_id'] = $image['image_default_id'];
            $rows[$key]['udfimg'] = $image['udfimg'];
            $rows[$key]['thumbnail_pic'] = $image['thumbnail_pic'];
            
        }
        return $rows;
    }

    public function _filter($filter,$tableAlias=null,$baseWhere=null){
        $where = array(1);
        if(isset($filter['time_from']) && $filter['time_from']){
            $where[] = ' O.createtime >='.strtotime($filter['time_from']);
        }
        if(isset($filter['time_to']) && $filter['time_to']){
            $where[] = ' O.createtime <'.(strtotime($filter['time_to'])+86400);
        }
        if(isset($filter['pname']) && $filter['pname']){
            $where[] = ' G.name LIKE \'%'.$filter['pname'].'%\'';
        }
        if(isset($filter['bn']) && $filter['bn']){
            $where[] = ' G.bn LIKE \''.$filter['bn'].'%\'';
        }
        return implode($where,' AND ');
    }

    public function get_schema(){
        $schema = array (
            'columns' => array (
                'rownum' => array (
                    'type' => 'number',
                    'default' => 0,
                    'label' => app::get('b2c')->_('排名'),
                    'width' => 110,
                    'orderby' => false,
                    'editable' => false,
                    'hidden' => true,
                    'in_list' => true,
                    'default_in_list' => true,
                    'realtype' => 'mediumint(8) unsigned',
                ),
                'pname' => array (
                    'type' => 'varchar(200)',
                    'pkey' => true,
                    'sdfpath' => 'pam_account/account_id',
                    'label' => app::get('b2c')->_('商品名称'),
                    'width' => 210,
                    'searchtype' => 'has',
                    'editable' => false,
                    'in_list' => true,
                    'default_in_list' => true,
                    'realtype' => 'mediumint(8) unsigned',
                ),
                'bn' => array (
                    'required' => true,
                    'default' => 0,
                    'label' => app::get('b2c')->_('商品编号'),
                    'sdfpath' => 'member_lv/member_group_id',
                    'width' => 120,
                    'searchtype' => 'has',
                    'type' => 'varchar(200)',
                    'editable' => true,
                    'filtertype' => 'bool',
                    'filterdefault' => 'true',
                    'in_list' => true,
                    'default_in_list' => true,
                    'realtype' => 'mediumint(8) unsigned',
                ),
                'saleTimes' => array (
                    'type' => 'number',
                    'label' => app::get('b2c')->_('销售量'),
                    'width' => 75,
                    'sdfpath' => 'contact/name',
                    'editable' => true,
                    'filtertype' => 'normal',
                    'filterdefault' => 'true',
                    'in_list' => true,
                    'is_title' => true,
                    'default_in_list' => true,
                    'realtype' => 'varchar(50)',
                ),
                'salePrice' => array (
                    'type' => 'money',
                    'default' => 0,
                    'required' => true,
                    'sdfpath' => 'score/total',
                    'label' => app::get('b2c')->_('销售额'),
                    'width' => 110,
                    'editable' => false,
                    'filtertype' => 'number',
                    'in_list' => true,
                    'default_in_list' => true,
                    'realtype' => 'mediumint(8) unsigned',
                ),
                'refund_num' => array (
                    'type' => 'varchar(200)',
                    'sdfpath' => 'profile/gender',
                    'default' => 1,
                    'required' => true,
                    'label' => app::get('b2c')->_('退换货量'),
                    'orderby' => false,
                    'width' => 110,
                    'editable' => true,
                    'filtertype' => 'yes',
                    'in_list' => true,
                    'default_in_list' => true,
                    'realtype' => 'enum(\'0\',\'1\')',
                ),
                'refund_ratio' => array (
                    'label' => app::get('b2c')->_('退换货率'),
                    'width' => 110,
                    'type' => 'varchar(200)',
                    'orderby' => false,
                    'editable' => false,
                    'filtertype' => 'time',
                    'filterdefault' => true,
                    'in_list' => true,
                    'default_in_list' => true,
                    'realtype' => 'int(10) unsigned',
                ),
            ),
            'idColumn' => 'pname',
            'in_list' => array (
                0 => 'rownum',
                1 => 'pname',
                2 => 'bn',
                3 => 'saleTimes',
                4 => 'salePrice',
                5 => 'refund_num',
                6 => 'refund_ratio',
            ),
            'default_in_list' => array (
                0 => 'rownum',
                1 => 'pname',
                2 => 'bn',
                3 => 'saleTimes',
                4 => 'salePrice',
                5 => 'refund_num',
                6 => 'refund_ratio',
            ),
        );
        return $schema;
    }
}

```

</details>

### 5.事务处理

```php
<?php
function add(){
    ...

    $this->begin();
    $mem_model = &$this->app->model("members");
    if($mem_model->validate($_POST,$message)){
        $id = $mem_model->create($_POST);//在pam表中添加新增会员信息
        if($id!=''&&$id){
            ...
               $data = ...;//会员信息数据
            ...
            $obj_account->fireEvent('register',$data,$id);//在members表中添加新增会员信息
            $this->end(true, app::get('b2c')->_('添加成功!'));
        }else{
            $this->end(false, app::get('b2c')->_('添加失败'));
        }
    }else{
        $this->end(false, $message);
    }
}
```

```php
<?php
function add(){
    ...
    $db = kernel :: database();
    $db->beginTransaction();//事务开始
    $mem_model = &$this->app->model("members");
    if($mem_model->validate($_POST,$message)){
        $id = $mem_model->create($_POST);//在pam表中添加新增会员信息
        if($id!=''&&$id){
            ...
               $data = ...;//会员信息数据
            ...
            $obj_account->fireEvent('register',$data,$id);//在members表中添加新增会员信息
            $db->commit();//提交
            $message = '添加成功';
        }else{
           $db->rollback();
           $message = '添加失败';
        }
    }else{
       $db->rollback();
       $message = '添加失败';
    }
    ...
}
```

### 6.api请求


1. 在系统中可以直接用工具类base_httpclient 来实现.
2. openapi的调用api可以用kernel::openapi_url()生成.（访问当前项目url)

```php
$http = new base_httpclient;
$url =  kernel::openapi_url('openapi.queue','worker',array('task_id'=>$task_id));
$http->post($url,$_POST);
```

```php
//请求post的时候数据要转json
function http_api_post($url, $data = null) {
    $http = new base_httpclient();
    $key = '3105289C8B0e5dD';
    $header['veryText'] = md5($key.date('Y-m-d',time()));
    $header['merchantCode'] = 'MC2374';
    $header['Content-Type'] = 'application/json';
    $json_str = json_encode($data);
    $response = $http->set_timeout(7)->post($url,$json_str, $header);
    logger::info('http_api请求response：'.$response);
    $res = json_decode($response,true);
    if($res['code'] == '200'){
        return $res['data'];
    }
    return null;
}
```



## 3.常用功能

### 1.状态修改按钮

- 视图

```html
<div class="table-action">
        <{button type='button' label=$___a='拒绝'|t:'site' id="refuse-form-submit" }>
    </div>
    
    
<script>
    (function(){
        var order_id = '<{$order.id}>';
        var refuse_btn =$('refuse-form-submit');
        
        refuse_btn.addEvent('click',function () {
            new Request.JSON({
                url:'index.php?app=cargoflow&ctl=admin_book_order&act=refuse',
                method:'post',
                data:'id='+order_id,
                onComplete:function(res){
                    if (res.success != undefined) {
                        try{
                            var _dialogIns = refuse_btn.getParent('.dialog').retrieve('instance');
                        }catch(e){}

                        if(_dialogIns)
                        {
                            _dialogIns.close();
                            window.finderGroup['<{$env.get._finder.finder_id}>'].refresh();
                        }
                    } else {
                        MessageBox.error(res.error);
                    }
                }
            }).send();
        });

    })();
</script>
```

- 控制器

  ```php
      function refuse()
      {
          $this->begin();
          $id = isset($_POST['id']) ? intval($_POST['id']) : 0;
          if (!$id) {
              $this->end(false,app::get('cargoflow')->_('操作失败，参数错误'));
          }
  
          $order_obj = $this->app->model('book_order');
          $order_exist = $order_obj->count(array('id'=>$id,'status'=>'0'));
          if (!$order_exist) {
              $this->end(false,app::get('cargoflow')->_('订货单不存在'));
          }
  
          $update_res = $order_obj->update(array('status'=>'2'),array('id'=>$id));
          if (!$update_res) {
              $this->end(false,app::get('cargoflow')->_('系统错误'));
          }
  
          $this->end(true,app::get('cargoflow')->_('操作成功'));
  
  
      }
  ```

  

### 2.打印

- 视图按钮

  ```html
  <div class="table-action">
      <{button type='button' label=$___a='打印'|t:'site' id="desk-form-submit" }>
      <{button type='button' label=$___a='作废'|t:'site' id="refuse-form-submit" }>
      <{button type='button' label=$___a='审核通过'|t:'site' id="agree-form-submit" }>
  </div>
      
      <script>
      (function(){
          var order_id = '<{$order.id}>';
          var _form = $('add_link');
          var btn =$('desk-form-submit');
          var refuse_btn =$('refuse-form-submit');
          var agree_btn =$('agree-form-submit');
          if('<{$order.status}>' != '2'){
              agree_btn.hide();
          }
          if('<{$order.status}>' == '1' || '<{$order.status}>' == '3' ){
              refuse_btn.hide();
          }
          btn.addEvent('click',function(){
              try{
                  var _dialogIns = btn.getParent('.dialog').retrieve('instance');
              }catch(e){}
  
              if(_dialogIns)
              {
                  _dialogIns.close();
              }
              var id = _form.getAttribute('data-id');
              window.open('index.php?app=cargoflow&ctl=admin_purchase_order&act=printing&p[0]=' + id, '_blank');
          });
          agree_btn.addEvent('click',function(){
              new Request.JSON({
                  url:'index.php?app=cargoflow&ctl=admin_purchase_order&act=agree',
                  method:'post',
                  data:'id='+order_id,
                  onComplete:function(res){
                      if (res.success != undefined) {
                          try{
                              var _dialogIns = refuse_btn.getParent('.dialog').retrieve('instance');
                          }catch(e){}
  
                          if(_dialogIns)
                          {
                              _dialogIns.close();
                              window.finderGroup['<{$env.get._finder.finder_id}>'].refresh();
                          }
                      } else {
                          MessageBox.error(res.error);
                      }
                  }
              }).send();
          });
  
          refuse_btn.addEvent('click',function () {
              new Request.JSON({
                  url:'index.php?app=cargoflow&ctl=admin_purchase_order&act=refuse',
                  method:'post',
                  data:'id='+order_id,
                  onComplete:function(res){
                      if (res.success != undefined) {
                          try{
                              var _dialogIns = refuse_btn.getParent('.dialog').retrieve('instance');
                          }catch(e){}
  
                          if(_dialogIns)
                          {
                              _dialogIns.close();
                              window.finderGroup['<{$env.get._finder.finder_id}>'].refresh();
                          }
                      } else {
                          MessageBox.error(res.error);
                      }
                  }
              }).send();
          });
  
  
      })();
  </script>
  ```

- 控制器

  ```php
  //所有取值代码跟显示是一样的，只不过display不同    
  function printing($id=null){
          $orderObj = $this->app->model('purchase_order');
          $orderItemObj = $this->app->model('purchase_order_item');
          $order = $orderObj->dump($id);
          if ($order) {
              $store = app::get('promotion')->model('stores')->getRow('store_name',array('store_id' => $order['store_id']));
              $order['store'] = $store ? $store['store_name'] : '';
              $supplier = $this->app->model('supplier')->getRow('name',array('id' => $order['supplier_id']));
              $order['supplier'] = $supplier ? $supplier['name'] : '';
  
              $items = $orderItemObj->getList('*',array('purchase_order_id'=>$id));
              foreach ($items as $k => $item) {
                  $items[$k]['tax_rate'] = empty($item['tax_rate'])?0:$item['tax_rate'];
                  $items[$k]['purchase_price_tax'] = $item['purchase_price'] / (1 + $item['tax_rate']/100);
  
              }
              $this->pagedata['order'] = $order;
              $this->pagedata['items'] = $items;
              $this->pagedata['user'] = $this->user_data['account']['login_name'];
          }
          $this->singlepage('admin/purchase/order/print.html');
      }
  ```

  ```html
  <form class="tableform">
      <div class="division">
          <table width="100%">
              <tr>
                  <th><{t}>单据编号：<{/t}></th>
                  <td>
                      <{$order.bn}>
                  </td>
                  <th><{t}>单据状态：<{/t}></th>
                  <td>
                      <{if $order.status == '0'}>
                      待入库
                      <{elseif $order.status == '1'}>
                      已入库
                      <{elseif $order.status == '2'}>
                      待审核
                      <{else}>
                      已作废
                      <{/if}>
                  </td>
                  <th><{t}>制单日期：<{/t}></th>
                  <td>
                      <{$order.create_time|cdate:FDATE_STIME}>
                  </td>
              </tr>
              <tr>
                  <th><{t}>门店：<{/t}></th>
                  <td>
                      <{$order.store}>
                  </td>
                  <th><{t}>送达日期：<{/t}></th>
                  <td>
                      <{$order.delivery_time|cdate:FDATE}>
                  </td>
                  <th><{t}>制单人：<{/t}></th>
                  <td>
                      <{$user}>
                  </td>
              </tr>
              <tr>
                  <th><{t}>供应商：<{/t}></th>
                  <td>
                      <{$order.supplier}>
                  </td>
                  <th><{t}>经营方式：<{/t}></th>
                  <td>
                      <{if $order.manage_way == '0'}>
                      经销
                      <{else}>
                      代销
                      <{/if}>
                  </td>
                  <th><{t}>结算方式：<{/t}></th>
                  <td>
                      <{if $order.settle_mode == '0'}>
                      先货后款
                      <{elseif $order.settle_mode == '1'}>
                      先款后货
                      <{else}>
                      按销结算
                      <{/if}>
                  </td>
              </tr>
          </table>
      </div>
      <div class="division">
          <table width="100%">
              <tr>
                  <th style="text-align: center"><{t}>商品编码<{/t}></th>
                  <th style="text-align: center"><{t}>商品名称<{/t}></th>
                  <th style="text-align: center"><{t}>单位<{/t}></th>
                  <th style="text-align: center"><{t}>采购数量<{/t}></th>
                  <th style="text-align: center"><{t}>含税进价<{/t}></th>
                  <th style="text-align: center"><{t}>进价税率<{/t}></th>
                  <th style="text-align: center"><{t}>不含税进价<{/t}></th>
                  <th style="text-align: center"><{t}>含税小计<{/t}></th>
                  <th style="text-align: center"><{t}>不含税小计<{/t}></th>
              </tr>
              <{foreach from=$items item=order_item key=key}>
              <tr>
                  <td style="text-align: center"><{$order_item.bn}></td>
                  <td style="text-align: center"><{$order_item.name}></td>
                  <td style="text-align: center"><{$order_item.unit}></td>
                  <td style="text-align: center"><{$order_item.nums}></td>
                  <td style="text-align: center"><{$order_item.purchase_price|cur}></td>
                  <td style="text-align: center"><{$order_item.tax_rate}>%</td>
                  <td style="text-align: center"><{$order_item.purchase_price_tax|cur}></td>
                  <td style="text-align: center"><{$order_item.nums * $order_item.purchase_price |cur}></td>
                  <td style="text-align: center"><{$order_item.nums * $order_item.purchase_price_tax |cur}></td>
              </tr>
              <{/foreach}>
          </table>
      </div>
  </form>
  <script>
      (function(){
          window.print()
      })();
  </script>
  ```

### 3.权限控制开发

- 审核权限

  `class cargoflow_ctl_admin_quick_instock extends desktop_controller`继承桌面控制器直接使用

  ```php
  //增加审核权限按钮显示
              if($this->has_permission('cargoflow_quick_instock_agree')){
                  $this->pagedata['agree'] = 0;
              }else{
                  $this->pagedata['agree'] = 1;
              }
  ```

  原生使用

  ```php
  $user = kernel::single('desktop_user');
          if($row['status'] == 2 && $user->has_permission('cargoflow_quick_instock_agree')){
              $name = '审核';
          }else{
              $name = '详情';
          }
  ```

- 角色权限

  父级角色——子集角色

  ```xml
    <permission id="cargoflow_purchase_order" display='true'>采购单管理</permission>
    <permission id="cargoflow_purchase_order_agree" display='true' parent='cargoflow_purchase_order'>采购订货审核</permission>
  
  ....
  
  <menugroup name="采购管理">
      <menu app='cargoflow' controller='admin_purchase_order' action='index' permission='cargoflow_purchase_order' display='true' order='420' >采购订货</menu>
       <menu app='cargoflow' controller='admin_purchase_order' action='agree' permission='cargoflow_purchase_order_agree' display='false' order='530' >采购订货审核</menu>
      <menu app='cargoflow' controller='admin_purchase_return' action='index' permission='cargoflow_purchase_order' display='true' order='420' >采购退货</menu>
  </menugroup>
  ```

  



## 4.列表操作

### 	1.组合列

#### 		a.明细弹窗

`app/cargoflow/lib/finder/purchase/order.php`

```php
<?php
/**
 * Created by PhpStorm.
 * User: wuchuanchuan
 * Date: 2019/8/29
 * Time: 15:11
 */
class cargoflow_finder_purchase_order{
    var $column_show = '操作';
    var $column_show_order = 20;
    function column_show($row){
        $return = '<a href="index.php?app=cargoflow&ctl=admin_purchase_order&act=show&_finder[finder_id]='.$_GET['_finder']['finder_id'].'&p[0]='.$row['id'].'" target="dialog::{title:\''.app::get('cargoflow')->_('采购单详情').'\',width:\'1000px\',height:\'600px\'}">'.app::get('cargoflow')->_('详细').'</a>';
        return $return;
    }

    var $column_amount = '不含税进价总额';
    var $column_amount_order = 50;
    function column_amount($row){
        $details = app::get('cargoflow')->model('purchase_order_item')->getList('purchase_price,nums,name,tax_rate',array('purchase_order_id'=> $row['id']));
        $amount = 0;
        foreach ($details as $detail) {
            $total = $detail['purchase_price'] * $detail['nums'];
            //进税计算
            if(empty($detail['tax_rate'])){
                $rate = 1;
            }else{
                $rate = 1 + ($detail['tax_rate']/100);
            }
            $amount += $total / $rate;
        }
        return '￥'.sprintf("%.2f", round($amount, 2));
    }

//    var $column_create_time = '制单时间';
//    var $column_create_time_order = 40;
//    function column_create_time($row){
//        kernel::log(print_r($row,true),'','debug.php');
//        return $row['create_time'];
//    }
}
```

#### 		b.tab分页标签

`app/cargoflow/lib/finder/purchase/order.php`

> 其中关于权限说明：超管跟门店用户管理员  见**登录用户信息**的**默认控制器**

```php
/**
     * 桌面订单相信汇总显示
     * @param null
     * @return null
     */
    public function _views(){
        $mdl_order = $this->app->model('purchase_order');

        if ($this->user_data['super']) {
            $sub_menu = array(
                0=>array('label'=>app::get('cargoflow')->_('全部'),'optional'=>false,'filter'=>array()),
                1=>array('label'=>app::get('cargoflow')->_('待审核'),'optional'=>false,'filter'=>array('status'=>'2')),
                2=>array('label'=>app::get('cargoflow')->_('待入库'),'optional'=>false,'filter'=>array('status'=>'0')),
                3=>array('label'=>app::get('cargoflow')->_('已入库'),'optional'=>false,'filter'=>array('status'=>'1')),
                4=>array('label'=>app::get('cargoflow')->_('已作废'),'optional'=>false,'filter'=>array('status'=>'3'))
            );
        } else {
            $sub_menu = array(
                0=>array('label'=>app::get('cargoflow')->_('全部'),'optional'=>false,'filter'=>array('store_id'=>$this->user_data['store_id'])),
                1=>array('label'=>app::get('cargoflow')->_('待审核'),'optional'=>false,'filter'=>array('store_id'=>$this->user_data['store_id'],'status'=>'2')),
                2=>array('label'=>app::get('cargoflow')->_('待入库'),'optional'=>false,'filter'=>array('store_id'=>$this->user_data['store_id'],'status'=>'0')),
                3=>array('label'=>app::get('cargoflow')->_('已入库'),'optional'=>false,'filter'=>array('store_id'=>$this->user_data['store_id'],'status'=>'1')),
                4=>array('label'=>app::get('cargoflow')->_('已作废'),'optional'=>false,'filter'=>array('store_id'=>$this->user_data['store_id'],'status'=>'3'))
            );
        }

        if(isset($_GET['optional_view'])) $sub_menu[$_GET['optional_view']]['optional'] = false;


        foreach($sub_menu as $k=>$v){
            if($v['optional']==false){
                $show_menu[$k] = $v;
                if(is_array($v['filter'])){
                    $v['filter'] = $v['filter'];
                }else{
                    $v['filter'] = array();
                }
                $show_menu[$k]['filter'] = $v['filter']?$v['filter']:null;
                if($k==$_GET['view']){
                    $show_menu[$k]['newcount'] = true;
                    $show_menu[$k]['addon'] = $mdl_order->count($v['filter']);
                }
                $show_menu[$k]['href'] = 'index.php?app=cargoflow&ctl=admin_purchase_order&act=index&view='.($k).(isset($_GET['optional_view'])?'&optional_view='.$_GET['optional_view'].'&view_from=dashboard':'');
            }elseif(($_GET['view_from']=='dashboard')&&$k==$_GET['view']){
                $show_menu[$k] = $v;
            }
        }

        return $show_menu;
    }
```

#### 		c.新增

`app/cargoflow/lib/finder/transfer/order.php`

```php
function index(){
//新增按钮
        $custom_actions[] = array(
            'label'=>app::get('cargoflow')->_('新增调拨单'),
            'icon'=>'add.gif',
            'href'=>'index.php?app=cargoflow&ctl=admin_transfer_order&act=add',
            'target'=>'dialog::{title:\''.app::get('cargoflow')->_('新增调拨单').'\'}'
        );
        $actions_base['title'] = app::get('cargoflow')->_('调拨单列表');
        $actions_base['actions'] = $custom_actions;
        $actions_base['orderBy'] = 'create_time DESC';
        $actions_base['use_buildin_recycle'] = false;
        $actions_base['use_buildin_filter'] = true;
        $actions_base['orderBy'] = 'create_time DESC';
        $this->finder('cargoflow_mdl_transfer_order',$actions_base);
    }
//新增页面
function add($id=null){
        $this->pagedata['obj_filter'] = urlencode('delivery=0&disabled=false');
        $this->pagedata['bn'] = 'DB' . date('YmdHis');
        $this->pagedata['create_time'] = date('Y-m-d H:i');
        $this->display('admin/transfer/order/add.html');
    }
//保存
function save(){
        $this->begin();
        $objOrder = $this->app->model('transfer_order');
        $objOrderItem = $this->app->model('transfer_order_item');
        $msg = app::get('cargoflow')->_('保存失败');
        $order = array();
        if($objOrder->validate($_POST, $order, $msg)){
            $items = $order['items'];
            unset($order['items']);
            if($objOrder->save($order)){
                $transfer_order_id = $objOrder->db->lastinsertid();
                foreach ($items as $item) {
                    $item['transfer_order_id'] = $transfer_order_id;
                    if (!$objOrderItem->save($item)) {
                        $this->end(false,app::get('cargoflow')->_('保存失败'));
                    }
                }

                // 出库单主表
                $objOutstockOrder = $this->app->model('outstock_order');
                $outstock_data = array(
                    'rel_id' => $transfer_order_id,
                    'store_id' => $order['from_store_id'],
                    'type' => '1',
                    'nums' => $order['nums']
                );

                // 出库单明细
                foreach ($items as $item) {

                    $outstock_item_data = array(
                        'goods_id' => $item['goods_id'],
                        'product_id' => $item['product_id'],
                        'bn' => $item['bn'],
                        'barcode' => $item['barcode'],
                        'name' => $item['name'],
                        'spec_info' => $item['spec_info'],
                        'unit' => $item['unit'],
                        'nums' => $item['nums'],
                        'sale_price' => $item['transfer_price'],
                    );
                    $outstock_data['products'][] = $outstock_item_data;
                }

                $outstock_save = $objOutstockOrder->add($outstock_data);

                if (!$outstock_save) {
                    $this->end(false,app::get('cargoflow')->_('保存失败'));
                }

                $this->end(true,app::get('cargoflow')->_('保存成功'));
            }else{
                $this->end(false,app::get('cargoflow')->_('保存失败'));
            }
        }else{
            $this->end(false,$msg);
        }
    }
```

- 视图

<details>


```html
<form method="POST" id='add_link' action="index.php?app=cargoflow&ctl=admin_transfer_order&act=save" class="tableform">
    <div class="division">
        <table width="100%">
            <tr>
                <th><{t}>单据编号：<{/t}></th>
                <td>
                    <{$bn}>
                </td>
                <th><{t}>制单日期：<{/t}></th>
                <td style="width: 260px">
                    <{$create_time}>
                </td>
            </tr>
            <tr>
                <th><{t}>发货门店：<{/t}></th>
                <td>
                    <{input type="object" object="stores@promotion" callback=update_price name="from_store_id" cols="name" multiple="false" select="radio" }>
                </td>
                <th><{t}>入库门店：<{/t}></th>
                <td>
                    <{input type="object" object="stores@promotion" callback=update_price name="store_id" cols="name" multiple="false" select="radio" }>
                </td>
            </tr>
            <tr>
                <th><{t}>备注：<{/t}></th>
                <td colspan="3">
                    <{input type="text" name="remark" value=""}>
                </td>
            </tr>
        </table>
    </div>
    <div class="division">
        <h4>商品选择：</h4>
        <{input type="object" object="products@b2c" multiple="true" textcol="name" callback=update_price obj_filter=$obj_filter name="product_ids" view="cargoflow:admin/transfer/order/product.html"}>
    </div>
    <div class="table-action">
        <{button type='button' label=$___a='取消'|t:'site' id="cancel-form-submit" }>
        <{button type='button' label=$___a='提交'|t:'site' id="agree-form-submit" }>
    </div>
</form>

<script>

    function update_price() {
        var from_store_id = $('add_link').getElement('input[name=from_store_id]').get('value');
        var to_store_id = $('add_link').getElement('input[name=store_id]').get('value');
        var products = $('add_link').getElements('.product-item');
        if (from_store_id && to_store_id) {
            products.forEach(function(product){
                var product_id = product.get('data-pro-id');
                new Request.JSON({
                    url:'index.php?app=cargoflow&ctl=admin_transfer_order&act=query',
                    method:'post',
                    data:'from_store_id='+from_store_id+'&to_store_id='+to_store_id+'&product_id='+product_id,
                    onComplete:function(res){
                        if (res.success != undefined) {
                            product.getElement('.from_store').set('text',res.from_stock)
                            product.getElement('.to_store').set('text',res.to_stock)
                        } else {
                            MessageBox.error(res.error);
                        }
                    }
                }).send();
            })
        }
    }

    (function(){
        var _form = $('add_link');
        var cancel_btn =$('cancel-form-submit');
        var agree_btn =$('agree-form-submit');
        var finder = finderGroup['<{$env.get._finder.finder_id}>'];

        _form.store('target',{
            onSuccess:function(response){
                var hash_res_obj = JSON.decode(response);

                if (hash_res_obj.success != undefined && hash_res_obj.success != "")
                {
                    try{
                        var _dialogIns = agree_btn.getParent('.dialog').retrieve('instance');
                    }catch(e){}

                    if(_dialogIns)
                    {
                        _dialogIns.close();
                        window.finderGroup['<{$env.get._finder.finder_id}>'].refresh();
                    }
                }
            }
        });

        agree_btn.addEvent('click',function(){
            _form.fireEvent('submit',{stop:$empty});
        });

        cancel_btn.addEvent('click',function(){
            try{
                var _dialogIns = cancel_btn.getParent('.dialog').retrieve('instance');
            }catch(e){}

            if(_dialogIns)
            {
                _dialogIns.close();
            }
        });

    })();
</script>
```

</details>



### 2.登录用户信息

- 业务处理控制器（`app/cargoflow/lib/finder/purchase/order.php`

```php
$desktop_user = new desktop_user();
$user_data = $desktop_user->user_data;
```

- 默认控制器（`app/cargoflow/controller/admin/purchase/order.php`

```php
class cargoflow_ctl_admin_purchase_order extends desktop_controller
{

    var $workground = 'cargoflow_ctl_admin_purchase_order';
    var $user_data;

    public function __construct($app)
    {
        parent::__construct($app);
        $desktop_user = new desktop_user();
        $user_data = $desktop_user->user_data;
        $member = app::get('promotion')->model('members')->getRow('member_id,store_id', array('username'=>$user_data['name']));
        $user_data['store_id'] = $member ? $member['store_id'] : 0;
        $user_data['operator_id'] = $member ? $member['member_id'] : 0;
        $this->user_data = $user_data;
    }
    
    .......
```

### 3.视图打印

```html
<{foreach from=$item item=value key=key}>
<{$key}> : <{$value}><br>
<{/foreach}>
```





## 5.控制器数据操作

```php
//id查找

$order_obj = $this->app->model('purchase_order');
$obj = $order_obj->dump($id);

//获取数据表
$goods = $this->app->model('goods');
dd($goods->_columns());
//获取列表（model类定义）
$importItems = app::get('cargoflow')->model('import_items')->getList('*',array('import_id'=>$importId));
```

## 6.打印

```php
/**
     * 导出明细
     */
    function exportDetail(){
        ini_set("memory_limit","80M");
        @include_once(app::get('b2c')->app_dir . "/lib/phpexcel/PHPExcel/IOFactory.php");
        $goods_model = app::get('cargoflow')->model('damage_item');
        if(isset($_POST['isSelectedAll']) && $_POST['isSelectedAll'] == '_ALL_'){
            $goodsData = $goods_model->getList('*',array());
        }else{
            $goodsData = $goods_model->db->select('select a.* from sdb_cargoflow_damage_item a left join sdb_cargoflow_damage b on a.damage_id=b.id where a.id in ('.implode(',',$_POST['id']).')');
        }
        $header_arr = array('A'=>'brand_name','B'=>'name','C'=>'unit','D'=>'price','E'=>'nums','F'=>'total','G'=>'create_time');
        //初始化PHPExcel()
        $objPHPExcel = new PHPExcel();
        $objWriter = new PHPExcel_Writer_Excel2007($objPHPExcel);
        $filename = 'outstock.xlsx';
        //接下来就是写数据到表格里面去
        $objActSheet = $objPHPExcel->getActiveSheet()->mergeCells('A1:G1');
        #$objActSheet = $objPHPExcel->getActiveSheet()->mergeCells('A2:G2');
        $objPHPExcel->getActiveSheet()->getColumnDimension('B')->setWidth(20);
        $objPHPExcel->getActiveSheet()->getColumnDimension('G')->setWidth(18);
        $objPHPExcel->getActiveSheet()->getStyle('A1')->getAlignment()->setHorizontal(\PHPExcel_Style_Alignment::HORIZONTAL_CENTER);
        $objActSheet->setCellValue('A1','出库单');
        #$objActSheet->setCellValue('A2','日期：'.date("Y").'年'.date("m").'月'.date("d").'日');
        $title = array('A'=>'品牌','B'=>'商品名称','C'=>'单位','D'=>'单价','E'=>'数量','F'=>'总计金额','G'=>'日期');
        foreach ($title as $ke=>$val){
            $objActSheet->setCellValue($ke.'2',$val);
        }
        $startRow = 3;
        $numTotal = 0;
        $amtTotal = 0;
        foreach ($goodsData as $k=>$row) {
            $row['total'] = $row['nums']*$row['price'];
            $numTotal += $row['nums'];
            $amtTotal += $row['total'];
            $brand = app::get('cargoflow')->model('quick_instock')->db->selectrow('select b.brand_name from sdb_b2c_products a left join sdb_b2c_brand b on a.brand_id=b.brand_id where a.product_id='.$row['product_id']);
            $row['brand_name'] = $brand['brand_name'];
            $damage =  app::get('cargoflow')->model('damage')->getRow('create_time',array('id'=>$row['damage_id']));
            $row['create_time'] = date('Y-m-d',$damage['create_time']);
            foreach ($header_arr as $key => $value){
                $objActSheet->setCellValue($key.$startRow,$row[$value]);
            }
            $startRow++;
        }
        $objPHPExcel->getActiveSheet()->mergeCells('A'.$startRow.':D'.$startRow);
        $objActSheet->setCellValue('A'.$startRow,'合计');
        $objActSheet->setCellValue('E'.$startRow,$numTotal);//数量合计
        $objActSheet->setCellValue('F'.$startRow,$amtTotal);//总计金额合计
        $startRow += 1;
        $objPHPExcel->getActiveSheet()->mergeCells('A'.$startRow.':G'.$startRow);
        $objActSheet->setCellValue('A'.$startRow,'保管人：                                领用人：');
        // 下载这个表格，在浏览器输出
        header("Pragma: public");
        header("Expires: 0");
        header("Cache-Control:must-revalidate, post-check=0, pre-check=0");
        header("Content-Type:application/force-download");
        header("Content-Type:application/vnd.ms-execl");
        header("Content-Type:application/octet-stream");
        header("Content-Type:application/download");;
        header('Content-Disposition:attachment;filename='.$filename.'');
        header("Content-Transfer-Encoding:binary");
        $objWriter->save('php://output');
        exit;
    }
```

```php
    /**
     * 导出明细
     */
    function exportDetail(){
        $orderItemObj = $this->app->model('purchase_order_item');
        $data = $orderItemObj->getList('barcode,name,unit,nums,purchase_price,tax_rate', array('purchase_order_id' => $_GET['id']));
        $header_arr = array('商品条码', '商品名称','单位', '采购数量' ,'含税进价' , '进价税率' ,  '不含税进价', '含税小计' ,'不含税小计');
        foreach ($data as $k => $v) {
            $data[$k]['tax_rate'] = empty($v['tax_rate']) ? 0 : $v['tax_rate']."%";
            $data[$k]['barcode'] = "\t".$v['barcode'];
            $data[$k]['purchase_price'] = "\t".sprintf("%.4f", round($v['purchase_price'], 4));
            $data[$k]['purchase_price_tax'] = "\t".sprintf("%.4f", round($v['purchase_price'] / (1 + $v['tax_rate'] / 100), 4));
            $data[$k]['total'] = "\t".sprintf("%.2f",round($v['nums'] * $v['purchase_price'], 2));
            $data[$k]['total_tax'] = "\t".sprintf("%.2f",round($v['nums'] * $data[$k]['purchase_price_tax'],2));
        }
        excelExport('purchase_detail',$header_arr, $data);
    }
```

