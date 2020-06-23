# 面试sql

## 分组查询

```sql
drop TABLE `student` ;
-- 创建表
drop table student;
create table student(
    id varchar(20),-- 编号
    class varchar(20),-- 年级
    score int-- 分数
);
 
-- 创建测试数据
delete from student;
insert student values('1','一年级',82);
insert student values('2','一年级',95);
insert student values('3','一年级',82);
insert student values('4','一年级',40);
insert student values('5','一年级',20);
insert student values('6','二年级',95);
insert student values('7','二年级',40);
insert student values('8','二年级',3);
insert student values('9','二年级',60);
insert student values('10','二年级',10);
insert student values('11','三年级',70);
insert student values('12','三年级',60);
insert student values('13','三年级',40);
insert student values('14','三年级',90);
insert student values('15','三年级',20);
 
查询每个班级的前三名并新增排名列
 
select id,class,score,`rank`
from 
( SELECT id,class,score, row_number() over (PARTITION BY class ORDER BY score desc) AS `rank` FROM student ) t 
WHERE
    t.rank <= 3 
order by t.`rank`;


select id,class,score,
(select count(*) from student t1 where t1.class=t2.class and t1.score > t2.score)+1 as rank from student t2
where rank<3 order by score desc

-- 
select a.sid,a.class,a.score from student as a
left join student as b
on a.class = b.class and a.score<b.score
group by a.class, a.sid
having count(b.class)<2
order by a.class,a.score desc;

-- 数学成绩的排名
-- 思想 统计另外一张表（自己）与目前的分数比统计次数，比自己大的次数为0代表自己分数最高
select id,name,score,
(select count(*) from stu t1 where sub ='数学' and t1.score > t2.score)+1 as 名次 from stu t2
where sub='数学' order by score desc

-- 各门课程成绩最好的两位学生


-- 执行报错
-- 问题出现的原因： 
-- MySQL 5.7.5及以上功能依赖检测功能。如果启用了ONLY_FULL_GROUP_BY SQL模式（默认情况下），MySQL将拒绝选择列表，HAVING条件或ORDER BY列表的查询引用在GROUP BY子句中既未命名的非集合列，也不在功能上依赖于它们。（5.7.5之前，MySQL没有检测到功能依赖关系，默认情况下不启用ONLY_FULL_GROUP_BY。有关5.7.5之前的行为的说明，请参见“MySQL 5.6参考手册”。）
-- ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION

-- 8.0
set @@global.sql_mode 
= 'STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION'
-- 5.75以上
set global sql_mode='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION';


set session sql_mode='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION';
```

## 订单查询

```sql
-- 使用row_number（）函数进行编号
select email,customerID, ROW_NUMBER() over(order by psd) as rows from QT_Customer;


-- 在订单中按价格的升序进行排序，并给每条记录进行排序
select DID,customerID,totalPrice,ROW_NUMBER() over(order by totalPrice) as rows from OP_Order


-- 统计出每一个各户的所有订单并按每一个客户下的订单的金额 升序排序，同时给每一个客户的订单进行编号。这样就知道每个客户下几单了
select ROW_NUMBER() over(partition by customerID  order by totalPrice)
 as rows,customerID,totalPrice, DID from OP_Order


 -- 统计每一个客户最近下的订单是第几次下的订单/

 with tabs as  
(  
select ROW_NUMBER() over(partition by customerID  order by totalPrice)
 as rows,customerID,totalPrice, DID from OP_Order  
 )  
select MAX(rows) as '下单次数',customerID from tabs 
group by customerID


-- 统计每一个客户所有的订单中购买的金额最小，而且并统计改订单中，客户是第几次购买的：

-- 思路：利用临时表来执行这一操作。

-- 1.先按客户进行分组，然后按客户的下单的时间进行排序，并进行编号。

-- 2.然后利用子查询查找出每一个客户购买时的最小价格。

-- 3.根据查找出每一个客户的最小价格来查找相应的记录。
 with tabs as  
     (  
    select ROW_NUMBER() over(partition by customerID  order by insDT) 
as rows,customerID,totalPrice, DID from OP_Order  
    )  
     select * from tabs  
    where totalPrice in   
    (  
    select MIN(totalPrice)from tabs group by customerID  
     ) 
    
```

