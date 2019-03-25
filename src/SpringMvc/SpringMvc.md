# SpringMvc

## 1.SpringMvc框架

###    1.1什么是Springmvc？

springmvc是spring框架的额一个模块，springmvc和spring无需通过中间整合层进行整合。

springmvc是一个基于mvc的web框架。

###      1.2mvc在b/s系统下的应用？

![](http://ww1.sinaimg.cn/large/005WjvZYly1g1dzqbgfelj30vh0fyt9l.jpg)

mvc是一个设计模式。



### 1.3springmvc框架

1. 发起请求到前端控制器(DispatcherServlet)
2. 前端控制器请求HandlerMapping查找Handler，可以根据xml配置、注解进行查找
3. 处理器映射器HandlerMapping向前端控制器返回Handler
4. 前端控制器调用处理器适配器去执行Handler
5. 处理器适配器去执行Handler
6. Handler执行完成给适配器返回ModelAndView
7. 处理器适配器向前端控制器返回ModelAndView，ModelAndView是springmvc框架的一个底层对象，包括Model和View
8. 前端控制器请求视图解析器去进行视图解析，根据逻辑视图名解析成真正的视图(jsp)。
9. 视图解析器向前端控制器返回view
10. 前端控制器进行视图渲染，视图渲染将模型数据（在ModelAndView对象中）填充到request域
11. 前端控制器向用户响应结果。

组件:

1. 前端控制器DispatcherServlet(不需要程序员开发)

   作用接受请求,响应结构,相当于转发器和中央处理器,减少了其他组件之间的耦合度.

2. 处理器你映射器HandlerMapping(不需要程序员开发)

   作用:根据请求的url查找Handler

3. 处理器适配器HandlerAdapter

   作用:按照特定规则(HandlerAdapter要求的规则)去执行Handler

4. 处理器Handler(**需要程序员开发**)

   注意:编写Handler时按照HandlerAdapter的要求去做,这样适配器才可以去正确执行Handler.

5. 视图解析器View resolver(不需要程序员开发)

   作用:进行视图解,根据逻辑视图名解析成真正的视图(View)

6. 视图View(**需要程序员开发JSP**)

   View是一个借口,实现类支持不同的View类型(jsp,freemarker)

   

   ## 2.入门程序

2.1配置前端控制器

```html
<!--springmvc前端控制器-->
<servlet>
    <servlet-name>springmvc</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <load-on-startup>1</load-on-startup>
</servlet>
<!--contextConfigLocation配置springmvc加载的配置文件(配置处理器映射器,适配器等等),
如果不配置contextConfigLocation,默认加载的是/WEB-INF/servlet名称-servlet.xml(springmvc-servlet.xml)-->
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>/WEB-INF/applicationContext.xml</param-value>
</context-param>
<listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
<servlet-mapping>
    <servlet-name>springmvc</servlet-name>
    <!--第一种:*.action,访问以.action借我由DispatcherServlet进行解析
    第二种:/,所有访问的地址都由DispatcherServlet进行解析,对于静态文件的解析需要配置不让DispatcherServlet进行解析
    使用此种方式可以实现RESTful风格的url
    第三种:/*,这样配置不对,使用这种配置,最终要转发到一个jsp页面时,仍然由DispatcherServlet解析jsp,不能根据jsp页面找到handler,会报错-->
    <url-pattern>*.form</url-pattern>
</servlet-mapping>
```



2.2配置处理器适配器

在WEB-INF下的applicationContext.xml中配置适配器

```html
<bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
```

通过查看源代码:

```java
public boolean supports(Object handler) {
    return handler instanceof Controller;
}
```

此适配器可以执行实现Controller接口的Handler

![](http://ww1.sinaimg.cn/large/005WjvZYly1g1f8v7v7yjj314t0frq4t.jpg)



2.3编写Handler

需要实现Controller接口,才能由

```java
org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter
```

来执行

```java
package cn.valvin.ssm.controller;

import cn.valvin.ss.po.Items;
import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.mvc.Controller;

import java.util.ArrayList;
import java.util.List;


public class ItemsController1 implements Controller {
    @Override
    public ModelAndView handleRequest(javax.servlet.http.HttpServletRequest request, javax.servlet.http.HttpServletResponse response) throws Exception {
        //调用service查找数据库,查找商品列表,这里使用静态模拟
        List<Items> itemsList = new ArrayList<>();
        //向list中填充静态数据

        Items items_1 = new Items();
        items_1.setName("联想笔记本");
        items_1.setPrice(6000f);
        items_1.setDetail("Thinkpad T430联想笔记本电脑");
        Items items_2 = new Items();
        items_2.setName("苹果手机");
        items_2.setPrice(5000f);
        items_2.setDetail("iphone6苹果手机");

        itemsList.add(items_1);
        itemsList.add(items_2);

        //返回ModelAndView
        ModelAndView modelAndView = new ModelAndView();
        //相当于request的setAttribut,在jsp页面中通过itellist取数据
        modelAndView.addObject("itemsList", itemsList);

        //指定视图
        modelAndView.setViewName("/WEB-INF/jsp/items/itemsList.jsp");


        return modelAndView;
    }
}
```

2.4视图的编写

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/fmt"  prefix="fmt"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>查询商品列表</title>
</head>
<body> 
<form action="${pageContext.request.contextPath }/item/queryItem.action" method="post">
查询条件：
<table width="100%" border=1>
<tr>
<td><input type="submit" value="查询"/></td>
</tr>
</table>
商品列表：
<table width="100%" border=1>
<tr>
   <td>商品名称</td>
   <td>商品价格</td>
   <td>生产日期</td>
   <td>商品描述</td>
   <td>操作</td>
</tr>
<c:forEach items="${itemsList }" var="item">
<tr>
   <td>${item.name }</td>
   <td>${item.price }</td>
   <td><fmt:formatDate value="${item.createtime}" pattern="yyyy-MM-dd HH:mm:ss"/></td>
   <td>${item.detail }</td>
   
   <td><a href="${pageContext.request.contextPath }/item/editItem.action?id=${item.id}">修改</a></td>

</tr>
</c:forEach>

</table>
</form>
</body>

</html>
```



2.5配置Handler

```html
<!--配置Handler-->
<bean name="/queryItems.action" class="cn.valvin.ssm.controller.ItemsController1"/>
```



2.6配置处理器映射器

在WEB-INF下的applicationContext.xml中配置映射器

```html
 <!--处理器映射器
将bean的那么作为url进行查找,需要在配置Handler时指定beanname(就是url)-->
    <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
```

2.7视图解析器

需要配置解析jsp的视图解析器

```html
<!-- 视图解析器-->
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"/>
```