## Filter    
### Filter
- Filter
```java
public interface Filter {
    //容器初始化的时候调用
    public void init(FilterConfig filterConfig) throws ServletException;
    //完成实际的过滤操作，当客户请求访问与过滤器关联的URL的时候，Servlet过滤器将先执行doFilter方法，FilterChain参数用于访问后续过滤器
    public void doFilter(ServletRequest request, ServletResponse response,FilterChain chain) throws IOException, ServletException;
    //容器销毁的时候调用
    public void destroy();
}
```
- FilterConfig  
Servlet 规范将代表 ServletContext对象和Filter的配置参数信息都封装到一个称为FilterConfig的对象中。
```java
public interface FilterConfig {
    public String getFilterName();//获取Filter名称
    public ServletContext getServletContext();//获取ServletContext
    public String getInitParameter(String name);//获取初始化参数
    public Enumeration<String> getInitParameterNames();//获取初始化参数名称
}
```
- FilterChain  
在一个web应用当中，可以开发编写多个Filter，这些Filter组合起来称之为一个Filter链，其中每个过滤器（Filter）都可以决定是否执行下一步。  
```java
public interface FilterChain {
    //表示对Filter过滤器过滤范围下的资源进行放行，让链中下一个对象进行处理
    public void doFilter ( ServletRequest request, ServletResponse response ) throws IOException, ServletException;
}
```


### Filter使用实例  
```java
//实现类
public class MyFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        // 初始化方法，在web应用程序启动时调用一次
    }

    @Override
    public void destroy() {
        // 销毁方法，当web应用移除或服务器停止时才调用一次来卸载Filter对象
    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        // 1. preCode，对Request做前置处理，也可以在这里实现一些Filter过滤器前置逻辑，比如读取Filter配置的init-param信息，存储到成员变量中，实现过滤逻辑
        // ……

        // 2. 当前过滤器放行，继续执行下一个过滤器的过滤逻辑
        filterChain.doFilter(servletRequest, servletResponse);
 
        // 3. postCode，对服务器响应执行后处理
        // ……
    }
}
```
配置 （Filter执行顺序）   
- 基于xml  
```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE web-app
        PUBLIC "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
        "http://java.sun.com/dtd/web-app_2_3.dtd">
 
<web-app>
    <!-- filter配置 -->
    <!-- 使用web.xml配置：根据对应的Mapping的顺序组织 -->
    <filter>
        <filter-name>testFilter</filter-name>
        <filter-class>com..servlet.MyFilter</filter-class>
        <init-param>
            <param-name>cacheTimeout</param-name>
            <param-value>600</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>testFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
    <!-- servlet配置 -->
    <servlet>
        <servlet-name>testServlet</servlet-name>
        <servlet-class>com.servlet.TestServlet</servlet-class>
        <init-param>
            <param-name>myParam</param-name>
            <param-value>paramValue</param-value>
        </init-param>
    </servlet>
    <servlet-mapping>
        <servlet-name>testServlet</servlet-name>
        <url-pattern>*.html</url-pattern>
    </servlet-mapping>
</web-app>

```
- 基于注解  
```java
//基于注解配置（Spring注解）（一般，按照类名的字符串比较规则比较，值小的先执行）
//1.@WebFilter("/myservlet1")//过滤路径,不能设置过滤器之间的优先级
//2.FilterRegistrationBean可以设置过滤器之间的优先级
```
































