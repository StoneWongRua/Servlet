##### 容器：Tomcat![image](https://segmentfault.com/img/bVGISg?w=744&h=443)
- 一个Server可以包含多个Service组件
    - 一个Service里面可以包含多个接受与相应客户请求的Connector组件
    - 一个Service只能包含一个Engine组件
        - 一个Engine组件可以包含多个虚拟主机（host）组件
            - localhost只是默认创建的主机，你还可以创建更多的虚拟主机，并且只要把DNS指向这台机器，就可以访问这台虚拟主机的URL
                - 一个Host组件可以包含多个Context
                    - 一个Context就代表一个Web应用
                
##### Servlet映射
- **一个**Servlet可以被映射到**多个**URL

```
<servlet>
    <servlet-name>demo</servlet-name>
    <servlet-class>com.tong.test.demo</servlet-class>
  </servlet>
  
  <servlet-mapping>
    <servlet-name>demo</servlet-name>
    <url-pattern>/demo</url-pattern>
  </servlet-mapping>
  
<servlet-mapping>
    <servlet-name>demo</servlet-name>
    <url-pattern>/cool</url-pattern>
  </servlet-mapping>
```
这样写的时候，无论我是访问
> http://localhost:8080/WebTest/demo

或者是
> http://localhost:8080/WebTest/cool

都可以成功运行。

###### **debug**

运行的时候出现了
> Server Tomcat v8.5 Server at localhost failed to start.

检查了一下输出的错误发现，因为继承的是**HttpServlet类**，所以生成代码的时候自动添加了注解
```
@WebServlet("/demo")
```
并映射文件。

但是自己又在web.xml中配置了Servlet映射，于是无法启动Tomcat服务器。

以下方法解决：
>1. 去掉自动生成的注解
>2. 保留注解，不添加新的映射

