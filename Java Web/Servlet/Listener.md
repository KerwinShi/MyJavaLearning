## Listener   
监听器是一个专门用于对其他对象身上发生的事件或状态改变进行监听和相应处理的对象，当被监视的对象发生情况时，立即采取相应的行动；这里讲的Listener其实就是是Servlet的监听器，它可以监听客户端请求（Request）、服务端Session、服务端ServletContext生命周期内的变化。    
事件源：即谁产生的事件  
事件对象：即产生了什么事件  
监听器：监听事件源的动作  

按照划分Java Web监听器可分为三类：  
ServletContext监听器：监听ServletContext对象的初始化和销毁  
- ServletContextListener  
用于对ServletContext进行监听（创建、销毁）  
```java
public interface ServletContextListener extends EventListener {
    //监听ServletContext初始化动作
    default void contextInitialized(ServletContextEvent sce) {
    }
    //监听ServletContext销毁动作
    default void contextDestroyed(ServletContextEvent sce) {
    }
}
public class ServletContextEvent extends EventObject {
    private static final long serialVersionUID = 1L;

    public ServletContextEvent(ServletContext source) {
        super(source);
    }
    //获取ServletContext
    public ServletContext getServletContext() {
        return (ServletContext)super.getSource();
    }
}
```
- ServletContextAttributeListener  
用于对ServletContext属性变更的监听（增删改属性）  
```java
public interface ServletContextAttributeListener extends EventListener {
    //监听ServletContext attributes添加kv动作
    default void attributeAdded(ServletContextAttributeEvent scae) {
    }
    //监听ServletContext attributes删除kv动作
    default void attributeRemoved(ServletContextAttributeEvent scae) {
    }
    //ServletContext attributes替换kv动作
    default void attributeReplaced(ServletContextAttributeEvent scae) {
    }
}

public class ServletContextAttributeEvent extends ServletContextEvent {
    private static final long serialVersionUID = 1L;
    private final String name;
    private final Object value;
    public ServletContextAttributeEvent(ServletContext source, String name, Object value) {
        super(source);
        this.name = name;
        this.value = value;
    }
    //获取属性名称
    public String getName() {
        return this.name;
    }
    //属性的值
    public Object getValue() {
        return this.value;
    }
}
```
tomcat服务器的ApplicationContext类
```java
    public void setAttribute(String name, Object value) {
        //name检查
        if (name == null) {
            throw new NullPointerException(sm.getString("applicationContext.setAttribute.namenull"));
        } else if (value == null) {
            //value为null，移除属性
            this.removeAttribute(name);
        } else if (!this.readOnlyAttributes.containsKey(name)) {
            //name对应属性为非只读
            //添加或者替换
            Object oldValue = this.attributes.put(name, value);
            //根据返回值判断是添加还是替换
            boolean replaced = oldValue != null;
            //获取所有的监听器
            Object[] listeners = this.context.getApplicationEventListeners();
            //非空
            if (listeners != null && listeners.length != 0) {
                ServletContextAttributeEvent event = null;
                
                if (replaced) {
                    //替换
                    event = new ServletContextAttributeEvent(this.context.getServletContext(), name, oldValue);
                } else {
                    //添加
                    event = new ServletContextAttributeEvent(this.context.getServletContext(), name, value);
                }

                Object[] var7 = listeners;
                int var8 = listeners.length;
                //遍历监听器
                for(int var9 = 0; var9 < var8; ++var9) {
                    Object obj = var7[var9];
                    //对于ServletContextAttributeListener类型的监听器
                    if (obj instanceof ServletContextAttributeListener) {
                        ServletContextAttributeListener listener = (ServletContextAttributeListener)obj;
                        try {
                            //触发事件
                            if (replaced) {
                                //替换
                                this.context.fireContainerEvent("beforeContextAttributeReplaced", listener);
                                listener.attributeReplaced(event);
                                this.context.fireContainerEvent("afterContextAttributeReplaced", listener);
                            } else {
                                //添加
                                this.context.fireContainerEvent("beforeContextAttributeAdded", listener);
                                listener.attributeAdded(event);
                                this.context.fireContainerEvent("afterContextAttributeAdded", listener);
                            }
                        } catch (Throwable var13) {
                            ExceptionUtils.handleThrowable(var13);
                            if (replaced) {
                                this.context.fireContainerEvent("afterContextAttributeReplaced", listener);
                            } else {
                                this.context.fireContainerEvent("afterContextAttributeAdded", listener);
                            }
                            this.log(sm.getString("applicationContext.attributeEvent"), var13);
                        }
                    }
                }

            }
        }
    }
```
Request监听器：监听Request对象的初始化和销毁以及属性改变  
- ServletRequestListener  
用于对Request请求进行监听（创建、销毁）  
```java
public interface ServletRequestListener extends EventListener {
    //监听ServletRequest初始化动作
    default void requestDestroyed(ServletRequestEvent sre) {
    }
    //监听ServletRequest销毁动作
    default void requestInitialized(ServletRequestEvent sre) {
    }
}
public class ServletRequestEvent extends EventObject {
    private static final long serialVersionUID = 1L;
    private final transient ServletRequest request;
    public ServletRequestEvent(ServletContext sc, ServletRequest request) {
        super(sc);
        this.request = request;
    }
    //从Evenet对象中获取ServletRequest对象
    public ServletRequest getServletRequest() {
        return this.request;
    }
    //从Event对象中获取ServletContext
    public ServletContext getServletContext() {
        return (ServletContext)super.getSource();
    }
}
```
- ServletRequestAttributeListener  
用于对ServletRequest属性添加、删除、修改做监听  
```java
public interface ServletRequestAttributeListener extends EventListener {
    //监听ServletRequest attribute添加动作
    default void attributeAdded(ServletRequestAttributeEvent srae) {
    }
    //监听ServletRequest attribute删除动作
    default void attributeRemoved(ServletRequestAttributeEvent srae) {
    }
    //监听ServletRequest attribute替换动作
    default void attributeReplaced(ServletRequestAttributeEvent srae) {
    }
}
public class ServletRequestAttributeEvent extends ServletRequestEvent {
    private static final long serialVersionUID = 1L;
    private final String name;
    private final Object value;
    public ServletRequestAttributeEvent(ServletContext sc, ServletRequest request, String name, Object value) {
        super(sc, request);
        this.name = name;
        this.value = value;
    }
    //获取ServletRequest attribute名称
    public String getName() {
        return this.name;
    }
    //获取ServletRequest attribute值
    public Object getValue() {
        return this.value;
    }
}
```

