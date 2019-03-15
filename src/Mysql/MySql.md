# MySql

## 一、对数据库的操作

### 1.创建数据库：

create  database 库名;

create  database 库名  character set 编码;

![1551865231173](C:\Users\ADMINI~1\AppData\Local\Temp\1551865231173.png)

![1551865500450](C:\Users\ADMINI~1\AppData\Local\Temp\1551865500450.png)

### 2.删除数据库

drop database 数据库名;

### 3.使用库

use 库名；

![1551866974509](C:\Users\ADMINI~1\AppData\Local\Temp\1551866974509.png)

### 4.查看当前正在使用的库

![1551867040568](C:\Users\ADMINI~1\AppData\Local\Temp\1551867040568.png)

## 二、对数据库表的操作

### 1.创建一张表

create table 表名（

​         字段名  类型(长度) [约束]，

​         字段名  类型(长度) [约束]，

​         字段名  类型(长度) [约束]

）；

![1551867152962](C:\Users\ADMINI~1\AppData\Local\Temp\1551867152962.png)

### 2.查看数据库表

创建完成后，我们可以查看数据库表

show tables;

![1551867187785](C:\Users\ADMINI~1\AppData\Local\Temp\1551867187785.png)

查看表的结构

Desc 表名;

![1551867224347](C:\Users\ADMINI~1\AppData\Local\Temp\1551867224347.png)

### 3.删除一张表

drop table 表名；

![1551867279363](C:\Users\ADMINI~1\AppData\Local\Temp\1551867279363.png)

### 4.修改表

#### 4.1 添加一列

alter table 表名 add 字段名  类型(长度)  [约束]

![1551867673362](C:\Users\ADMINI~1\AppData\Local\Temp\1551867673362.png)

#### 4.2修改列的类型(长度、约束)

alter table 表名 modify  要修改的字段名  类型(长度)    [约束]

![1551867826634](C:\Users\ADMINI~1\AppData\Local\Temp\1551867826634.png)



#### 4.3修改列的列名

alter  table 表名 change 旧列名  新列名  类型(长度)    [约束]

![1551920735674](C:\Users\ADMINI~1\AppData\Local\Temp\1551920735674.png)



#### 4.4删除表的列

alter table 表名 drop 列名

![1551920805665](C:\Users\ADMINI~1\AppData\Local\Temp\1551920805665.png)



#### 4.5修改表名

rename table 表名 to 新表名

![1551920877649](C:\Users\ADMINI~1\AppData\Local\Temp\1551920877649.png)



#### 4.6修改表的字符集

alter table 表名 character set 编码



![1551921096950](C:\Users\ADMINI~1\AppData\Local\Temp\1551921096950.png)



查看当前表的编码

show create  table tb_user；

![1551921008785](C:\Users\ADMINI~1\AppData\Local\Temp\1551921008785.png)



### 三、对数据库表记录进行操作(修改)

#### 1.插入记录

insert into 表名(列名1，列名2.列名3...) values(值1，值2，值3....)；

![1551921349589](C:\Users\ADMINI~1\AppData\Local\Temp\1551921349589.png)



insert into 表名 values(值1，值2，值3....);

![1551921424982](C:\Users\ADMINI~1\AppData\Local\Temp\1551921424982.png)



##### 1.1插入中文解决乱码问题解决方法

方式一【不建议】

直接修改数据库的安装目录中的my.ini文件![1551921652526](C:\Users\ADMINI~1\AppData\Local\Temp\1551921652526.png)

方式二

set names gbk；

![1551921853181](C:\Users\ADMINI~1\AppData\Local\Temp\1551921853181.png)



#### 2.修改表记录

##### 2.1不带条件的

update 表名 set 字段名=值,字段名=值,字段名=值,

![1551922023120](C:\Users\ADMINI~1\AppData\Local\Temp\1551922023120.png)

它会将列的所有记录都更改

##### 2.2带条件的

update 表名 set 字段名=值 where 条件；

![1551922299470](C:\Users\ADMINI~1\AppData\Local\Temp\1551922299470.png)



#### 3.删除记录

##### 3.1带条件的

delete from 表名 where 

![1551922433151](C:\Users\ADMINI~1\AppData\Local\Temp\1551922433151.png)

**注意，删除后uid不会重置**



##### 3.2带条件的

先准备数据

insert into tb_user valuses(null,'老王','666');

![1551922547072](C:\Users\ADMINI~1\AppData\Local\Temp\1551922547072.png)



delete from tb_user;

![1551922610963](C:\Users\ADMINI~1\AppData\Local\Temp\1551922610963.png)



##### 3.3面试题

**说说delete与truncate的区别？**

delete删除的时候是一条一条地删除记录，它配合事务，可以将删除的数据找回。

Truncate删除，它是将整个表摧毁，然后再创建一张一模一样的表，它删除的数据无法找回。

演示：

用delete：

![1551922767458](C:\Users\ADMINI~1\AppData\Local\Temp\1551922767458.png)



用truncate

![1551922869011](C:\Users\ADMINI~1\AppData\Local\Temp\1551922869011.png)

**注意，delete删除uid不会重置，truncate删除uid会重置（因为删除了表结构，然后再创建一个一模一样的表，所以再插入时uid会重置）**



#### 4.查询操作

select [distinct]  * 列名，列名 from 表名  [ where 条件];

##### 4.1简单查询

select * from product；

![1551924185754](C:\Users\ADMINI~1\AppData\Local\Temp\1551924185754.png)

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

右外连接：





