# Mysql索引

mysql索引分为单列索引(主键索引，唯一索引，普通索引)和组合索引。

单列索引：一个索引只包含一个列，一个表可以有多个单列索引。

组合索引：一个组合索引包含两个或两个以上的列

### 一、索引的创建

1. #### 单列索引

   ##### 1.1 普通索引

   第一种方式：

   ```mysql
   create index indexname on tablename('字段名'(length))
   ```

   第二种方式: 

   ```mysql
     alter table tablename add index indexname('字段名'(length))
   ```

   如果是char，varchar类型，length可以小于字段的实际长度，如果是blob(二进制对象)和text类型就必须指定长度

   ##### 1.2 唯一索引

   与普通索引类似，不同的是唯一索引要求所有的类的值是惟一的，这一点和主键索引一样，但是他允许有空值

   ```mysql
   create unique index indexname on tablename('字段名'(长度))
   ```

   ##### 1.3 主键索引

   不允许有空值(在B+Tree中的InnoDB引擎中，主键索引起到了至关重要的地位)

   主键索引建立的规则是int优于varchar，一般在建表的时候创建，最好是与表的其他字段不相关的列或者是业务不相关的列，一般会设为int而且是auto_increment自增类型的

2. #### 组合索引

   一个表中含有多个单列索引不代表是组合索引，组合索引是：包含多个字段但是只有索引名称。

   ```mysql
   create index nickname_account_createTime_Index on table('nickname','account','create_time');
   ```

   **如果你简历了组合索引(nickname_account_createTime_Index)，那么它实际包含的是3个索引(nickname)、(nickname,account)、(nickname,account,createTime)**

   在使用查询的时候遵循mysql组合索引的“最左前缀”

   1. 不按做阴最左列开始查询(多例查询) 例如index('c1','c2','c3') where 'c2'='aaa' ，不使用索引，where 'c2' and 'c3'='sss'不能使用索引

   2. 查询中某个列有范围查询，则其右边的所有列都无法使用查询(多例查询)Where c1='xxx' and c2 like ='aa%' and c3='sss' 该查询只会使用索引中的前两列，因为like是范围查询

   3. 不能跳过某个字段进行查询，这样利用不到索引，比如我的sql是：

      explain select * from 'award' where nickname>'rSUQFzpk' and account ='DYxJoq' and create_time=20194161522;那么这时候他使用不到其组合索引

      因为我的索引是(nickname,account,create_time)，如果第一个字段出现范围符号的查找，那么将不会用到索引，如果我是第二个或者第三个字段使用范围符号的查找，那么他会利用索引，利用的索引是(nickname)，因为建立组合索引(nickname,account,create_time)会出现三个索引

3. #### 全文索引

   文本字段上(text)如果建立的是普通索引，那么只有对文本的字段内容前面的字符进行索引，其字符大小根据索引建立索引时申明的大小来规定。

   如果文本中出现多个一样的字符，而且需要查找的话，那么其条件只能是

   ```mysql
   where column like '%xxxx%'
   ```

   .这样做会让索引失效，这个时候全文索引就起到了作用

   ```mysql
   alter table tablename add fullText(column1,column2);
   ```

   有了全文索引，就可以用select查询命令去检索那些包含一个或多个给定单词的数据记录了。

   ```mysql
   select * from tablename where match(column1,column2) against('xxx','sss','ddd')
   ```

   这条命令把column1和column2字段里有xxx,sss和ddd的数据记录全部查询出来。

### 二、索引的删除

```mysql
Drop index indexname on tablename
```

### 三、使用索引的优点

1. 可以通过建立唯一索引或者主键索引，保证数据库表中每一行数据的唯一性
2. 建立索引可以大大提高检索的数据，以及减少表的检索行数
3. 在表连接的连接条件，可以加速表与表直接的相连
4. 在分组和排序语句进行数据检索，可以减少查询时间中分组和排序时所消耗的时间(数据库的记录会重新排序)
5. 建立索引，在查询中使用索引，可以提高性能

### 四、使用索引的缺点

1. 在创建索引和维护索引会消耗时间，随着数据量的增加而增加
2. 索引文件会占用物理空间，除了数据表需要占用物理空间之外，每一个索引还会占用一定的物理空间
3. 当对表的数据进行insert，update，delete的时候，索引也要动态维护，这样会降低数据的维护速度，(建立索引会占用磁盘空间的索引文件。一般情况这个问题不太严重，但如果你在一个大表上创建了多种组合索引，索引文件会膨胀很快)

### 五、使用索引需要注意的地方

在建立索引的时候应该考虑索引应该在数据库表中的某些列上面，哪一些索引需要建立，哪一些索引是多余的，一般来说：

1. 在经常需要搜索的列上，可以加快索引的速度

2. 主键列上可以确保列的唯一性

3. 在表与表的连接条件上加上索引，可以加快连接查询的速度

4. 在经常需要排序(order by),分组(group by)和distinct列上加索引，可以加快排序查询的时间。(单独order by用不了索引，索引考虑加where或者加limit)

5. 在一些where之后的< <= > >= between in以及某个情况下的like建立字段的所以(B-Tree)

6. like语句的，如果你对nickename字段建立了一个索引，当查询的时候语句是nickename like '%abc%'，那么这个索引不会起到作用，而nickname licke 'abc%' ，那么将可以用索引

7. 索引不会包含NULL列，如果列中包含null值都将不会被包含在索引中，组合索引中如果有一列含有null值，那么这个组合索引都将失效，一般需要给默认值0或者''字符串。

8. 使用端索引，如果你的一个字段是char(32)或者int(32)，在创建索引的时候指定前缀长度，比如前10个字符(前提是多数值是唯一的)，那么短索引可以提高查询速度，并且可以减少磁盘的控件，也可以减少I/O操作

9. 不要在列上进行运算，这样会使mysql索引失效，也会进行全表扫描

10. 选择越小的数据类型约好，因为通常越小的数据通常在磁盘，内存，CPU缓存中，占用的空间很少，处理起来更快。

    

### 六、什么情况下不创建索引

1. 查询中很少使用的列，不应该创建索引，如果创建了索引还会降低mysql的性能和增大了空间需求
2. 很少数据的列也不应该创建索引，比如：一个性别字段0或者1，结果集中的数据占了表中的数据行的比例较大，mysql需要扫描的行数很多，增加索引，并不能提高效率
3. 定义为text和image和bit类型的列不应该增加索引
4. 当表的修改(update,insert,delete)操作远远大于检索(select)操作时不应该创建索引，这两个操作是互斥关系。