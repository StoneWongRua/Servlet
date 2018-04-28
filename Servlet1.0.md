##### Servlet
###### 什么是Servlet
- Servlet其实就是一个遵循servlet开发的一个**Java类**。Servlet是由**服务器调用**的，运行在**服务器端**。

- servlet帮助我们完成一些交互功能。

#### HTTP协议
##### 什么是HTTP协议
- 它是TCP/IP协议的一个应用层协议。
- 客户端与服务器交互的一种通讯的格式。

##### 一个例子

1. 点击某个链接。
    
1. 浏览器会向服务器发送**一段文本**，告诉服务器请求打开的是哪一个网页。
    
1. 服务器收到请求，返回**一段文本**给浏览器。
    
2. 浏览器解析**该文本**，并显示出来。

3. 显示该链接指向的网页。

>该文本是遵循HTTP协议规范的



##### HTTP1.0和HTTP1.1的区别
- HTTP1.0
    - 客户端与web服务器建立连接以后，只能获得**一个**web资源。
    - 短连接，获取资源后连接自动断开。

- HTTP1.1
    - 客户端与web服务器建立连接以后，可以在一个连接上获取**多个**web资源。
    - 保持连接。

##### HTTP请求
HTTP**请求**格式如下：

```
＜request-line＞
＜headers＞
＜blank line＞
[＜request-body＞]
```

- 第一行**必须是**一个请求行（request line），用来说明请求类型、要访问的资源以及使用的HTTP版本。

- 紧接着是多个首部（header）小节，用来说明服务器要使用的附加信息。例如描述客户端请求哪台主机，以及客户端的一些环境信息等。

- 在首部之后是一个空行，再此之后可以添加任意的其他数据[称之为主体（body）]。

###### 请求行

> GET/Java.html HTTP/1.1

##### 请求方法

- ==GET==**（URL地址后附带参数有限制）**
    - 向指定的资源发出**显示**请求。使用GET方法应该只用在读取资料，而不应当被用于产生副作用的操作中。

- ==POST==**（传输的数据量无限制）**
    - 向指定资源**提交**数据，请求服务器进行处理（例如提交表单或者上传文件）。数据被包含在请求本文中。這個請求可能会建立新的资源或修改現有资源，或二者皆有。

- HEAD
    - 与GET方法一样，想服务器发出指定资源的请求，只不过服务器不传回资源的文本部分。使用这个方法可以不必传输全部内容，就可以获取其中关于该资源的信息。

- PUT
    - 向指定资源位置上传其最新内容。

- DELETE
    - 请求服务器删除Request-URI所标识的资源。

- TRACE
    - 回显服务器收到的请求，主要用于测试或诊断。

- OPTION
    - 這個方法可使服务器傳回該资源所支持的所有HTTP请求方法。用'*'來代替資源名稱，向Web服务器发送OPTIONS请求，可以测试服务器功能是否正常運作。

- CONNECT
    - HTTP/1.1协议中预留给能够将连接改为管道方式的代理服务器。通常用於SSL加密伺服器的連結（經由非加密的HTTP代理伺服器）。

###### 常用的有：**GET**,**POST**

###### 简单理解：GET用于查询数据，POST用于提交数据

##### HTTP响应
HTTP**响应**格式如下：

```
＜status-line＞
＜headers＞
＜blank line＞
[＜response-body＞]
```
- 首行为状态行，用来描述服务器对请求的处理结果
- 多个信息头（header），用于描述服务器的节本信息，以及数据的描述，服务器通过这些数据的描述信息，可以通知客户端如何处理数据
- 一个空行
- 响应实体：所请求资源的HTML源文件（尽管还可能包含纯文本或其他资源类型的二进制数据）。浏览器将把这些数据显示给用户。

###### 状态行
> HTTP/1.1 200 OK

##### 状态码
>用于表示服务器对请求的处理结果，共有五类

- 1xx == 消息
    - 代表请求已被接受，需要继续处理。
    - **临时**响应。
    - HTTP/1.0协议中没有定义任何1xx状态码。

- 2xx == 成功
    - 代表请求已成功被服务器接收、理解、并接受。

- 3xx == 重定向
    - 代表需要客户端采取进一步的操作才能完成请求。
    - 后续的请求地址（重定向目标）在本次响应的Location域中指明。

- 4xx == 客户端错误
    - 代表了客户端看起来可能发生了错误，妨碍了服务器的处理。
    
- 5xx == 服务器错误
    - 代表了服务器在处理请求的过程中有错误或者异常状态发生。
    - 也有可能是服务器意识到以当前的软硬件资源无法完成对请求的处理。


