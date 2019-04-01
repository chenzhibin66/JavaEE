# HTTP协议

### 1.什么是Http协议

HTTP，超文本传输协议(HyperText Transfer Protocol)是互联网上应用最广泛的一种网络协议。所有的WWW文件都必须遵守这个标准。设计HTTP最初的目的是为了提供一种发布和接收HTML页面的方法

###  2.Http协议的组成

​        Http协议由http请求和http响应组成，当在浏览器中输入网址访问某个网站时，你的浏览器会将你的请求封装成一个http请求发送给服务器站点，服务器接收到请求后会组织响应数据封装成一个http响应返回给浏览器，即没有请求就没有响应。

### 3.常见状态码：

1xx:通知

200:一切正常

301:永久性重定向    302：暂时重定向

304:拿本地缓存

400 Bad Request  因为错误的语法导致服务器无法理解请求信息。

401 Unauthorized  如果请求需要用户验证。回送应该包含一个WWW-Authenticate头字段用来指明请求资源的权限。

402 Payment Required  保留状态码 

403 Forbidden  服务器接受请求，但是被拒绝处理。 

404 Not Found  服务器没有找到任何匹配Request-URI的资源。 

405 Menthod Not Allowed  Request-Line 请求的方法不被允许通过指定的URI。 

500:服务器端错误

### 4.http与https有什么区别呢？ 

http和https之间的区别就在于其传输的内容是否加密和是否是开发性的内容 .

HTTPS和HTTP的区别：

​      https协议需要到ca申请证书，一般免费证书很少，需要交费。

​      http是超文本传输协议，信息是明文传输，https 则是具有安全性的ssl加密传输协议。

​      http和https使用的是完全不同的连接方式用的端口也不一样，前者是80，后者是443。

​      http的连接很简单，是无状态的。

​      HTTPS协议是由SSL+HTTP协议构建的可进行加密传输、身份认证的网络协议，要比http协议安全。