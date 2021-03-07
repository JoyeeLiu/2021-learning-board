" 垂死梦中惊坐起，发现blog还没更"

-- 20210307



##### TDSQL知识点

1.创建一个和表 pnr_1 结构一样的表

Create table pnr_2 as pnr_1 with no data  不含数据
Create table pnr_2 as pnr_1 with  data  含数据

2.NULLIF（exp1,exp2） 当字段exp1与exp2相等时返回 空；当两者不相等时返回 exp1 


-------常用在除数不能为0的限制下  RPK/NULLIF(ASK,0)

3.字段截取

Substr（exp,m,n） 从字段exp的第m位开始截取，截取长度为 n
例如：substr（‘20141023’,1,6）结果：‘201410’
4.NULL值的替换

COALESCE (expression_1, expression_2, ...,expression_n)
依次参考各参数表达式，遇到非null值即停止并返回该值。如果所有的表达式都是空值，最终将返回一个空值， 将ask为null的转换为0 

5.Index(str1 , str2)  返回 str2在str1中的起始位置
Index（str1 ， str2） > 0   表名在str1中包含str2
6.Decimal （n ,m） 有效长度为n 小数位占m
   Cast（12345.6789 as decimal(20,3)）===》 12345.678
7.cast类型转换

Teradata 四舍五入原则：Teradata中四舍五入的规则由DBSCONTROL下的参数 RoundHalfwayMagUp 决定，默认 RoundHalfwayMagUp = FALSE，此时按Teradata的规则进行进位，Teradata的规则考虑到5是个中间值，都进位则概率不平均，如果取决于前一位的奇偶，则进位的5和舍掉的5是平均的，根据这个原则，Teradata的四舍五入规则为：当5后面有1位不为0时，则进位，当5后面位都为0或者没有其他位时根据5前面一位的奇偶，奇进偶不进。
select cast('1212.705' as decimal(18,2))    --1212.70
select cast('1212.715' as decimal(18,2))     --1212.72
select cast('1212.7051' as decimal(18,2))      --1212.71
select cast('1212.7052' as decimal(18,2))     --1212.71
select cast('1212.70500' as decimal(18,2))    --1212.70
select cast('1212.705001' as decimal(18,2))    --1212.71

8.两个表进行union 时 对应的字段的 数据类型 要保持完全一致。
------字母与汉字不能进行union

------958与0.123进行union时 0.123会强制转换为0，所以要保证数据的类型一致
         Cast(958 as decimal(20,3))     ；  cast(0.123 as decimal(20,3))

9.替换    oreplace('tera-data', '-', '')  ===》     teradata

            oreplace('ABCD', 'C', 'P')       ===》       ABPD

10.MINUS  
      select  A,B,C   from table_1   where D>0
minus   sleect  A,B,C   from  table_1 where E=1
------ 在查询结果1中删除 包含有 查询结果2 中的数据   

11. 字符串连接：||


当前日期的上一年的第一个月的日期

Add_months( CAST(substr( flight_dt ,1,4) ||‘01’AS date format‘YYYYMM’), -12)

Add_months( cast( substr( ‘20141120’,1,4) || ‘01’as date format ‘YYYYMM’), -12)  ===》 201301

将小数转化为百分数

cast(0.1234*100 as decimal(20,2))||'%'   ===》  12.34%

12. 表重命名  

 rename  table  tablename   to  newtablename

13. HELP
Help database pmart   显示pmart库中的对象（表、视图、宏等）
14. between A and B  ------------在A与B之间且包括A和B

15. 强调字母大小写-----  CASESPECIFIC   /     NOT CASESPECIFIC

   Select * from  table_1  where  name(CASESPECIFIC)  like ‘%M%’

--------teradata 在缺省的状态下是不区分大小写的

16. LIKE    模糊查询 


 like '_T%'    ----T 在第二个位置,以任意字符结尾      ;  
 like  '_M_N_' ---- M在第二个位置,N在第四个位置
  %、_ 在 like 中作为通配符使用,但当查找 % 和 _ 本身时

   定义 ESCAPE 字符  在escape字符后的 %和 _ 作为一般的字符使用

like '%A%' ESCAPE 'A'   ----（第一个%为通配符，A%表示一般字符%） 表示以%结尾的字符串 
like  '_B_%' ESCAPE 'B'  ----第二个字符为 _ 的以任意结尾的字符串

