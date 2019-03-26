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

