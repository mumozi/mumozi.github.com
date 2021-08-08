# 常用方法



```php
//获取url参数
request()->input('order_time')
//获取env配置
env('IMG_URL')
```



```shell
#命令
 php artisan migrate
 php artisan make:migration add_sdb_b2c_orders_table
 #填充数据
 php artisan db:seed --class=AdminTablesSeeder
 #生成迁移数据
 php artisan iseed my_table
```