Session监听器：监听Session对象的创建和销毁以及属性改变
- HttpSessionListener  
用于对Session的整体状态（创建，销毁）进行监听
```java
public interface HttpSessionListener extends EventListener {
    //监听session的创建动作
    default void sessionCreated(HttpSessionEvent se) {
    }
    //监听session的销毁动作
    default void sessionDestroyed(HttpSessionEvent se) {
    }
}
public class HttpSessionEvent extends EventObject {
    private static final long serialVersionUID = 1L;

    public HttpSessionEvent(HttpSession source) {
        super(source);
    }
    //获取session
    public HttpSession getSession() {
        return (HttpSession)super.getSource();
    }
}
```
-  HttpSessionAttributeListener
用于对session的属性（添加，删除，替换）进行监听  
```java
public interface HttpSessionAttributeListener extends EventListener {
    //监听session attribute添加动作
    default void attributeAdded(HttpSessionBindingEvent se) {
    }
    //监听session attribute删除动作
    default void attributeRemoved(HttpSessionBindingEvent se) {
    }
    //监听session attribute替换动作
    default void attributeReplaced(HttpSessionBindingEvent se) {
    }
}
public class HttpSessionBindingEvent extends HttpSessionEvent {
    private static final long serialVersionUID = 1L;
    private final String name;
    private final Object value;

    public HttpSessionBindingEvent(HttpSession session, String name) {
        super(session);
        this.name = name;
        this.value = null;
    }
    
    public HttpSessionBindingEvent(HttpSession session, String name, Object value) {
        super(session);
        this.name = name;
        this.value = value;
    }
    //获取session
    public HttpSession getSession() {
        return super.getSession();
    }
    //获取session attribute名称
    public String getName() {
        return this.name;
    }
    //获取session attribute值
    public Object getValue() {
        return this.value;
    }
}
```






































