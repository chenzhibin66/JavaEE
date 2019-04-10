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

**3.mybatis特点**

- 不屏蔽SQL，意味着可以更为精确地定位SQL语句，可以对其进行优化和改造，这有利于互联网系统性能的提高。
- 提供强大、灵活地映射机制，方便java开发者使用。提供动态SQL的功能，允许我们饿根据不同条件组装SQL。
- 在MyBatis中，提供了使用Mapper的接口编程，只要一个接口和一个XML就能创建映射器，进一步简化我们的工作。

4.MyBatis的核心组件

- SqlSessionFactoryBuilder(构造器):它会根据配置或者代码来生成SqlSessionFactoryBuilder,采用的是分布构建的建造者模式。
- SqlSessionFactory(工厂接口)：依靠它来生成SqlSession，使用的是工厂模式。
- SqlSession(会话)：一个既可以发送SQL执行返回结果，也可以获取Mapper的接口。在现有技术中心，一般我们会让其在业务逻辑中"消失"，而使用的是MyBatis提供的SQL Mapper接口编程技术，它能提高代码的可读性和可维护性。
- SQL Mapper(映射器):MyBatis新设计存在的组件，由一个java接口和XML文件(或注解)构成，需要给出对应的SQL和映射规则。它负责发送SQL去执行，并返回结果。

**使用XML构建SqlSessionFactory**

每个基于 MyBatis 的应用都是以一个 SqlSessionFactory 的实例为核心的。SqlSessionFactory 的实例可以通过 SqlSessionFactoryBuilder 获得。而 SqlSessionFactoryBuilder 则可以从 XML 配置文件或一个预先定制的 Configuration 的实例构建出 SqlSessionFactory 的实例。从 XML 文件中构建 SqlSessionFactory 的实例非常简单，建议使用类路径下的资源文件进行配置。 但是也可以使用任意的输入流（InputStream）实例，包括字符串形式的文件路径或者 file:// 的 URL 形式的文件路径来配置。MyBatis 包含一个名叫 Resources 的工具类，它包含一些实用方法，可使从 classpath 或其他位置加载资源文件更加容易。

```java
String resource = "org/mybatis/example/mybatis-config.xml";
InputStream inputStream =Resources.getResourceAsStream(resource);
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
```

XML 配置文件中包含了对 MyBatis 系统的核心设置，包含获取数据库连接实例的数据源（DataSource）和决定事务作用域和控制方式的事务管理器（TransactionManager）。 XML 配置文件的详细内容后面再探讨，这里先给出一个简单的示例：

```html
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
  <environments default="development">
    <environment id="development">
      <transactionManager type="JDBC"/>
      <dataSource type="POOLED">
        <property name="driver" value="${driver}"/>
        <property name="url" value="${url}"/>
        <property name="username" value="${username}"/>
        <property name="password" value="${password}"/>
      </dataSource>
    </environment>
  </environments>
  <mappers>
    <mapper resource="org/mybatis/example/BlogMapper.xml"/>
  </mappers>
</configuration>
```

#### SqlSession

SqlSession是核心接口。在MyBatis中有两个实现类，DefaultSqlSession和SqlSessionManager。DefaultSqlSession是单线程使用的，而SqlSessionManager在多线程使用。

SqlSession作用有3个：

1. 获取Mapper接口
2. 发送SQL给数据库
3. 控制数据库事务

```java
创建SqlSession：

    SqlSession sqlSession = SqlSessionFactory.openSession();

```

注意，SqlSession只是一个门面接口，它有很多方法，可以直接发送SQL。

SqlSession控制数据库事务的方法代码：