17.  char(M)  固定长度字符串 共占M个字节  当字符串少于M个字节时以空格补充 ; varchar(M)  可变长度的字符串  最大长度为M

     Name  Char(6)    ----LJT   ;    
     name varchar(6) ----LJT.

18. 与日期有关的函数


EXTRACT    ---  select extract( year/month/day  from date+30)

ADD_MONTHS ----select add_months(date, -3)

--select add_months(cast(flight_dt as date format ‘YYYYMMDD’) ,9)

19. CAST  数据转换

 以大写显示结果  
select CAST(name as char(5) UPPERCASE)
select UPPER(name) ;
select name(UPPERCASE)

20. MACRO 宏


宏定义：

 Create MACRO macro_name

( icur_start_dt VARCHAR(10)

, icmp_start_dt VARCHAE(10) ) ---宏参数

 AS  (  ………  ) ;

改变宏的定义

REPLACE MACRO  macro_name (宏参数)  AS  ( ………)

宏执行：

EXEC  pmart.macro_mkt_dom_reg_detail('20140430','20140401')

宏删除：

 DROP MACRO   pmart.macro_mkt_dom_reg_detail

宏注释：

COMMENT ON MACRO pmart.macro_mkt_dom_reg_detail  IS '国际报表'


21. 子查询 


----子查询的结果是唯一的，不含重复值，相当于加上了 distinct

---- order by   不能用于子查询

22.   TITLE


 Select   pax_count  DECIMAL(20,6)  ( TITLE‘承运旅客人数’)



23. 计算varchar 型字符的实际长度

 

   Select * from table_1  where   CHARACTERS(first_name)>5
   Select * from tbale_1  where  CHARACTERS( TRIM(frist_name) )>5



24. trim用法

去掉字符数据前端和后端的字符
 select trim ('    abc    ')                          ===》  abc
 select  trim(both 'a' from 'aabacdea')      ===》bacde     去掉首尾指定字符
 select  trim(leading 'a' from 'aabacdea')   ===》bacdea    去掉前端指定字符
 select  trim(trailing 'a' from 'aabacdea')    ===》aabacde   去掉结尾指定字符

25. 当一个表与其自己进行连接 即自连接（self join）时  表一定要使用 别名

26. 建立表

CREATE VOLATILE MULTISET TABLE VT_RPT_OD_FORWARD_BOOKING
,FALLBACK   ----数据备份     在缺省时默认为 无数据备份有恢复日志
,NO BEFORE JOURNAL   -------无向前的恢复日志
,NO AFTER JOURNAL     ---------无向后的恢复日志
(
      Summ_Dt       DATE  FORMAT 'YYYYMMDD',
      Od_Seq                         INTEGER  NOT NULL WITH DEFAULT,
      Group_Ind     CHAR(1)         CHARACTER SET LATIN CASESPECIFIC,
      Od_Poo       CHAR(3)         CHARACTER SET UNICODE CASESPECIFIC,
      Od_Booked_Qty_Sum    DECIMAL(10,0)  DEFAULT ‘999’
)
PRIMARY INDEX  ( Od_Dpt_Airpt_Cd ,Od_Arrv_Airpt_Cd ,Od_Link_Airpt_Cd1 )
ON COMMIT PRESERVE ROWS;
 WITH DEFAULT---用字段的系统默认值代替null
DEFAULT ‘999’  ------当无数据时用 默认值999代替空
CHARACTER SET LATIN CASESPECIFIC  -----该字段大小写敏感
CHARACTER SET UNICODE CASESPECIFIC
VOLATILE---建立临时表 只存在于cache中，data dictionary中也找不到这个表，当User结束一个session时，这个表将自动删除，所有用户无法访问，下次用户需要重新建表

ON COMMIT PRESERVE ROWS ----使用ON COMMIT PRESERVE ROWS，允许会话中的其他查询使用这个可变临时表。缺省是ON COMMIT DELETE ROWS，意味着查询提交后，数据被删除。

创建可变临时表时，不允许使用的CREATE TABLE选项包括：
 永久日志(Permanent Journaling)
 参照完整性(Referential Integrity)
 检查约束(Check)
 列压缩
 列缺省值
 列标题
 命名的索引
