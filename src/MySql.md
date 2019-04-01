# MySql

## 一、对数据库的操作

### 1.创建数据库：

create  database 库名;

create  database 库名  character set 编码;

### 2.删除数据库

drop database 数据库名;

### 3.使用库

use 库名；

![](http://ww1.sinaimg.cn/large/005WjvZYly1g1g7tbs9xyj308p01dgle.jpg)

### 4.查看当前正在使用的库

![](http://ww1.sinaimg.cn/large/005WjvZYly1g1g7v0utt6j30aq03wglh.jpg)

## 二、对数据库表的操作

### 1.创建一张表

create table 表名（

​         字段名  类型(长度) [约束]，

​         字段名  类型(长度) [约束]，

​         字段名  类型(长度) [约束]

）；

![](http://ww1.sinaimg.cn/large/005WjvZYly1g1g7w91xu6j30e005gmxb.jpg)

### 2.查看数据库表

创建完成后，我们可以查看数据库表

show tables;

![](http://ww1.sinaimg.cn/large/005WjvZYly1g1g7wzzlg6j309v06iaa1.jpg)

查看表的结构

Desc 表名;

![](http://ww1.sinaimg.cn/large/005WjvZYly1g1g7xjeuzsj30jb05pdg0.jpg)

### 3.删除一张表

drop table 表名；

![](http://ww1.sinaimg.cn/large/005WjvZYly1g1g7ykbvv7j30ba01pq2r.jpg)

### 4.修改表

#### 4.1 添加一列

alter table 表名 add 字段名  类型(长度)  [约束]

![](http://ww1.sinaimg.cn/large/005WjvZYly1g1gbjljkqpj30ji06pdg3.jpg)

#### 4.2修改列的类型(长度、约束)

alter table 表名 modify  要修改的字段名  类型(长度)    [约束]

![](http://ww1.sinaimg.cn/large/005WjvZYly1g1gblkhmgoj30k808oaaj.jpg)



#### 4.3修改列的列名

alter  table 表名 change 旧列名  新列名  类型(长度)    [约束]

![](http://ww1.sinaimg.cn/large/005WjvZYly1g1gbn63f03j30lq089gm3.jpg)



#### 4.4删除表的列

alter table 表名 drop 列名

![](http://ww1.sinaimg.cn/large/005WjvZYly1g1gbogzfvoj30ki07z3yw.jpg)



#### 4.5修改表名

rename table 表名 to 新表名

![](http://ww1.sinaimg.cn/large/005WjvZYly1g1gbpg7q8kj30d507lq30.jpg)



#### 4.6修改表的字符集

alter table 表名 character set 编码

![](http://ww1.sinaimg.cn/large/005WjvZYly1g1gbqggmp0j30g202bjra.jpg)



查看当前表的编码

show create  table tb_user；

![](http://ww1.sinaimg.cn/large/005WjvZYly1g1gbrtlaxij30i50dijsa.jpg)



### 三、对数据库表记录进行操作(修改)

#### 1.插入记录

insert into 表名(列名1，列名2.列名3...) values(值1，值2，值3....)；



insert into 表名 values(值1，值2，值3....);

![](http://ww1.sinaimg.cn/large/005WjvZYly1g1g84snvjaj30k805gglq.jpg)



##### 1.1插入中文解决乱码问题解决方法

方式一【不建议】

直接修改数据库的安装目录中的my.ini文件

方式二

set names gbk；



#### 2.修改表记录

##### 2.1不带条件的

update 表名 set 字段名=值,字段名=值,字段名=值,

它会将列的所有记录都更改

##### 2.2带条件的

update 表名 set 字段名=值 where 条件；

![](http://ww1.sinaimg.cn/large/005WjvZYly1g1gbuoq4v9j30h50953z2.jpg)



#### 3.删除记录

##### 3.1带条件的

delete from 表名 where 

![](http://ww1.sinaimg.cn/large/005WjvZYly1g1gbz4nj3sj30ff0esq3p.jpg)

**注意，删除后uid不会重置**



##### 3.2不带条件的

delete from tb_user;

![](http://ww1.sinaimg.cn/large/005WjvZYly1g1gc12r5lpj317q082dgs.jpg)



##### 3.3面试题

**说说delete与truncate的区别？**

delete删除的时候是一条一条地删除记录，它配合事务，可以将删除的数据找回。

Truncate删除，它是将整个表摧毁，然后再创建一张一模一样的表，它删除的数据无法找回。

演示：



**注意，delete删除uid不会重置，truncate删除uid会重置（因为删除了表结构，然后再创建一个一模一样的表，所以再插入时uid会重置）**



#### 4.查询操作

select [distinct]  * 列名，列名 from 表名  [ where 条件];

##### 4.1简单查询

select * from product；

![](http://ww1.sinaimg.cn/large/005WjvZYly1g1gc1zj1rkj316q057js2.jpg)

##### 4.2.查询商品和商品价格

select pname，price from product；

![1551924246507](C:\Users\ADMINI~1\AppData\Local\Temp\1551924246507.png)

##### 4.3查询所有商品信息使用表别名

select * from product as(可以省略) p；

![1551924308596](C:\Users\ADMINI~1\AppData\Local\Temp\1551924308596.png)

##### 4.4查询商品名，使用列别名

select pname as name from product；

![1551924405764](C:\Users\ADMINI~1\AppData\Local\Temp\1551924405764.png)

##### 4.5去掉重复值（按照价格）

select distinct(price)  from product;

![1551924629059](C:\Users\ADMINI~1\AppData\Local\Temp\1551924629059.png)

![1551924615073](C:\Users\ADMINI~1\AppData\Local\Temp\1551924615073.png)



##### 4.6将所有商品价格+10进行显示

select pname,price+10 from product;

![1551926354922](C:\Users\ADMINI~1\AppData\Local\Temp\1551926354922.png)



#### 4.2条件查询

##### 1.查询商品名为“杰士邦”的商品信息

![1551926661126](C:\Users\ADMINI~1\AppData\Local\Temp\1551926661126.png)

##### 2.查询价格>0.01的所有商品信息

select * from product where price>0.01;

![1551926739912](C:\Users\ADMINI~1\AppData\Local\Temp\1551926739912.png)

##### 3.查询所有商品含有“欧”字的商品信息

select * from product where pname like ‘%欧%';

![1551926808997](C:\Users\ADMINI~1\AppData\Local\Temp\1551926808997.png)



##### 4.查询商品id在(3,6,9)范围内的所有商品信息

select * from product where pid in(3,6,9);

![1551926995163](C:\Users\ADMINI~1\AppData\Local\Temp\1551926995163.png)



##### 5.查询所有商品含有“欧”字并且id为2的商品信息

select * from product where pname like '%欧%' and pid=2;

![1551927086624](C:\Users\ADMINI~1\AppData\Local\Temp\1551927086624.png)



##### 6.查询所有商品含有“欧”字或者id为6

![1551927151841](C:\Users\ADMINI~1\AppData\Local\Temp\1551927151841.png)



#### 4.3排序

##### 1.查询所有的商品，按价格进行排序(升序(asc)，降序(desc))

降序：

![1551927385758](C:\Users\ADMINI~1\AppData\Local\Temp\1551927385758.png)



#### 4.4聚合函数

##### 1.获得所有商品的价格总和

select sum(price) from product;

![1551927507112](C:\Users\ADMINI~1\AppData\Local\Temp\1551927507112.png)

##### 2.获得所有商品的平均价格

select avg(price) from product；

![1551927535517](C:\Users\ADMINI~1\AppData\Local\Temp\1551927535517.png)

##### 3.获得所有商品个数

select  count(*) from product;

![1551927653239](C:\Users\ADMINI~1\AppData\Local\Temp\1551927653239.png)



#### 4.5分组操作

##### 1.添加分类id

(alter table product add cid varchar(32);)



##### 2.初始化数据

update product set cid='1';

update product set cid='2' where pid in(5,6,7);

![1551927933123](C:\Users\ADMINI~1\AppData\Local\Temp\1551927933123.png)

##### 1.根据cid字段分组，分组后统计商品的个数

![1551928077047](C:\Users\ADMINI~1\AppData\Local\Temp\1551928077047.png)



##### 2.根据cid字段分组，分组统计每组商品的平均价格，并且平均价格大于10元

![1551928249457](C:\Users\ADMINI~1\AppData\Local\Temp\1551928249457.png)



#### 4.6查询总结

select  

from

where

group by 

having(分组后带有条件只能使用having)

order by (它必须放到最后)



## 四、对数据库进行查询

### 1.多表查询

1.1交叉连接查询(基本不会使用-得到的是两个表的乘积)

语法：select * from A,B；

1.2内连接查询(使用的关键字  inner join   --inner可以省略)

隐式内连接：select * from A,B where 条件；

显示内连接：select * from A inner join B on 条件；

1.3.外连接查询(使用的关键字  outer join   --outer可以省略)

左外连接：select * from A left outer join B on 条件；

右外连接：select * from A right outer join B on 条件;



## 五、案例:网上书店系统综合查询

- 网上书店系统表

![](http://ww1.sinaimg.cn/large/005WjvZYly1g1g8ealql7j309106b3yg.jpg)

- 查询会员表中会员编号、会员名称及电话号码,要求列名以汉字标题显示.

![](http://ww1.sinaimg.cn/large/005WjvZYly1g1g8abexm0j30lo0720t2.jpg)



- 查询价格最高的图书信息

  ![](http://ww1.sinaimg.cn/large/005WjvZYly1g1gbe5wahlj316d054t9c.jpg)

  ![](http://ww1.sinaimg.cn/large/005WjvZYly1g1gbcx0vo6j30pb043aa3.jpg)





## 六、Mysql视图

- ### 什么是视图?视图干什么用?

  视图(View)是一种虚拟存在的表,是一个逻辑表,本身并不包含数据.作为一个select语句保存在数据字典中的。

  通过视图,可以展现基表的部分数据;视图数据来自定义视图的查询中使用的表,使用视图动态生成。

- ### 为什么使用视图?

  A：因为视图的诸多优点，如下

  　　1）简单：使用视图的用户完全不需要关心后面对应的表的结构、关联条件和筛选条件，对用户来说已经是过滤好的复合条件的结果集。

  　　2）安全：使用视图的用户只能访问他们被允许查询的结果集，对表的权限管理并不能限制到某个行某个列，但是通过视图就可以简单的实现。

  　　3）数据独立：一旦视图的结构确定了，可以屏蔽表结构变化对用户的影响，源表增加列对视图没有影响；源表修改列名，则可以通过修改视图来解决，不会造成对访问者的影响。

  总而言之，使用视图的大部分情况是为了保障数据安全性，提高查询效率

  

### 一、创建视图

```java
CREATE [OR REPLACE] [ALGORITHM = {UNDEFINED | MERGE | TEMPTABLE}]
    VIEW view_name [(column_list)]
    AS select_statement
   [WITH [CASCADED | LOCAL] CHECK OPTION]
```

1）OR REPLACE：表示替换已有视图

2）ALGORITHM：表示视图选择算法，默认算法是UNDEFINED(未定义的)：MySQL自动选择要使用的算法 ；merge合并；temptable临时表

3）select_statement：表示select语句

4）[WITH [CASCADED | LOCAL] CHECK OPTION]：表示视图在更新时保证在视图的权限范围之内

　　cascade是默认值，表示更新视图的时候，要满足视图和表的相关条件

　　local表示更新视图的时候，要满足该视图定义的一个条件即可

TIPS：推荐使用WHIT [CASCADED|LOCAL] CHECK OPTION选项，可以保证数据的安全性 

**基本格式：**

　　create view <视图名称>[(column_list)]

​       as select语句

​       with check option;

#### 1、在单表上创建视图

```
mysql> create view v_F_players(编号,名字,性别,电话)
    -> as
    -> select PLAYERNO,NAME,SEX,PHONENO from PLAYERS
    -> where SEX='F'
    -> with check option;
Query OK, 0 rows affected (0.00 sec)

mysql> desc v_F_players;
+--------+----------+------+-----+---------+-------+
| Field  | Type     | Null | Key | Default | Extra |
+--------+----------+------+-----+---------+-------+
| 编号    | int(11)  | NO   |     | NULL    |       |
| 名字    | char(15) | NO   |     | NULL    |       |
| 性别    | char(1)  | NO   |     | NULL    |       |
| 电话    | char(13) | YES  |     | NULL    |       |
+--------+----------+------+-----+---------+-------+
4 rows in set (0.00 sec)

mysql> select * from  v_F_players;
+--------+-----------+--------+------------+
| 编号    | 名字      | 性别    | 电话        |
+--------+-----------+--------+------------+
|      8 | Newcastle | F      | 070-458458 |
|     27 | Collins   | F      | 079-234857 |
|     28 | Collins   | F      | 010-659599 |
|    104 | Moorman   | F      | 079-987571 |
|    112 | Bailey    | F      | 010-548745 |
+--------+-----------+--------+------------+
5 rows in set (0.02 sec)
```

#### 2、在多表上创建视图

```
mysql> create view v_match
    -> as 
    -> select a.PLAYERNO,a.NAME,MATCHNO,WON,LOST,c.TEAMNO,c.DIVISION
    -> from 
    -> PLAYERS a,MATCHES b,TEAMS c
    -> where a.PLAYERNO=b.PLAYERNO and b.TEAMNO=c.TEAMNO;
Query OK, 0 rows affected (0.03 sec)

mysql> select * from v_match;
+----------+-----------+---------+-----+------+--------+----------+
| PLAYERNO | NAME      | MATCHNO | WON | LOST | TEAMNO | DIVISION |
+----------+-----------+---------+-----+------+--------+----------+
|        6 | Parmenter |       1 |   3 |    1 |      1 | first    |
|       44 | Baker     |       4 |   3 |    2 |      1 | first    |
|       83 | Hope      |       5 |   0 |    3 |      1 | first    |
|      112 | Bailey    |      12 |   1 |    3 |      2 | second   |
|        8 | Newcastle |      13 |   0 |    3 |      2 | second   |
+----------+-----------+---------+-----+------+--------+----------+
5 rows in set (0.04 sec)
```

视图将我们不需要的数据过滤掉，将相关的列名用我们自定义的列名替换。视图作为一个访问接口，不管基表的表结构和表名有多复杂。

 　　如果创建视图时不明确指定视图的列名，那么列名就和定义视图的select子句中的列名完全相同；

　　如果显式的指定视图的列名就按照指定的列名。

注意：显示指定视图列名，要求视图名后面的列的数量必须匹配select子句中的列的数量。

### 二、查看视图

#### 1、使用show create view语句查看视图信息

```java
mysql> show create view v_F_players\G;
*************************** 1. row ***************************
                View: v_F_players
         Create View: CREATE ALGORITHM=UNDEFINED DEFINER=`root`@`localhost` SQL SECURITY DEFINER VIEW `v_F_players` AS select `PLAYERS`.`PLAYERNO` AS `编号`,`PLAYERS`.`NAME` AS `名字`,`PLAYERS`.`SEX` AS `性别`,`PLAYERS`.`PHONENO` AS `电话` from `PLAYERS` where (`PLAYERS`.`SEX` = 'F') WITH CASCADED CHECK OPTION
character_set_client: utf8
collation_connection: utf8_general_ci
1 row in set (0.00 sec)
```

#### 2、视图一旦创建完毕，就可以像一个普通表那样使用，视图主要用来查询

mysql> select * from view_name;

#### 3、有关视图的信息记录在information_schema数据库中的views表中

```
mysql> select * from information_schema.views 
    -> where TABLE_NAME='v_F_players'\G;
*************************** 1. row ***************************
       TABLE_CATALOG: def
        TABLE_SCHEMA: TENNIS
          TABLE_NAME: v_F_players
     VIEW_DEFINITION: select `TENNIS`.`PLAYERS`.`PLAYERNO` AS `编号`,`TENNIS`.`PLAYERS`.`NAME` AS `名字`,`TENNIS`.`PLAYERS`.`SEX` AS `性别`,`TENNIS`.`PLAYERS`.`PHONENO` AS `电话` from `TENNIS`.`PLAYERS` where (`TENNIS`.`PLAYERS`.`SEX` = 'F')
        CHECK_OPTION: CASCADED
        IS_UPDATABLE: YES
             DEFINER: root@localhost
       SECURITY_TYPE: DEFINER
CHARACTER_SET_CLIENT: utf8
COLLATION_CONNECTION: utf8_general_ci
1 row in set (0.00 sec)        
```

### 三、视图的更改

#### 1、CREATE OR REPLACE VIEW语句修改视图

基本格式：

　　create or replace view view_name as select语句;

在视图存在的情况下可对视图进行修改，视图不在的情况下可创建视图

#### 2、ALTER语句修改视图

```
ALTER
    [ALGORITHM = {UNDEFINED | MERGE | TEMPTABLE}]
    [DEFINER = { user | CURRENT_USER }]
    [SQL SECURITY { DEFINER | INVOKER }]
VIEW view_name [(column_list)]
AS select_statement
    [WITH [CASCADED | LOCAL] CHECK OPTION]
```

注意：修改视图是指修改数据库中已存在的表的定义，当基表的某些字段发生改变时，可以通过修改视图来保持视图和基本表之间一致

#### 3、DML操作更新视图

　　因为视图本身没有数据，因此对视图进行的dml操作最终都体现在基表中

```
mysql> create view v_student as select * from student;

mysql> select * from v_student;
+--------+--------+------+
| 学号    | name   | sex  |
+--------+--------+------+
|      1 | 张三    | M    |
|      2 | 李四    | F    |
|      5 | 王五    | NULL |
+--------+--------+------+

mysql> update v_student set name='钱六' where 学号='1';

mysql> select * from student;
+--------+--------+------+
| 学号    | name   | sex  |
+--------+--------+------+
|      1 | 钱六    | M    |
|      2 | 李四    | F    |
|      5 | 王五    | NULL |
+--------+--------+------+
```

当然，视图的DML操作，不是所有的视图都可以做DML操作。

**有下列内容之一，视图不能做DML操作：**

　　①select子句中包含distinct

　　②select子句中包含组函数

　　③select语句中包含group by子句

　　④select语句中包含order by子句

　　⑤select语句中包含union 、union all等集合运算符

　　⑥where子句中包含相关子查询

　　⑦from子句中包含多个表

　　⑧如果视图中有计算列，则不能更新

　　⑨如果基表中有某个具有非空约束的列未出现在视图定义中，则不能做insert操作

#### 4、drop删除视图

　　删除视图是指删除数据库中已存在的视图，删除视图时，只能删除视图的定义，不会删除数据，也就是说不动基表：

```
DROP VIEW [IF EXISTS]   
view_name [, view_name] ...
```

mysql> drop view v_student;

如果视图不存在，则抛出异常；使用IF EXISTS选项使得删除不存在的视图时不抛出异常。

### 四、使用WITH CHECK OPTION约束 

对于可以执行DML操作的视图，定义时可以带上WITH CHECK OPTION约束

作用：

　　对视图所做的DML操作的结果，不能违反视图的WHERE条件的限制。

示例：创建视图，包含1960年之前出生的所有球员（老兵）

```
mysql> create view v_veterans
    -> as
    -> select * from PLAYERS
    -> where birth_date < '1960-01-01'
    -> with check option;
Query OK, 0 rows affected (0.01 sec)

mysql> select * from v_veterans;
+----------+---------+----------+------------+-----+--------+----------------+---------+----------+-----------+------------+----------+
| PLAYERNO | NAME    | INITIALS | BIRTH_DATE | SEX | JOINED | STREET         | HOUSENO | POSTCODE | TOWN      | PHONENO    | LEAGUENO |
+----------+---------+----------+------------+-----+--------+----------------+---------+----------+-----------+------------+----------+
|        2 | Everett | R        | 1948-09-01 | M   |   1975 | Stoney Road    | 43      | 3575NH   | Stratford | 070-237893 | 2411     |
|       39 | Bishop  | D        | 1956-10-29 | M   |   1980 | Eaton Square   | 78      | 9629CD   | Stratford | 070-393435 | NULL     |
|       83 | Hope    | PK       | 1956-11-11 | M   |   1982 | Magdalene Road | 16A     | 1812UP   | Stratford | 070-353548 | 1608     |
+----------+---------+----------+------------+-----+--------+----------------+---------+----------+-----------+------------+----------+
3 rows in set (0.02 sec)
```

此时，使用update对视图进行修改：

```
mysql> update v_veterans
    -> set BIRTH_DATE='1970-09-01'
    -> where PLAYERNO=39;
ERROR 1369 (HY000): CHECK OPTION failed 'TENNIS.v_veterans'
```

因为违反了视图中的WHERE birth_date < '1960-01-01'子句，所以抛出异常；

利用with check option约束限制，保证更新视图是在该视图的权限范围之内。

 

嵌套视图：定义在另一个视图的上面的视图

```
mysql> create view v_ear_veterans
    -> as
    -> select * from v_veterans
　　 -> where JOINED < 1980;
```

使用WITH CHECK OPTION约束时，(不指定选项则默认是CASCADED)

可以使用CASCADED或者 LOCAL选项指定检查的程度：

　　①WITH CASCADED CHECK OPTION：检查所有的视图

　　　　例如：嵌套视图及其底层的视图

　　②WITH LOCAL CHECK OPTION：只检查将要更新的视图本身

　　　　对嵌套视图不检查其底层的视图　

### 五、定义视图时的其他选项

```
CREATE [OR REPLACE]   
　　[ALGORITHM = {UNDEFINED | MERGE | TEMPTABLE}]  
　　[DEFINER = { user | CURRENT_USER }]  
　　[SQL SECURITY { DEFINER | INVOKER }]
VIEW view_name [(column_list)]  
AS select_statement  
　　[WITH [CASCADED | LOCAL] CHECK OPTION]
```

#### 1、ALGORITHM选项：选择在处理定义视图的select语句中使用的方法

　　①UNDEFINED：MySQL将自动选择所要使用的算法

　　②MERGE：将视图的语句与视图定义合并起来，使得视图定义的某一部分取代语句的对应部分

　　③TEMPTABLE：将视图的结果存入临时表，然后使用临时表执行语句

缺省ALGORITHM选项等同于ALGORITHM = UNDEFINED

 

#### 2、DEFINER选项：指出谁是视图的创建者或定义者

　　①definer= '用户名'@'登录主机'

　　②如果不指定该选项，则创建视图的用户就是定义者，指定关键字CURRENT_USER(当前用户)和不指定该选项效果相同

 

#### 3、SQL SECURITY选项：要查询一个视图，首先必须要具有对视图的select权限。

　　但是，如果同一个用户对于视图所访问的表没有select权限，那会怎么样？

SQL SECURITY选项决定执行的结果：

　　①SQL SECURITY DEFINER：定义(创建)视图的用户必须对视图所访问的表具有select权限，也就是说将来其他用户访问表的时候以定义者的身份，此时其他用户并没有访问权限。

　　②SQL SECURITY INVOKER：访问视图的用户必须对视图所访问的表具有select权限。

缺省SQL SECURITY选项等同于SQL SECURITY DEFINER　

视图权限总结：

　　使用root用户定义一个视图(推荐使用第一种)：u1、u2

　　　　1）u1作为定义者定义一个视图，u1对基表有select权限，u2对视图有访问权限：u2是以定义者的身份访问可以查询到基表的内容；

　　　　2）u1作为定义者定义一个视图，u1对基表没有select权限，u2对视图有访问权限，u2对基表有select权限：u2访问视图的时候是以调用者的身份，此时调用者是u2，可以查询到基表的内容。

### 六、视图查询语句的处理

示例：所有有罚款的球员的信息

创建视图：

```
mysql> create view cost_raisers
    -> as
    -> select * from PLAYERS
    -> where playerno in (select playerno from PENALTIES);
```

查询视图：

```
mysql> select playerno from cost_raisers
    -> where town='Stratford';
+----------+
| PLAYERNO |
+----------+
|        6 |
+----------+ 
```

#### 1、替代方法：

　　先把select语句中的视图名使用定义视图的select语句来替代；

　　再处理所得到的select语句。

```
mysql> select playerno from
　　 -> (
    ->   select * from PLAYERS
    ->   where playerno in->   　　(select playerno from PENALTIES)
    -> )as viewformula
    -> where town='Stratford';
+----------+
| PLAYERNO |
+----------+
|        6 |
+----------+
```

#### 2、具体化方法：

　　先处理定义视图的select语句，这会生成一个中间的结果集；

　　然后，再在中间结果上执行select查询。

mysql> select <列名> from <中间结果>;　　　　