##### Servlet映射的URL可以使用**通配符**
- 通配符
    1. *.扩展名
    2. 正斜杠（/）开头并以“/*”结尾

- **注意**
> 两种通配符同时出现的时候，匹配度高的被优先选择。*.扩展名的优先级最低。

> 优先级：完全匹配>目录匹配>扩展名匹配

##### 单例的Servlet
- Servlet对象一旦创建了，就会驻留在内存中，为后续的请求做服务，直到服务器关闭（Servlet的生命周期了解一下）
- Servlet每次访问请求对象和响应对象都是新的。对于每次访问请求，Servlet引擎都会创建一个新的HttpServletRequest请求对象和一个新的HttpServletResponse响应对象。

##### 缺省Servlet
- 如果某个servlet的映射路径仅仅是/，那么这个servlet就成为当前web应用程序的缺省servlet。
- 凡是在web.xml文件中**找不到匹配的<servlet-mapping>元素的url**，他们的访问请求都将交给缺省的servlet。
- 缺省servlet用于处理所有其他servlet都不处理的访问请求。

在web.xml中，有
```
 <servlet>
    <servlet-name>demo</servlet-name>
    <servlet-class>com.tong.test.demo</servlet-class>
  </servlet>
  
  <servlet-mapping>
    <servlet-name>demo</servlet-name>
    <url-pattern>/demo</url-pattern>
  </servlet-mapping>
  
    <servlet-mapping>
    <servlet-name>demo</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>
```

于是运行demo的时候，显示的是demo这个Servlet上写的内容。但是如果是访问不存在的资源的时候，就会交给缺省Servlet运行。

##### ServletConfig：代表当前Servlet在web.xml中的配置信息（用得不多）
1. 在Servlet的配置文件web.xml中，可以看到使用
        ```
        <init-param>
        ```标签为Servlet配置的初始化参数：

例如：
```
    <servlet>
        <servlet-name>default</servlet-name>
        <servlet-class>org.apache.catalina.servlets.DefaultServlet</servlet-class>
        <init-param>
            <param-name>debug</param-name>
            <param-value>0</param-value>
        </init-param>
        <init-param>
            <param-name>listings</param-name>
            <param-value>false</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
```
2. 通过ServletConfig获取Servlet的初始化参数
- 当servlet配置了初始化参数以后，web容器在创建servlet实例对象时，会自动将这些初始化参数封装到ServletConfig对象中
- 调用
        ```
        init
        ```方法时，会将servletconfig对象传递给servlet
- 我们可以通过servletconfig对象得到当前servlet的初始化参数信息

###### 一个例子

- 首先我的servlet配置如下，其使用中<init-param>标签为servlet配置一些初始化参数：

```
<servlet>
   <servlet-name>demo</servlet-name>
   <servlet-class>com.tong.test.demo</servlet-class> 
   <init-param>
   <param-name>name</param-name>
   <param-value>tong</param-value>
   </init-param>
</servlet>
<servlet-mapping>
    <servlet-name>demo</servlet-name>
    <url-pattern>/demo</url-pattern>
</servlet-mapping>
```
- 然后通过servletconfig获取servlet的初始参数：

```
package com.tong.test;

import java.io.IOException;
import java.util.Enumeration;

import javax.servlet.ServletConfig;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class demo extends HttpServlet {

    public void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        ServletConfig config = this.getServletConfig();
        //获取在web.xml中配置的初始化参数
        String paramVal = config.getInitParameter("name");//获取指定的初始化参数
       // response.getWriter().print(paramVal);
        
       // response.getWriter().print("<hr/>");
        //获取所有的初始化参数
        Enumeration<String> e = config.getInitParameterNames();
        while(e.hasMoreElements()){
            String name = e.nextElement();
            String value = config.getInitParameter(name);
            response.getWriter().print(name + "=" + value);
        }
    }

    public void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        this.doGet(request, response);
    }

}
```
- 运行结果如下：
> name=tong

##### ServletContext对象:代表当前web站点
- 所有的servlet**共享**一个servletcontext对象，servlet之间**都**可以通过servletcontext**实现通讯**以及**数据共享**

    ###### 一个例子:demo1 和 demo2 通过servletcontext对象实现数据共享
    1. 先实现demo1
            
        ```
        package com.tong.test;
        
        import java.io.IOException;
        
        import javax.servlet.ServletContext;
        import javax.servlet.ServletException;
        import javax.servlet.annotation.WebServlet;
        import javax.servlet.http.HttpServlet;
        import javax.servlet.http.HttpServletRequest;
        import javax.servlet.http.HttpServletResponse;
        
        /**
         * Servlet implementation class demo1
         */
        @WebServlet("/demo1")
        public class demo1 extends HttpServlet {
            private static final long serialVersionUID = 1L;
               
            /**
             * @see HttpServlet#HttpServlet()
             */
            public demo1() {
                super();
                // TODO Auto-generated constructor stub
            }
        
            /**
             * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
             */
            protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                // TODO Auto-generated method stub
                String data = "tong_XD";
                ServletContext context = this.getServletConfig().getServletContext();
                context.setAttribute("data", data);
            }
        
            /**
             * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
             */
            protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                // TODO Auto-generated method stub
                doGet(request, response);
            }
        
        }
        
        ```
      2. 再实现demo2
            
            ```
            package com.tong.test;
            
            import java.io.IOException;
            import javax.servlet.ServletException;
            import javax.servlet.annotation.WebServlet;
            import javax.servlet.http.HttpServlet;
            import javax.servlet.http.HttpServletRequest;
            import javax.servlet.http.HttpServletResponse;
            
            import org.apache.catalina.servlet4preview.ServletContext;
            
            /**
             * Servlet implementation class demo2
             */
            @WebServlet("/demo2")
            public class demo2 extends HttpServlet {
                private static final long serialVersionUID = 1L;
                   
                /**
                 * @see HttpServlet#HttpServlet()
                 */
                public demo2() {
                    super();
                    // TODO Auto-generated constructor stub
                }
            
                /**
                 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
                 */
                protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                    // TODO Auto-generated method stub
                    ServletContext context = (ServletContext) this.getServletContext();
                    String data = (String)context.getAttribute("data");
                    response.getWriter().print("data = " + data);
                }
            
                /**
                 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
                 */
                protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                    // TODO Auto-generated method stub
                    doGet(request, response);
                }
            
            }
    3. 运行
        1. 先运行demo1，再运行demo2：
            > 输出 data = tong_XD（符合需求）
        
        2. 先运行demo2，再运行demo1：
            > 输出 data = null （不符合预期）
   
    - **==原理==**:先运行demo1，数据data就会存储到servletcontext对象中，然后运行demo2就可以从该对象中输出数据，从而实现数据共享。
    
    - **==注意==**：要打开浏览器的cookie设置。