MULTISET ------允许记录重复       SET------不允许记录重复
缺省时默认为 SET TABLE  当对SET TABLE 进行insert 时需要先检查表中是否有重复的数据 故会相当耗费资源 故建表时宜都用 MULTISET TABLE
PRIMARY INDEX  ---主索引
 选择原则：不同值尽量多、使用频繁（值访问及连接访问）、少更新
CREATE VOLATILE MULTISET TABLE vt_airport_city_cnty_region AS

(
   SELECT t1.airport_iata_cd,
          t1.city_cd,
          COALESCE(t2.city_name,'') AS city_name,
          COALESCE(t3.country_cd,'') AS country_cd,
          COALESCE(t3.country_name,'') AS country_name,
          COALESCE(t4.iata_region,'') AS iata_region
     FROM ${AUTO_PVIEWDB}.ref_airport_cd t1
    LEFT JOIN
          ${AUTO_PVIEWDB}.ref_city_cd t2
       ON t1.city_cd = t2.city_cd
     LEFT JOIN
          ${AUTO_PVIEWDB}.ref_country_cd t3
       ON t2.country_cd = t3.country_cd
     LEFT JOIN
          ${AUTO_PLGADB}.lga_iata_region t4
       ON t3.country_name = t4.country_name

) WITH DATA
PRIMARY INDEX (airport_iata_cd)
ON COMMIT PRESERVE ROWS;

27.  UNION  与 UNION ALL的用法


UNION         ---自动去除两个集合合并后重复的记录
UNION ALL   ---保留重复记录

28. 删除表

drop table  table_name  删除表中内容及其在数据字典中的定义
delect  table_name   /   delect  from  table_name  只删除表中数据
删除符合某条件的记录： delect  from  table_name   where  age<18 

29.表的修改

增加或删除字段：
alter table  table_name  add   flight_dt  varchar(10)
                                   ,add   summ_dt  varchar(10)
alter table  table_name  drop   flight_dt  varchar(10)
                                   ,drop  summ_dt  varchar(10)
 修改已有字段属性：
对没有FALLBACK 的表建立fallback 保护：
ALTER  TABLE  table_name  , FALLBACK
修改约束条件：表中已有的数据如果不符合修改后的约束条件则修改不能成功
添加约束：
 Alter  table  table_name  constraint  check (age>20 and age<30)
修改约束：
Alter  table  tbale_name  modify constraint  age_range(即约束名称) 
Check (age>20 and  age<30)
删除约束：
Alter  table  table_name  drop  constraint  age_range(即约束名称)

30.创建索引

