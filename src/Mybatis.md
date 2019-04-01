# Mybatis

**一、基础知识**

1.对原生态jdbc程序(单独使用jdbc开发)问题总结

- 对数据库进行频繁连接开启和关闭,造成数据库资源浪费,影响数据库性能.

  解决方案:用数据库连接池.例如c3p0

- 将sql语句硬编码到java代码中,如果sql语句修改,需要重新编译java代码,不利于系统维护

  设想:将sql语句配置在xml配置文件中.即使sql变化,不需要对java代码进行重新编译

- 想prearedStatement中设置参数,对占位符位置和谁知参数值,硬编码在java代码中,不利于系统维护.

  设想:将sql语句及占位符号和参数全部配置在xml中

- 从result中遍历结果数据时,存在硬编码,将获取表的字段进行硬编码,不利于系统维护

  设想:将查询的结果集,自动映射成java对象.

2.mybatis框架原理

- mybatis是一个持久层框架,是apache下的顶级项目。

- mybatis托管到goolecode下,再后来托管到github下。

- mybatis让程序将主要精力放在sql上,通过mybatis提供的映射方式,自由灵活生成(半自动化,大部分需要程序员编写sql)满足需要sql语句。

- mybatis可以将向prearedStatement中的输入参数自动进行输入映射,将查询结果集灵活地映射出java对象。

mybatis框架

![](http://ww1.sinaimg.cn/large/005WjvZYly1g1jqsqd4dfj30m50nxmzz.jpg)

**3.mybatis入门程序**

根据用户id(主键)查询用户信息

根据用户名称模糊查询

mybatis开发dao两种方法

​          原始dao开发方法(程序需要编写dao接口和dao实现类)

​          mybatis的mapper接口(相当于dao接口)代理开发方法



