## Servlet    
Servlet（Server Applet），全称Java Servlet，是用Java编写的服务器端程序。  
>浏览器-服务器（Tomcat代表）-Servlet之间的工作机制：  
>浏览器发送文本数据到服务器-->服务器解析，生成request对象-->加载对应的Servlet并发送request对象-->Servlet处理request，返回一个response--> 服务器根据response生成对应的文本，发送到浏览器-->浏览器解析文本，展示页面  

### 1.Servlet的核心包  
Servlet约定了Servlet类与Servlet容器之间的规定：Servlet容器将Servlet类载入内存，并产生Servlet实例和调用它具体的方法（一个程序中每种Servlet仅有一个实例）；  
用户请求致使Servlet容器调用Servlet的Service（）方法，并传入一个ServletRequest对象和一个ServletResponse对象；  
每个Servlet对象也都有一个封装Servlet配置的ServletConfig对象；
Servlet容器还会创建一个ServletContext对象，封装上下文（应用程序）的环境详情（每个应用程序只有一个ServletContext）；  
#### javax.servlet  
javax.servlet主要包含的接口有：  
- Servlet：  
实至名归，核心所在，所有Servlet类必须直接或者间接实现  
```java
public interface Servlet {
    //Servlet生命周期的方法--出生
    //Servlet第一次被请求时调用：调用这个方法时，Servlet容器会传入一个ServletConfig对象进来从而对Servlet对象进行初始化
    void init(ServletConfig var1) throws ServletException;
    //返回由Servlet容器传给init()方法的ServletConfig对象
    ServletConfig getServletConfig();
    //Servlet生命周期的方法--工作
    //每当请求Servlet时，Servlet容器就会调用这个方法,第一次请求时，Servlet容器会先调用init( )方法初始化一个Servlet对象出来，然后会调用它的service( )方法进行工作，但在后续的请求中，Servlet容器只会调用service方法了
    //service中使用的编码解码方式默认为：ISO-8859-1编码，中文乱码，解决方法：utf-8编码 --> SO-8859-1解码  --> 乱码  -->  SO-8859-1编码  --> utf-8解码
    void service(ServletRequest var1, ServletResponse var2) throws ServletException, IOException;
    //返回Servlet的一段描述
    String getServletInfo();
    //Servlet生命周期的方法--死亡
    //在卸载应用程序或者关闭Servlet容器，要销毁Servlet时，Servlet容器就会调用这个方法
    void destroy();
}
```
    - GenericServlet（抽象类）
    尽可能使代码简洁的原则，GenericServlet实现了Servlet和ServletConfig接口，避免需要从头实现所有方法，减少工作量。  
```java
//Servlet接口中的所有方法提供了默认的实现，则程序员需要什么就直接改什么，不再需要把所有的方法都自己实现了
//包围ServletConfig对象中的方法
//将init()方法中的ServletConfig参数赋给了一个内部的ServletConfig引用从而来保存ServletConfig对象，不需要程序员自己去维护
public abstract class GenericServlet implements Servlet, ServletConfig, Serializable {
    private static final String LSTRING_FILE = "javax.servlet.LocalStrings";
    private static ResourceBundle lStrings = ResourceBundle.getBundle("javax.servlet.LocalStrings");
    ////将init()方法中的ServletConfig参数赋给了一个内部的ServletConfig引用从而来保存ServletConfig对象，不需要程序员自己去维护
    private transient ServletConfig config;
 
    public GenericServlet() {
    }
 
    public void destroy() {
    }
 
    public String getInitParameter(String name) {
        ServletConfig sc = this.getServletConfig();
        if (sc == null) {
            throw new IllegalStateException(lStrings.getString("err.servlet_config_not_initialized"));
        } else {
            return sc.getInitParameter(name);
        }
    }
 
    public Enumeration<String> getInitParameterNames() {
        ServletConfig sc = this.getServletConfig();
        if (sc == null) {
            throw new IllegalStateException(lStrings.getString("err.servlet_config_not_initialized"));
        } else {
            return sc.getInitParameterNames();
        }
    }
 
    public ServletConfig getServletConfig() {
        return this.config;
    }
 
    public ServletContext getServletContext() {
        ServletConfig sc = this.getServletConfig();
        if (sc == null) {
            throw new IllegalStateException(lStrings.getString("err.servlet_config_not_initialized"));
        } else {
            return sc.getServletContext();
        }
    }
 
    public String getServletInfo() {
        return "";
    }
 
    public void init(ServletConfig config) throws ServletException {
        this.config = config;
        //实际调用的是不带参数的init()方法
        this.init();
    }
    //抽象类是无法直接产生实例的，需要另一个类去继承这个抽象类
    //当程序员如果需要覆盖这个GenericServlet的初始化方法init(ServletConfig config)，则只需要覆盖那个不带参数的init()方法就好了,servletConfig对象仍然由GenericServlet保存着
    public void init() throws ServletException {
    }
 
    public void log(String msg) {
        this.getServletContext().log(this.getServletName() + ": " + msg);
    }
 
    public void log(String message, Throwable t) {
        this.getServletContext().log(this.getServletName() + ": " + message, t);
    }
 
    public abstract void service(ServletRequest var1, ServletResponse var2) throws ServletException, IOException;
 
    public String getServletName() {
        ServletConfig sc = this.getServletConfig();
        if (sc == null) {
            throw new IllegalStateException(lStrings.getString("err.servlet_config_not_initialized"));
        } else {
            return sc.getServletName();
        }
    }
}
```
- ServletRequest  
Servlet容器对于接受到的每一个Http请求，都会创建一个ServletRequest对象，并把这个对象传递给Servlet的Sevice()方法
```java
public interface ServletRequest {
    String getParameter(String var1);//返回请求参数的值
    //还有很多其他的方法
}
```
- ServletResponse  
Servlet响应，在调用Servlet的Service()方法前，Servlet容器会先创建一个ServletResponse对象，并把它作为第二个参数传给Service( )方法，ServletResponse隐藏了向浏览器发送响应的复杂过程。  
```java
public interface ServletResponse {
    ServletOutputStream getOutputStream() throws IOException;//向浏览器发送数据，二进制流对象
}
```
- ServletContext  
ServletContext对象表示Servlet应用程序，每个Web应用程序都只有一个ServletContext对象。  
共享从应用程序中的所有资料处访问到的信息（将对象保存在ServletContext中的一个内部Map中），并且可以动态注册Web对象。（保存在ServletContext中的对象被称作属性）  
```java
public interface ServletContext {
    Object getAttribute(String var1);
    Enumeration<String> getAttributeNames();
    void setAttribute(String var1, Object var2);
    void removeAttribute(String var1);
}

```
- ServletConfig  
当Servlet容器初始化Servlet时，Servlet容器会给Servlet的init( )方式传入一个ServletConfig对象。  
```java
public interface ServletConfig {
    //获取web.xml文件配置的name
    String getServletName();
    //获取ServletContext对象
    ServletContext getServletContext();
    //获取Servlet初始化参数
    String getInitParameter(String var1);
    //获取Servlet初始化参数的name
    Enumeration<String> getInitParameterNames();
}
```
- RequestDispatcher
- Filter  
Filter（过滤器）用于拦截用户请求，在服务器作出响应前，可以在拦截后修改request和response。   
[Filter详情](./Filter.md)  