##### 编写servlet程序
- 编写一个自定义类，实现Servlet接口:

```
package com.tong.test;

import java.io.IOException;

import javax.servlet.Servlet;
import javax.servlet.ServletConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;

public class HelloServlet implements Servlet {

	@Override
	public void destroy() {
		// TODO Auto-generated method stub

	}

	@Override
	public ServletConfig getServletConfig() {
		// TODO Auto-generated method stub
		return null;
	}

	@Override
	public String getServletInfo() {
		// TODO Auto-generated method stub
		return null;
	}

	@Override
	public void init(ServletConfig arg0) throws ServletException {
		// TODO Auto-generated method stub

	}

	@Override
	public void service(ServletRequest arg0, ServletResponse arg1) throws ServletException, IOException {
		// TODO Auto-generated method stub

	}

}

```
- 由上可见，有五个方法需要重写
    - init：初始化Servlet对象
    - destroy：释放Servlet对象占用的资源
    - service：响应客户端的请求
    - ServletConfig：Servlet配置
    - getServletInfo：Servlet信息

- 代码逻辑写到**service**中

###### 一个例子
> 调用Servlet在浏览器输出HelloWorld

1. 在service方法内调用ServletResponse对象

```
	public void service(ServletRequest arg0, ServletResponse arg1) throws ServletException, IOException {
		// TODO Auto-generated method stub
		arg1.getWriter().write("hello,world");
	}
```


2. 配置xml文件

```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://xmlns.jcp.org/xml/ns/javaee" xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd" id="WebApp_ID" version="3.1">
  <display-name>WebTest</display-name>
  <welcome-file-list>
    <welcome-file>index.html</welcome-file>
    <welcome-file>index.htm</welcome-file>
    <welcome-file>index.jsp</welcome-file>
    <welcome-file>default.html</welcome-file>
    <welcome-file>default.htm</welcome-file>
    <welcome-file>default.jsp</welcome-file>
  </welcome-file-list>
  
  <!-- 
	当浏览器输入 http://localhost:8080/HelloServlet 发生了什么？
	
	1. 发现Tomcat中web.xml有配置相应的映射路径
	2. 查看映射 路径的名字是什么
	3. 通过映射路径的名字找到配置Servlet的名字
	4. 通过Servlet的名字找到Servlet编译后class文件存放的位置
	5. 执行HelloServlet
	
-->

  <servlet>
 	<servlet-name>HelloServlet</servlet-name>
 	<servlet-class>com.tong.test.HelloServlet</servlet-class>
 </servlet>
 
 <servlet-mapping>
 	<servlet-name>HelloServlet</servlet-name>
 	<url-pattern>/HelloServlet</url-pattern>
 </servlet-mapping>
</web-app>

 
```
3. ~~成功运行~~  事实上出现了**404**

异常信息如下：
> The origin server did not find a current representation for the target resource or is not willing to disclose that one exists.

研究了一下发现是路径出了问题

直接点击运行的话是请求到的URL是：
> http://localhost:8080/WebTest/WEB-INF/classes/com/tong/test/HelloServlet.java

修改URL为：
> http://localhost:8080/WebTest/HelloServlet

即可运行。

总结：
> 访问的时候，浏览器应该访问的是我配置的路径，而不是直接去找我对应的.java文件.

要访问Servlet的话，需要：
> http:hostname:port/projectName/urlMapping

##### Servlet的生命周期
1. 初始化阶段：
    - 容器**加载Servlet类**，创建对象
    - 调用其init()方法

2. 运行时阶段：
    - 通过service()方法重复接受不同的请求
    - 每次的参数ServletRequest和ServletResponse对象会重新创建和销毁

3. 销毁阶段：
    - 调用所有Servlet对象的destroy()方法
    - 集中销毁上述调用的Servlet对象

> 综上所述，只要访问Servlet，service()就会被调用。init()只有第一次访问Servlet的时候才会被调用。destroy()只有在Tomcat关闭的时候才会被调用

##### Web应用的生命周期
1. 启动阶段：加载Web应用的数据，创建ServletContext对象，对Filter和一些Servlet进行初始化。
2. 运行阶段：所有的Servlet都处于待命状态，随时可以响应客户端的请求。
3. 终止阶段：销毁各种对象和资源。

##### 继承HttpServlet编写Servlet
- 上面实现Servlet接口比较麻烦，所以开发中一般都是**重写**doGEt()和doPOST()








