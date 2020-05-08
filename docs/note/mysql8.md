# Mysql8 特性

## 一、账户与安全

  1、强制分开用户创建和用户授权

  \#、prompt 字符：可以更改mysql命令提示符

## 二、认证插件更新

  1、之前版本是mysql_native_password，8.0版本是caching_sha2_password

​     mysql> show variables like '%default%';
​     +---------------------------------+-----------------------+
​     | Variable_name | Value |
​     +---------------------------------+-----------------------+
​     | default_authentication_plugin | caching_sha2_password |

  2、由于认证插件的更新，客户端需要升级才可以连接到8.0，或者服务器修改用户认证插件：

​     alter user [root@'%'](mailto:root@'%') identified with mysql_native_password by '123';

## 三、密码管理

  1、8.0版本开始限制重复使用以前的密码

​     password_history=3      --不允许和最近三次密码一样

​     password_reuse_interval=90 --不允许和最近90天内的密码一样

​     password_require_current=ON --修改密码需要提供当前密码

​     语句：

​        alter user [user@host](mailto:user@host) identified by 'new_password' replace 'cur_password';

  2、这三个变量可以全局设置或者利用alter user [user@host](mailto:user@host) identified by '123' password_history 针对用户设置

  3、历史密码都是保存在mysql.password_history

  \#、set persist var=value --对变量持久化修改

  原理：将persist设置的变量写入到数据目录下的配置文件中（json格式），服务器启动时也会读取该配置文件

## 四、角色管理

  1、8.0版本提供了角色管理功能，角色是一组权限的集合，即把一组权限放在一起并起一个名字，就成为了一个角色

  2、角色分配步骤

​     创建角色

​           create roll 'new_role'；   --创建了一个用户

​     给角色分配权限

​           grant insert,select on test.* to 'new_role';

​     给用户指定角色

​           grant [default] 'new_role' to 'user'@host; --不使用default的话，默认登录后需要用set role激活角色，使用default后就已经激活

​      指定用户

​           set role 'new_role';

  3、查看用户权限

​     show grant for 'user'@host using 'new_role';

  4、显式当前用户使用的角色

​     select current_role();

## 五、优化器索引

  1、隐藏索引（invisible index）

​        不会被优化器使用，但是仍然需要进行维护

​        create index index_name on tab(col) invisible;

​    \#、如果想设置优化器对隐藏索引可见，可以修改optimizer_switch中的use_invisible_index=on;（可以会话级别修改）

​     \#、修改索引可见性

​        alter table tab alter index index_name visible;

​     \#、主键不能设置为不可见

​     使用场景：

​         软删除   --删除一个索引，并不用真的删除，将其设置为隐藏索引即可

  2、降序索引

​     8.0版本中只有innodb的Btree支持降序索引

​     8.0版本中不再对group by操作进行隐式排序

  3、函数索引

​      支持在索引中使用函数（表达式）的值

​      支持降序索引，支持json数据的索引

​      函数索引基于虚拟列功能实现

  虚拟列建立：

​     alter table tab add c3 varchar(30) generated always as (upper(c2));

  创建：

​     create index index_name on tab( (upper(c2)) )

## 六、通用表表达式（CTE）

  1、即，with子句：

​     with cte_name as （select * from tab）

​      高级用法：

​          with etc1(col1) as(select co1 from tab1 ),

​              with etc2(col2) as(select co1*2 from etc1)

  2、递归cte

​     with recursive cte_name(n) as(

​       select 1

​       union all

​        select n+1 from cte where n<5

​       )

​      select * from ct;

  例：

​     mysql> with recursive etc(n) as( select 1 union select n*(n+1) from etc where n<=5) select * from etc;
​     +------+
​     | n |
​     +------+
​     | 1 |
​     | 2 |
​     | 6 |
​     +------+

   mysql> with recursive cte(id,name,path) as ( select id ,name ,cast(id as char(200)) from t where boss is null union all select t.id ,t.name ,concat(ep.id,',',t.id) from cte as ep join t on ep.id=t.boss ) select * from cte;
     +------+------+------+
     | id | name | path |
     +------+------+------+
     | 6 | f | 6 |
     | 3 | c | 6,3 |
     | 1 | a | 3,1 |
     | 4 | d | 1,4 |
     | 5 | e | 4,5 |
     | 2 | b | 5,2 |
     +------+------+------+

  3、递归限制

​     正常操作应该在cte中设置一个停止条件，否则将会陷入死循环，但是mysql为了避免死循环的发生，对递归深度有了限制，cte_max_recursion_depth、max_execution_time

  例1：阶乘