[Listener详情](./Listener.md)
- ServletContextListener  
- ServletRequestListener  



#### javax.servlet.http  
虽然GenricServlet是对Servlet一个很好的加强，but，HttpServlet才是主角。  
- HttpServlet（抽象类）  
HttpServlet抽象类是继承于GenericServlet抽象类而来的,使用HttpServlet抽象类时，还需要借助分别代表Servlet请求和Servlet响应的HttpServletRequest和HttpServletResponse对象。  
```java
public abstract class HttpServlet extends GenericServlet {
    //HttpServlet抽象类覆盖了GenericServlet抽象类中的Service( )方法，并且添加了一个自己独有的Service(HttpServletRequest request，HttpServletResponse方法。
    public void service(ServletRequest req, ServletResponse res) throws ServletException, IOException {
        //把ServletRequsest类型的对象转换成了HttpServletRequest类型的对象
        //把ServletResponse类型的对象转换成了HttpServletResponse类型的对象
        //在调用Servlet的Service方法时，Servlet容器总会传入一个HttpServletRequest对象和HttpServletResponse对象，预备使用HTTP，所以向下转型不会出错。
        if (req instanceof HttpServletRequest && res instanceof HttpServletResponse) {
            HttpServletRequest request = (HttpServletRequest)req;
            HttpServletResponse response = (HttpServletResponse)res;
            this.service(request, response);
        } else {
            throw new ServletException("non-HTTP request or response");
        }
    }

    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String method = req.getMethod();
        long lastModified;
        //解析HttpServletRequest中的方法参数，并调用以下方法之一：doGet,doPost,doHead,doPut,doTrace,doOptions和doDelete
        //如果我们需要实现具体的服务逻辑，不再需要覆盖service方法了，只需要覆盖doGet或者doPost等方法就好了。
        if (method.equals("GET")) {
            lastModified = this.getLastModified(req);
            if (lastModified == -1L) {
                this.doGet(req, resp);
            } else {
                long ifModifiedSince = req.getDateHeader("If-Modified-Since");
                if (ifModifiedSince < lastModified) {
                    this.maybeSetLastModified(resp, lastModified);
                    this.doGet(req, resp);
                } else {
                    resp.setStatus(304);
                }
            }
        } else if (method.equals("HEAD")) {
            lastModified = this.getLastModified(req);
            this.maybeSetLastModified(resp, lastModified);
            this.doHead(req, resp);
        } else if (method.equals("POST")) {
            this.doPost(req, resp);
        } else if (method.equals("PUT")) {
            this.doPut(req, resp);
        } else if (method.equals("DELETE")) {
            this.doDelete(req, resp);
        } else if (method.equals("OPTIONS")) {
            this.doOptions(req, resp);
        } else if (method.equals("TRACE")) {
            this.doTrace(req, resp);
        } else {
            String errMsg = lStrings.getString("http.method_not_implemented");
            Object[] errArgs = new Object[]{method};
            errMsg = MessageFormat.format(errMsg, errArgs);
            resp.sendError(501, errMsg);
        }
    }

    //doGet
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String protocol = req.getProtocol();
        String msg = lStrings.getString("http.method_get_not_supported");
        if (protocol.endsWith("1.1")) {
            resp.sendError(405, msg);
        } else {
            resp.sendError(400, msg);
        }

    }

    //doPost
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String protocol = req.getProtocol();
        String msg = lStrings.getString("http.method_post_not_supported");
        if (protocol.endsWith("1.1")) {
            resp.sendError(405, msg);
        } else {
            resp.sendError(400, msg);
        }

    }

    //等等http方法

}
```
- HttpServletRequest  
```java
//通过该对象分别获得HTTP请求的请求行，请求头和请求体
public interface HttpServletRequest extends ServletRequest {
    String getContextPath();//返回请求上下文的请求URI部分
    Cookie[] getCookies();//返回一个cookie对象数组
    String getHeader(String var1);//返回指定HTTP标题的值
    String getMethod();//返回生成这个请求HTTP的方法名称
    String getQueryString();//返回请求URL中的查询字符串
    HttpSession getSession();//返回与这个请求相关的会话对象
}

//通过request获取请求行、请求头、请求体
//获得客户端的请求方式：String getMethod()
//获得请求的资源：String getRequestURI()  StringBuffer getRequestURL()  String getContextPath()---web应用的名称  String getQueryString()---get提交url地址后的参数字符串
//获得请求头：long getDateHeader(String name) String getHeader(String name) Enumeration getHeaderNames() Enumeration getHeaders(String name) int getIntHeader(String name)
//获得请求体：String getParameter(String name) String[] getParameterValues(String name) Enumeration getParameterNames() Map<String,String[]> getParameterMap()
```
[HTTP扩展](../HTTP/HTTP.md)