主索引只能在创建表时建立，次索引既可在创建表时建立也可以用create语句建立.
Help  index  table_name   显示表table_name 上的所有索引
创建有名称的唯一次索引：
Create unique index  full_naem (last_name,first_name） on table_name
创建非唯一性次索引，且不用命名:
Create  index  (airline_cd)  on  table_name
删除次索引：只有次索引可以被删除，主索引不能被删除
Drop index full_name on table_name   利用次索引名删除次索引
Drop index （airline_cd） on table_name  利用次索引定义删除此索引

31.向表中插入数据

Insert into table_name (flight_dt,airline_cd,rpt,ask,rev)   values  (‘20141125’, ‘CA’, 0.57,  0.87, 9999 )
Insert int table_name  values  (‘20141125’, ‘CA’, 0.57,  0.87)
Insert into table_name_1(flight_dt,airline_cd)
                 select * from table_name_2
Insert into  table_name_1   select * from table_name_2

32.更新数据

Update  table_name  set    flight=’20141125’,airline_cd=’CA’    Where  sum_dt=’20141023’

33.数据删除

delete from  table_name  where  flight_dt=’20141125’

34.交易完整性

Teradata系统保持交易的完整，在缺省模式下，以分号结束的每个SQL语句都是一个完整的交易；也可以用BT和ET 显示的定义一个交易。
Select * from table_name_1  ;
Select * from table_name_2  ;
表示两个交易，一个失败不影响另一个的执行
      BT ;
Select * from table_name_1  ;
Select * from table_name_2  ;
      ET ;
表示一个交易，任何一个SQL语句失败，整个交易都会失败，系统将主动进行恢复处理。


35. group by 与where 同时使用时

group by 只对符合where限制的记录进行聚合。（在聚合之前，where已将不符合限制条件的记录删除）
在对聚合后的结果进行限制时使用 having （having是对聚合后的结果进行筛选）
Select avg（pax_nb）as pax_1 from  table_name  having  pax_1>999


36.集合操作    （在子查询中不能使用集合操作）


Union -------保持字段数目相同且对应字段的域兼容  .     Union（自动去除重复的记录）   union all（保留重复记录）
Intersect   -----相交操作
Except  、 minus  ----排它操作


37.视图    ----- 视图定义中不能使用 order  by

Create  view  view_name  as  select * from table_name
Replace  view  view_name(flight_dt,rpk)  as  Select  flight_dt , rpk  from table_name group by 1
在关系数据库中，并不是所有的视图都是可更新的，因为有些视图的更新不能唯一地有意义地转换成对相应基本表的更新。
（1）       若视图是由两个以上基本表导出的，则此视图不允许更新。
（2）       若视图的字段来自字段表达式或常数，则不允许对此视图执行INSERT和UPDATE操作，但允许执行DELETE操作。
（3）       若视图的字段来自集函数，则此视图不允许更新。
（4）       若视图定义中含有GROUP BY子句，则此视图不允许更新。
（5）       若视图定义中含有DISTINCT短语，则此视图不允许更新。

（6）       若视图定义中有嵌套查询，并且内层查询的FROM子句中涉及的表也是导出该视图的基本表，则此视图不允许更新。


38. 系统日历


系统日历基础表 Sys_calendar.Caldates 
当前日期字段： current_date      (select current_date -----2014/11/27)
包含内容：
calendar_date     DATE UNIQUE    (标准Teradata日期)
day_of_week     BYTEINT,   (1-7，星期几，1代表星期天)
day_of_month     BYTEINT, (1-31，本月中的第几号)
day_of_year      SMALLINT, (1-366，本年中的第几天)
weekday_of_month BYTEINT, (本月中该星期几出现的次数)
week_of_month BYTEINT, (本月中第几周，以星期天到星期六为一周。0，表
示月的第一个不完整的周；1表示月的第一个完整的周)
week_of_year BYTEINT, (0-53) (本年中第几周，0表示第一个不完整的周)
month_of_quarter BYTEINT, (1-3，本季度中第几月)
month_of_year BYTEINT, (1-12，本年中第几月)
month_of_calendar INTEGER, (1-n，本日历中第几月，从1900年1月起)
quarter_of_year BYTEINT, (1-4，本年中第几季度)
quarter_of_calendar INTEGER, (本日历中第几季度，从1900年1月起)
year_of_calendar SMALLINT, (年份，从1900起)
39.  累计统计   csum( flight_number ,flight_dt)
以 flight_dt 对 flight_number 进行累计
在不同的航线上 按 flight_dt 对flight_number 进行累计 ,即在不同航线上进行重新累计


Select csum( flight_number ,flight_dt) from table_name group by  flight_class_name_cn

40.排序函数

rank(flight_profit)  
   按flight_profit 的降序进行排序，最高的flight_profit 的序号为1 在不同的航空公司上进行排序：group by 限制排序的组别
  Select   airline_cd ,  route_cd ,rank(flight_profit)  from  table_name where  flight_dt=’20141127’  group by  airline_cd    
 为不同的航空公司统计其航线收入排序
 对rank后的结果进行条件限制： qualify
Quality 是对列表取前几列，不看具体的值     Quality>10 是取列表中的前10个，不是取排名的前10
按降序排 rank（flight_profit ASC）  此时Quality>10 就是取列表中的前10行（即flight_profit最低的前10个）
Select  rank(flight_profit)   as  rank_1    from table_name  
             where  flight_dt=’20141127’  group by  airline_cd
             qualify  rank_1>10
选择排名在前10的数据



41.分位数 （按照order_list 进行升序排列，每条记录处于位置分位数）

Quantile （quantile_constant , order_list）
quantile_constant  ---à定义分位数大小的常量 如100—百分位数 ，4---四分位数
order_list   ---à用于分割和排序的列
Quantile （quantile_constant , order_list  ASC） 按照order_list的降序排列，最大的在最前面，分位数最小(为0)
 Quantile （quantile_constant , order_list_1 ,order_list_2）当 两条记录的order_list1值一样时，按照order_list2的值进行升序排序，然后确定相应的分位数

42.数据采样 

Select * from table_name  sample  1000   -------采样1000条数据
Select * from table_name  sample  0.25   -------采样25%的数据

43.数据导入库

把外部数据文件保存成CSV格式的excel文件，把CSV文件再保存为txt文本文件
在库里建立待导入数据的新表
File --->  import data
Insert into  new_table_name (‘字段1’，‘字段2’，’字段3’)  values(?,?,?)


44.查询表中的重复数据

Select flight_dt, airline_cd,rpk,ask from table_name where flight_dt=’20141128’  group by 1,2,3,4 having  count(*)>1

45.可变临时表

可以使用 HELP VOLATILE TABLE 命令获得存在于会话中的所有可变临时表的信息。（注意：HELP DATABASE命令不会显示可变临时表，因为数据字典没有记录可变临时表。）
可变临时表不能：使用存取日志、改名、使用 Multiload 或Fastload实用程序装载


46.非唯一次索引 UNSI

 非唯一次索引(NUSI)是Teradata的一种索引，非主索引，索引的列值允许不唯一。典型地，在WHERE子句中使用索引的列，将提高查询性能。创建非唯一次索引，可以使用CREATE TABLE语法与表一起创建，也可以使用CREATE INDEX语法在建表后创建。如果索引不再需要了，可以使用DROP INDEX删除索引。
       创建了非唯一次索引，每个AMP上都建立了一个子表。子表中存储了一些记录，包含每个索引值和基础表记录的记录号(row-id)，子表中记录按照索引值的哈稀值排序存储。这样，按照索引值来查找记录非常方便，但是进行范围搜索，索引就没有用了。例如，使用上面的索引，查询工作代码为122100的雇员，索引起作用；查询工作代码在122000和123000之间的雇员，索引不起作用。

创建非唯一次索引：
(1)  在建表语句后面 直接加上  index（字段1名，字段2名….）
(2)  在表已经存在的情况下创建非唯一次索引
  Create  index （字段名） on  table­­_name


删除某表的非唯一次索引
    Drop index（字段名）  on   table_name
--------按值排序的非唯一次索引

按值排序的非唯一次索引(Value Ordered NUSI)的索引子表按数据值存储记录，而不是哈稀值。在按照范围查询时，这种索引非常有用。
(1)  在建表语句后  直接加   index（字段名） order by values（字段名）
(2)  表已存在情况下
Create  index（字段名） order by values（字段名） on  table_name
按值排序的非唯一次索引的列必须是：


! 单一的列
! 属于索引定义中的列
! 数字列 – 不允许非数字列
! 长度不能大于4个字节 – INT, SMALLINT, BYTEINT, DATE, DEC是有效的。
注：虽然允许DECIMAL数据类型，但长度不能超过4个字节，不能有小数。

--------连接索引
连接索引是一种能够提高某些类型查询的性能的索引技术，可以包含一个或多个表中的列。连接索引被创建后，由优化器决定是否使用，用户不能直接访问。


连接索引的目的，是从索引子表提供数据，避免访问基础表。


CREATE JOIN INDEX   cust_ord_ix   AS
SELECT (c.cust_id, cust_name) , (order_id, order_status, order_date)
FROM customer c INNER JOIN orders o
ON c.cust_id = o.cust_id
PRIMARY INDEX (cust_id);    ---------为连接索引赋予的主索引（缺省时默认第一列为主索引）
连接索引包括两部分：固定部分(第一个括号内) 和可重复部分 (第二个括号内)。


47.外部数据加载 （数据量不大，字段较少）

（1）将外部的CSV类型的数据文件保存为文本文件 txt
------确保 Teradata assistant 可识别的分隔符为 逗号
 Tools---Options---Export/Import Data ----选择逗号
（2）先建立待导入数据的空表----Import Data -----加载语句

Insert  into  ptest.corp_name (sort_num,corp_name)  values (?,?) ;

48. 分组排序partition的用法

partition by  order  by rank() over (partition by calss order by age )   
  (按照分组字段对记录进行排序)   先按照班级分组，然后在每个班级中按照age排序

row_number() over (partition by class order by age )
   (按照分组字段对记录进行排序)  先按照班级分组，然后在每个班级中按照age排序
 sum() over (partition by calss order by score )
   (按照分组字段对记录进行聚合)   先按照班级分组，然后对分数求sum



##### 参考

https://blog.csdn.net/u010020099/article/details/82219967