​     MySQL [test]> with recursive cte as( select 1 x,2 y union all select x*y,y+1 from cte where x<200 ) select * from cte;
​       +------+------+
​        |x | y |
​       +------+------+
​       | 1 | 2 |
​       | 2 | 3 |
​       | 6 | 4 |
​       | 24 | 5 |
​       | 120 | 6 |
​       | 720 | 7 |
​       +------+------+

  例2：斐波那契数列

​     MySQL [test]> with recursive cte as(select 1 x,1 y union all select y x,x+y y from cte where cte.x<20) select * from cte;
​     +------+------+
​     | x | y |
​     +------+------+
​     | 1 | 1 |
​     | 1 | 2 |
​     | 2 | 3 |
​     | 3 | 5 |
​     | 5 | 8 |
​     | 8 | 13 |
​     | 13 | 21 |
​     | 21 | 34 |
​     +------+------+

## 七、每门课程的第一名

​     MySQL [test]> select * from score a where (select distinct count(*) num from score b where a.course=b.course and a.id!=b.id and b.score>a.score) = 0;   ---0为第一，1为第二，2为第三

## 八、窗口函数

  1、聚合函数都可以用窗口函数改写，如：

​        mysql> select *,avg(score)over(partition by stuid) from score;

  2、专用窗口函数

​      ROW_NUMBER()/RANK()/DENSE_RANK()/PERCENT_RANK()   --获取排名

​      FIRST_VALUE()/LAST_VALUE/LEAD()/LAG()   

​      CUME_DIST()/NTH_VALUE()/NTILE()

  3、ROW_NUMBER():分组后内部编号，编号至于前后位置相关，与内容无关

  4、RANK():分组后内部编号，排序后编号，如果值一样则编号一样

  5、窗口定义

​     window_function(expr)

​      OVER(PARTITION BY ...

​            ORDER BY ...

​            frame_clause...)

​      CURRENT ROW :当前处理的行

​      M PRECEDING   :当前处理行第前M行

​      N FOLLOWING  :当前处理行第后N行

​      UNBOUNDED PRECEDING   :分组内部最前沿

​      UNBOUNDED FOLLOWING   :分组内最下沿

  6、高级定义（可以省去多次写窗口内容）

​      window_function1(expr)

​      OVER w as 'col1',

​      window_function2(expr)

​      OVER w as 'col2'

​      FROM table

​      WINDOW w AS(PARTITION BY col ORDER BY col ROW 1 PRECEDING AND 1 FOLLOWING);

## 九、集成数据字典

​      1、删除了之前版本的元数据文件，如opt、frm文件，对innodb只剩余了ibd文件

​     2、系统表（mysql）和数据字典全部改为innodb存储引擎

​      3、支持ddl原子性

​      4、简化了information_schema的实现，提高了访问性能

​      5、提供了序列化字典信息（SDI）的支持，以及ibd2sdi工具

​      6、innodb_read_only影响所有存储引擎，因为数据字典是innodb存储引擎的；对普通用户数据字典是不可见，无法查询和修改

​     7、自增列持久化，将自增列计数器的最大值写入redo log，同时在每次检查点将其写入引擎私有的系统表，会感知到每次对自增列中值的修改

​      8、死锁检查控制

​       mysql> show variables like '%innodb_deadlock%';
​         +------------------------+-------+
​         | Variable_name | Value |
​          +------------------------+-------+
​         | innodb_deadlock_detect | ON |
​         +------------------------+-------+

​        死锁检测会有性能消耗，在高并发场景下可以考虑关闭死锁检测，以提高系统性能

​      9、锁定语句新增选项（仅针对行锁起作用）

​        select ... for share/select for update [NOWAIT|SKIP LOCKED]

​        NOWAIT:如果请求的行被其它事务锁定，语句立即返回错误信息

​        SKIP LOCKED,从返回的结果集中移除被锁定的行，只返回未被锁定的行

​      10、支持部分快速DDL，ALTER TABLE ... ALGORITHM=INSTANT;

​      11、新增静态变量innodb_dedicated_server：可以自动配置innodb_buffer_pool_size/innodb_log_buffer_size等

​      12、默认创建两个undo表空间，不再使用共享表空间

## 十、json内联路径操作符

  1、column>>path = json_unquote(column->path)

## 十一、json聚合函数

  1、json_arrayagg(),用于生产json数组，将多行数据组合成json数组

  2、json_objectagg(),用于生成json对象，对于同一属性的对个值，选取最后一个值

## 十二、json使用函数

  1、json_pretty()：输出json内容时，进行格式化

  2、json_storage_size()：返回json数据占用的存储空间

## 十三、json合并函数

  1、json_merge_patch()：用于将两个json对象合并为一个对象，如果有相同节点，则第二个中的覆盖第一个json中的节点

  2、json_merge_preserv()：用于将两个json对象合并为一个对象，如果有相同节点，都会保留并将值合并为数组

## 十四、json表函数

  1、json_table()：将json数据转换为关系表