- HttpServletResponse  
```java
//HTTP请求消息分为状态行，响应消息头，响应消息体三部分，因此，在HttpServletResponse接口中定义了向客户端发送响应状态码，响应消息头，响应消息体的方法
public interface HttpServletResponse extends ServletResponse {
    void addCookie(Cookie var1);//给这个响应添加一个cookie
    void addHeader(String var1, String var2);//给这个请求添加一个响应头
    void sendRedirect(String var1) throws IOException;//发送一条响应码，讲浏览器跳转到指定的位置
    void setStatus(int var1);//设置响应行的状态码
}
//ServletResponse里面的
PrintWriter getWriter();//获得字符流，通过字符流的write(String s)方法可以将字符串设置到response   缓冲区中，随后Tomcat会将response缓冲区中的内容组装成Http响应返回给浏览器端。
ServletOutputStream getOutputStream();//获得字节流，通过该字节流的write(byte[] bytes)可以向response缓冲区中写入字节，再由Tomcat服务器将字节内容组成Http响应返回给浏览器。

//乱码
//response缓冲区的默认编码是iso8859-1，此码表中没有中文
response.setCharacterEncoding("utf-8");//更改response的编码方式
//同时，还需要告诉浏览器使用UTF-8编码去解码
response.setHeader("Content-Type","text/html;charset=utf-8");
```

- HttpSession  
- Cookie（类）  
- HttpSessionListener[Listener详情](./Listener.md)

#### javax.servlet.annotation  



#### javax.servlet.descriptor  



### 2.Servlet的使用  
#### 没有框架的时代
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xmlns="http://java.sun.com/xml/ns/j2ee" xmlns:javaee="http://java.sun.com/xml/ns/javaee"
 xmlns:web="http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
 xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd"
 version="2.4">
    <display-name>Lonely Wolf Web Application</display-name>
    <servlet>
        <servlet-name>helloServlet</servlet-name>
        <!-- 定位servlet -->
        <servlet-class>com.lonely.wolf.mini.spring.servlet.HelloServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>helloServlet</servlet-name>
        <!-- 映射路径 -->
        <url-pattern>/hello/*</url-pattern>
    </servlet-mapping>
</web-app>
```
```java
public class HelloServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request,response);
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html;charset=utf-8");
        response.getWriter().write("Hello：" + request.getParameter("name"));
    }
}
```
#### Spring框架的时代  
```java
//接口类
@RestController
@RequestMapping("/hello")
public class HelloController {
    @GetMapping("/demo")
    public String helloWorld(String name){
        return "Hello：" + name;
    }
}

//启动类
@SpringBootApplication(scanBasePackages = "com.lonely.wolf.note.springboot")
class MySpringBootApplication {
    public static void main(String[] args) {
        SpringApplication.run(MySpringBootApplication.class, args);
    }
}
```


#### 框架的实现思路  












参考万网站：  
1. https://blog.csdn.net/qq_19782019/article/details/80292110  
2. https://mp.weixin.qq.com/s/iHumipT1DagVurCgXIEw6w
































