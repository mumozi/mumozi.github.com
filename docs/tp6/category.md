# 分类管理

## 表设计

```sql
CREATE TABLE `mall`.`Untitled`  (
  `id` int(10) UNSIGNED NOT NULL AUTO_INCREMENT,
  `name` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL DEFAULT '' COMMENT '分类名称',
  `pid` int(10) UNSIGNED NOT NULL DEFAULT 0 COMMENT '父类ID',
  `icon` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL DEFAULT '' COMMENT '图标',
  `path` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL DEFAULT '' COMMENT '路径',
  `create_time` int(10) UNSIGNED NOT NULL DEFAULT 0 COMMENT '创建时间',
  `update_time` int(10) UNSIGNED NOT NULL DEFAULT 0 COMMENT '更新时间',
  `operate_user` varchar(100) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL DEFAULT '' COMMENT '操作人',
  `status` tinyint(1) UNSIGNED NOT NULL DEFAULT 0 COMMENT '状态',
  `listorder` int(10) UNSIGNED NOT NULL DEFAULT 0 COMMENT '排序',
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;
```

## 页面 部署

- 页面改名放在view/category文件夹下
- admin应用下存放控制器Category
- admin应用下添加验证器
- 分类属于前后端调用-》业务放在公共目录的business下
- 数据库模型-》公共目录下（开启时间自动保存）

!>出现页面500错误检查view文件替换方法是否写错。检查控制器是否引用错误。

## 新增分类

- 控制器

```php
public function save() {
        $pid = input("param.pid", 0 ,"intval");
        $name = input("param.name", "", "trim");

        //参数校验
        $data = [
            'pid' => $pid,
            'name' => $name,
        ];

        $validate = new \app\admin\validate\Category();
        if (!$validate->check($data)) {
            return show(config("status.error"), $validate->getError());
        }

        //业务添加
        $category = new CategoryBus();
        try {
            $result = $category->add($data);
        }catch (\Exception $e){
            return show(config("status.error"), $e->getMessage());
        }

        return show(config("status.success"), "ok");
    }
```

- 验证

```php
class Category extends Validate
{
    protected $rule = [
        'pid' => 'require',
        'name' =>  'require',
    ];

    protected $message = [
        'pid' => '上级分类ID必须',
        'name' => '分类名称必须',
    ];
}
```

- 业务

```php
 public function add($data) {
        $data['status'] = config("status.mysql.table_normal");
        $this->categoryObj->save($data);
        return $this->categoryObj->getLastInsID();
    }s
```

## 新增优化

- 内部异常单独优化抛出
- 检查分类已存在情况
- js前端抛出用户异常信息

```php
 public function add($data) {
        $data['status'] = config("status.mysql.table_normal");
        //根据name去查询是否存在记录
        $categoryResult = $this->getCategoryByName($data['name']);
        if ($categoryResult) {
            throw new \think\Exception("该分类已经存在，请重新输入");
        }
        try {
            $this->categoryObj->save($data);
        }catch (\Exception $e){
            throw new \think\Exception("服务内部异常");
        }

        return $this->categoryObj->getLastInsID();
    }
```

```js
 $.ajax({
                type:"POST",
                data:data.field,
                url: "/admin/category/save",
                success(res)  {
                    //todo
                    if(res.status == 1){
                        layer.msg(res.data, function () {
                            window.location = '/admin/category/index';
                        });
                    }else{
                        layer.msg(res.data);
                        return false;
                    }
                }
            })
```

## 分类初始化获取

- 获取所有分类（默认条件状态，获取选定字段）
- 控制器输出参数
- js获取直接输出（注意raw不进行转义输出）

```php
public function add() {
        $category = new CategoryBus();
        $categorys = $category->getNormalCategorys();
        return View::fetch("",[
            "categorys" => json_encode($categorys),
        ]);
    }
```

```js
 let toTrees = toTree({$categorys | raw});
```

```php
 public function getNormalCategory($field = "*") {
        $where = [
            "status" => config("status.mysql.table_normal"),
        ];
        return $this->where($where)->field($field)->select();
    }
```

```php
public function getNormalCategorys(){
        $field = "id, name, pid";
        $categorys = $this->categoryObj->getNormalCategory($field);
        if(!$categorys){
            $categorys = [];
        }
        return $categorys->toArray();
    }
```

## 分页查询

- 获取分节点
- 注意状态（删除不要，开启关闭都要）
- 分页单独使用paginate

```php
    public function index() {
        $pid = input("param.pid", "", "intval");
        $data = [
            "pid" => $pid,
        ];
        $category = new CategoryBus();
        try {
            $categorys = $category->getLists($data, 5);
        }catch (\Exception $e){
            $categorys = [];
        }
        return View::fetch("",[
            "categorys" => $categorys,
        ]);
    }
```

```php
public function getLists($where, $num = 10){
        $order = [
            "listorder" => "desc",
            "id" => "desc"
        ];
        return $this->where("status", "<>", config("status.mysql.table_delete"))
            ->where($where)
            ->order($order)
            ->paginate($num);
    }
```

```html
 {volist name="categorys['data']" id="vo"}

{if $vo.status == 1}checked{else /}{/if}

...
{/volist}

```

```php
//补充插件（默认
public function getLists($data, $num){
        $list = $this->categoryObj->getLists($data, $num);
        if (!$list) {
            $list = [];
        }
        $res = $list->toArray();
       // $res['render'] = $list->render();//获取的默认分页插件
        return $res;
    }
```

### 结果集整合(打印categorys)

```php
 array:6 [▼
  "total" => 6
  "per_page" => 5
  "current_page" => 1
  "last_page" => 2
  "data" => array:5 [▼
    0 => array:10 [▶]
    1 => array:10 [▶]
    2 => array:10 [▶]
    3 => array:10 [▶]
    4 => array:10 [▶]
  ]
  "render" => "<ul class="pagination"><li class="disabled"><span>&laquo;</span></li> <li class="active"><span>1</span></li><li><a href="/admin/category?page=2">2</a></li> <li> ▶"
]
```

```js
laypage.render({ //分页
            elem: 'pages'
            , count: {$categorys.total}
            , theme: '#FFB800'
            , limit: {$categorys.per_page}
            , curr: {$categorys.current_page}
            , jump:function (obj, first) {
                if(!first){
                    location.href="?page="+obj.curr
                }
            }

        });
```

# 8-9