- ServeletContext获取配置**整个站点**的参数信息
    ###### 一个例子：获取web应用的初始化参数
    1. 利用<context-param>标签配置web应用的初始化参数：
    
        ```
              <context-param>
                <param-name>name</param-name>
                <param-value>tong</param-value>
            </context-param>
        ```
    2. 获取web应用的初始化参数
    
        ```
        package com.tong.test;
        
        import java.io.IOException;
        
        import javax.servlet.ServletContext;
        import javax.servlet.ServletException;
        import javax.servlet.annotation.WebServlet;
        import javax.servlet.http.HttpServlet;
        import javax.servlet.http.HttpServletRequest;
        import javax.servlet.http.HttpServletResponse;
        
        /**
         * Servlet implementation class demo1
         */
        @WebServlet("/demo3")
        public class demo3 extends HttpServlet {
            private static final long serialVersionUID = 1L;
               
            /**
             * @see HttpServlet#HttpServlet()
             */
            public demo3() {
                super();
                // TODO Auto-generated constructor stub
            }
        
            /**
             * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
             */
            protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                // TODO Auto-generated method stub
                
                ServletContext context = this.getServletContext();
                String value = context.getInitParameter("name");
                response.getWriter().print("name = " + value);
            }
        
            /**
             * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
             */
            protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                // TODO Auto-generated method stub
                doGet(request, response);
            }
        
        }
        
        ```
    > 页面显示 name = tong


- 实现servlet的**转发**
    ###### 一个例子
    demo4如下:
    
    ```
     package com.tong.test;
    
    import java.io.IOException;
    
    import javax.servlet.RequestDispatcher;
    import javax.servlet.ServletContext;
    import javax.servlet.ServletException;
    import javax.servlet.annotation.WebServlet;
    import javax.servlet.http.HttpServlet;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    
    /**
     * Servlet implementation class demo1
     */
    @WebServlet("/demo4")
    public class demo4 extends HttpServlet {
        private static final long serialVersionUID = 1L;
           
        /**
         * @see HttpServlet#HttpServlet()
         */
        public demo4() {
            super();
            // TODO Auto-generated constructor stub
        }
    
        /**
         * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
         */
        protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            // TODO Auto-generated method stub
            String data = "<h1><font color = 'blue'>tong</font></h1>";
            response.getOutputStream().write(data.getBytes());
            ServletContext context = this.getServletContext();
            RequestDispatcher rd = context.getRequestDispatcher("/demo5");
            rd.forward(request, response);
        }
    
        /**
         * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
         */
        protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            // TODO Auto-generated method stub
            //doGet(request, response);
        }
    
    }
    
    ```
    demo5如下：
    
    ```
     package com.tong.test;
    
    import java.io.IOException;
    
    import javax.servlet.RequestDispatcher;
    import javax.servlet.ServletContext;
    import javax.servlet.ServletException;
    import javax.servlet.annotation.WebServlet;
    import javax.servlet.http.HttpServlet;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    
    /**
     * Servlet implementation class demo1
     */
    @WebServlet("/demo5")
    public class demo5 extends HttpServlet {
        private static final long serialVersionUID = 1L;
           
        /**
         * @see HttpServlet#HttpServlet()
         */
        public demo5() {
            super();
            // TODO Auto-generated constructor stub
        }
    
        /**
         * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
         */
        protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            response.getOutputStream().write("demo5".getBytes());
        }
    
        /**
         * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
         */
        protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            // TODO Auto-generated method stub
            this.doGet(request, response);
        }
    
    }
    
    ```
    结果：
    > 显示demo5内容

- 利用ServletContext对象读取**资源文件**
    - 在Servlet的context域中读取文件（相对目录）
    - 非servlet中读取配置文件，要使用类加载器去读取。




